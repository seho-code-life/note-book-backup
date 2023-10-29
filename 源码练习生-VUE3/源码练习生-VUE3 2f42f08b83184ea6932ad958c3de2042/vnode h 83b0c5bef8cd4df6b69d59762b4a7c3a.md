# vnode / h

> h代表的是hyperscript，缩写为h是因为能够更简单的输入。它是html的一部分，在处理脚本的时候，虚拟dom节点经常去使用它进行替换（指的是自定义）。h虽然代码量很小，仅仅是校验类型，底层还是调用vnode实现，所以h只是给开发者自己定义渲染内容的便利方法。所以我选择把h.ts和vnode.ts的实现一次性分析
> 

我们在vue2，可能会写很多这样的代码，用于自己构建视图

```tsx
new Vue({
 render: h => h(App)
}).$mount('#app')
```

完全可以把这段代码理解为这个意思

```tsx
render: function(createElement){
	return createElement(App);
}
// 使用es6就是demo的样子
```

有一个库的名字和hyperscript一样，但是我们在这种场景下谈论hyperscript，并不是指这个库的实现。h在vue3的定义中就是比createVNode更友好的函数，它同样返回一个Vnode（虚拟节点）。

Vnode是一个普通对象，向vue描述了呈现节点的信息（包括子节点）

我们可以在vue中使用h函数编写自定义render

```tsx
// runtime-core/h.ts

/*
// type only
h('div')

// type + props
h('div', {})

// type + omit props + children
// Omit props does NOT support named slots
h('div', []) // array
h('div', 'foo') // text
h('div', h('br')) // vnode
h(Component, () => {}) // default slot

// type + props + children
h('div', {}, []) // array
h('div', {}, 'foo') // text
h('div', {}, h('br')) // vnode
h(Component, {}, () => {}) // default slot
h(Component, {}, {}) // named slots

// named slots without props requires explicit `null` to avoid ambiguity
h(Component, null, {})
**/
```

例子（会渲染出hello world）

```tsx
const App = {
    render() {
      return Vue.h('h1', {}, 'Hello world')
    }
}
Vue.createApp(App).mount('#app')
```

看一下h函数的具体实现

```tsx
export function h(type: any, propsOrChildren?: any, children?: any): VNode {
  const l = arguments.length
  if (l === 2) {
    if (isObject(propsOrChildren) && !isArray(propsOrChildren)) {
      // single vnode without props
      if (isVNode(propsOrChildren)) {
        return createVNode(type, null, [propsOrChildren])
      }
      // props without children
      return createVNode(type, propsOrChildren)
    } else {
      // omit props
      return createVNode(type, null, propsOrChildren)
    }
  } else {
    if (l > 3) {
      children = Array.prototype.slice.call(arguments, 2)
    } else if (l === 3 && isVNode(children)) {
      children = [children]
    }
    return createVNode(type, propsOrChildren, children)
  }
}
```

函数接受3个参数:  

1. type(元素类型) 
2. *propsOrChildren (比如style,class,props,attr等等)*
3. 子节点

可以看到h函数只是做了参数的校验，根据参数的类型去调用createVNode函数。

我们具体看一下createVNode实现逻辑

```tsx
// runtime-core/vnode.ts

function _createVNode(
  type: VNodeTypes | ClassComponent | typeof NULL_DYNAMIC_COMPONENT,
  props: (Data & VNodeProps) | null = null,
  children: unknown = null,
  patchFlag: number = 0,
  dynamicProps: string[] | null = null,
  isBlockNode = false
): VNode {
	// 判断type是否合法，如果不合法且是在开发环境就警告
	// 如果非开发环境type不合法，就把Comment（上下文分析为undefined）赋值给type
  if (!type || type === NULL_DYNAMIC_COMPONENT) {
    if (__DEV__ && !type) {
      warn(`Invalid vnode type when creating vnode: ${type}.`)
    }
    type = Comment
  }
	
	// 判断type是否本身为vnode
  if (isVNode(type)) {
		// 在传递一个vnode给组件时，会进入这个判断，下面的注释也说明了
    // createVNode receiving an existing vnode. This happens in cases like
    // <component :is="vnode"/>
    // #2078 make sure to merge refs during the clone instead of overwriting it
		// 克隆vnode节点
		const cloned = cloneVNode(type, props, true /* mergeRef: true */)
    if (children) {
			// 存在子节点，调用 普通化子节点的函数（也可以理解为对子节点的兼容处理）
			// 这个函数里通过判断子节点的类型，对vnode进行一些处理，比如赋值children以及修改ShapeFlags
      normalizeChildren(cloned, children)
    }
    return cloned
  }
	
	// 判断是否是类组件（class类型的type标准化）
  // class component normalization.
  if (isClassComponent(type)) {
    type = type.__vccOpts
  }
	
	// 判断兼容，2.0版本的异步组件需要被转换
  // 2.x async/functional component compat
  if (__COMPAT__) {
    type = convertLegacyComponent(type, currentRenderingInstance)
  }

  // class & style normalization.
  if (props) {
    // for reactive or proxy objects, we need to clone it to enable mutation.
		// 如果props是响应式的，需要克隆一份
    props = guardReactiveProps(props)!
		// 从props中解构class和style
    let { class: klass, style } = props
    if (klass && !isString(klass)) {
			// 对于class来说，如果不是string，则就要调用方法去规范并且返回给props.cl
      props.class = normalizeClass(klass)
    }
		// 同样如果style是对象，判断是否响应式，如果是响应式就拷贝一份
    if (isObject(style)) {
      // reactive state objects need to be cloned since they are likely to be
      // mutated
      if (isProxy(style) && !isArray(style)) {
        style = extend({}, style)
      }
      props.style = normalizeStyle(style)
    }
  }

  // encode the vnode type information into a bitmap
	// 可以看出调用这个底层方法的type五花八门，有字符串的，有对象的（本身vnode），也有各种自有组件的类型
	// 但是shapeFlag在这里仅仅是做了一层处理，具体的逻辑会在渲染层做，vnode不会做
  const shapeFlag = isString(type)
    ? ShapeFlags.ELEMENT
    : __FEATURE_SUSPENSE__ && isSuspense(type)
    ? ShapeFlags.SUSPENSE
    : isTeleport(type)
    ? ShapeFlags.TELEPORT
    : isObject(type)
    ? ShapeFlags.STATEFUL_COMPONENT
    : isFunction(type)
    ? ShapeFlags.FUNCTIONAL_COMPONENT
    : 0

  if (__DEV__ && shapeFlag & ShapeFlags.STATEFUL_COMPONENT && isProxy(type)) {
    type = toRaw(type)
    warn(
      `Vue received a Component which was made a reactive object. This can ` +
        `lead to unnecessary performance overhead, and should be avoided by ` +
        `marking the component with \`markRaw\` or using \`shallowRef\` ` +
        `instead of \`ref\`.`,
      `\nComponent that was made reactive: `,
      type
    )
  }

  return createBaseVNode(
    type,
    props,
    children,
    patchFlag,
    dynamicProps,
    shapeFlag,
    isBlockNode,
    true
  )
}
```

在创建_createVNode逻辑中，有几个函数也值得我们注意，分别是:

1. cloneVNode

我们可以看到在判断type是vnode的时候，类似于下面这段代码

```tsx
<component :is="vnode"/>
```

为什么要clone ?

```tsx
// 克隆vnode节点
const cloned = cloneVNode(type, props, true /* mergeRef: true */)
```

我们先看看cloneVNode的实现，其实就是重新构建了一下对象并且返回

```tsx
// cloneVNode
const { props, ref, patchFlag, children } = vnode
const mergedProps = extraProps ? mergeProps(props || {}, extraProps) : props
const cloned: VNode = {
  __v_isVNode: true,
  __v_skip: true,
	...
}
```

> 这是为了我们在写<component :is="vnode"/>这样的代码的时候vue内部不会改变现有的vnode，而会新建一个新的vnode
> 

_createVNode函数在末尾调用了createBaseVNode这个函数，并且传入了所有的参数，我们稍等看一下createBaseVNode的实现，在此之前我们看看vnode源码中，还有哪些函数是调用createBaseVNode这个底层方法的，我们简单了解一下：

```tsx
// 创建文本的vnode节点
export function createTextVNode(text: string = ' ', flag: number = 0): VNode {
  return createVNode(Text, null, text, flag)
}

// 创建静态节点，代码中没有动态属性的节点
export function createStaticVNode(
  content: string,
  numberOfNodes: number
): VNode {
  // A static vnode can contain multiple stringified elements, and the number
  // of elements is necessary for hydration.
  const vnode = createVNode(Static, null, content)
  vnode.staticCount = numberOfNodes
  return vnode
}

// 创建注释vnode节点
// v-if/v-else的node节点就是由这个函数创建的，通过asBlock
export function createCommentVNode(
  text: string = '',
  // when used as the v-else branch, the comment node must be created as a
  // block to ensure correct updates.
  asBlock: boolean = false
): VNode {
  return asBlock
    ? (openBlock(), createBlock(Comment, null, text))
    : createVNode(Comment, null, text)
}

// 格式化vnode
// 我们可以根据传入的child，来返回一个vnode类型格式，传递的child可以是boolean，可以是单独的内容，也可以是vnode节点
// 比如这样（下面是这个函数的测试用例，也在源码中）
// expect(normalizeVNode(null)).toMatchObject({ type: Comment })
// expect(normalizeVNode(undefined)).toMatchObject({ type: Comment })
// expect(normalizeVNode(['foo'])).toMatchObject({ type: Fragment })
// expect(normalizeVNode(vnode)).toBe(vnode)
export function normalizeVNode(child: VNodeChild): VNode {
  if (child == null || typeof child === 'boolean') {
    // empty placeholder
    return createVNode(Comment)
  } else if (isArray(child)) {
    // fragment
    return createVNode(
      Fragment,
      null,
      // #3666, avoid reference pollution when reusing vnode
      child.slice()
    )
  } else if (typeof child === 'object') {
    // already vnode, this should be the most common since compiled templates
    // always produce all-vnode children arrays
    return cloneIfMounted(child)
  } else {
    // strings and numbers
    return createVNode(Text, null, String(child))
  }
}
```

ok了解完毕，我们现在来正式看看vnode最最最底层的方法createBaseVNode

```tsx
function createBaseVNode(
  type: VNodeTypes | ClassComponent | typeof NULL_DYNAMIC_COMPONENT,
  props: (Data & VNodeProps) | null = null,
  children: unknown = null,
  patchFlag = 0,
  dynamicProps: string[] | null = null,
  shapeFlag = type === Fragment ? 0 : ShapeFlags.ELEMENT,
  isBlockNode = false,
  needFullChildrenNormalization = false
) {
	// 构建vnode对象
  const vnode = {
    __v_isVNode: true,
    __v_skip: true,
    type,
    props,
    key: props && normalizeKey(props),
    ref: props && normalizeRef(props),
    scopeId: currentScopeId,
    slotScopeIds: null,
    children,
    component: null,
    suspense: null,
    ssContent: null,
    ssFallback: null,
    dirs: null,
    transition: null,
    el: null,
    anchor: null,
    target: null,
    targetAnchor: null,
    staticCount: 0,
    shapeFlag,
    patchFlag,
    dynamicProps,
    dynamicChildren: null,
    appContext: null
  } as VNode

  if (needFullChildrenNormalization) {
    normalizeChildren(vnode, children)
    // normalize suspense children
    if (__FEATURE_SUSPENSE__ && shapeFlag & ShapeFlags.SUSPENSE) {
      ;(type as typeof SuspenseImpl).normalize(vnode)
    }
  } else if (children) {
    // compiled element vnode - if children is passed, only possible types are
    // string or Array.
    vnode.shapeFlag |= isString(children)
      ? ShapeFlags.TEXT_CHILDREN
      : ShapeFlags.ARRAY_CHILDREN
  }

  // validate key
	// 校验key如果是nan（NaN !== NaN）就警告
  if (__DEV__ && vnode.key !== vnode.key) {
    warn(`VNode created with invalid key (NaN). VNode type:`, vnode.type)
  }

  // track vnode for block tree
	// patchFlag是更新标识，节点中将会从始至终存在这个标识，因为就算不更新节点，节点也需要持久化到栈中，以便之后更好的卸载
  if (
    isBlockTreeEnabled > 0 &&
    // avoid a block node from tracking itself
    !isBlockNode &&
    // has current parent block
    currentBlock &&
    // presence of a patch flag indicates this node needs patching on updates.
    // component nodes also should always be patched, because even if the
    // component doesn't need to update, it needs to persist the instance on to
    // the next vnode so that it can be properly unmounted later.
    (vnode.patchFlag > 0 || shapeFlag & ShapeFlags.COMPONENT) &&
    // the EVENTS flag is only for hydration and if it is the only flag, the
    // vnode should not be considered dynamic due to handler caching.
    vnode.patchFlag !== PatchFlags.HYDRATE_EVENTS
  ) {
    currentBlock.push(vnode)
  }
	
	// 兼容2.x
  if (__COMPAT__) {
    convertLegacyVModelProps(vnode)
    convertLegacyRefInFor(vnode)
    defineLegacyVNodeProperties(vnode)
  }

  return vnode
}
```

我们这一篇，大概的讲述了h函数，我们知道了h函数的具体定义（一个createVNode函数的封装函数），h函数主要做了参数校验功能，主要的创建vnode还是createVNode函数去做的。我们了解到createVNode函数主要做了以下几个事情:

1. type的校验
2. 标准化了class，style，props
3. 给type打各种标识
4. 核心createBaseVNode函数创建了vnode
5. 标准化了子节点
6. 还有对2.x的兼容处理

参考

[h() | Vue3](https://vue3js.cn/global/h.html)

[Vue3源码阅读笔记之vnode定义](https://cloud.tencent.com/developer/article/1812838)