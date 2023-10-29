# scheduler调度

runtimecore包中的scheduler是我们这一章主要研究的点，在这个包中不仅有我们熟悉的api比如nextTick，还有维护任务队列的核心逻辑，我们在这一章不仅可以学习到以上的实现细节，还能解开一些疑惑，比如：“为什么父组件要先于子组件更新”

我们简单先重温一下nextTick

> 将回调推迟到下一个 DOM 更新周期之后执行。在一些应用场景中，比如在更改了一些数据，开发者需要获取到最新的dom，就可以使用这个API
> 

```tsx
import { createApp, nextTick } from 'vue'

const app = createApp({
  setup() {
    const message = ref('Hello!')
    const changeMessage = async newMessage => {
      message.value = newMessage
      await nextTick()
      console.log('Now DOM is updated')
    }
  }
})
```

这个函数作为vue开发者，你肯定用过，而且它和js的执行机制息息相关，理解起来也非常简单，我们直接看一下源码:

```tsx
// runtime-core/scheduler.ts
export function nextTick<T = void>(
  this: T,
  fn?: (this: T) => void
): Promise<void> {
	// 这一行与源码有一点出入，源码是 = currentFlushPromise || resolvedPromise
	// 为了demo能够更好理解所以写成下面的样子
  const p = Promise.resolve()
  return fn ? p.then(this ? fn.bind(this) : fn) : p
}
```

这段代码很容易理解，nextTick的用法我们在demo里面也可以看到，可以不用传递其中的回调函数。但是如果我们传递了回调函数，我们就可以执行p.then，p.then的回调就传入了fn，如果没传递回调函数，就把内部的p变量返回。

Vue的DOM更新是有自己的策略的，反正肯定的是，不是同步更新。我们可能会有疑惑Vue是怎么保证”DOM更新任务“执行的顺序呢，那么答案就在scheduler.ts调度核心中，我们刚刚看的nextTick就在scheduler定义，我们看看其他的函数主要做了哪些事情。

queueJob主要维护了任务队列: 

```tsx
export function queueJob(job: SchedulerJob) {
  // the dedupe search uses the startIndex argument of Array.includes()
  // by default the search index includes the current job that is being run
  // so it cannot recursively trigger itself again.
  // if the job is a watch() callback, the search will start with a +1 index to
  // allow it recursively trigger itself - it is the user's responsibility to
  // ensure it doesn't end up in an infinite loop.
	// 如果队列为空或者队列中不包含此job或者当前job不是正在运行的job
  if (
    (!queue.length ||
      !queue.includes(
        job,
        isFlushing && job.allowRecurse ? flushIndex + 1 : flushIndex
      )) &&
    job !== currentPreFlushParentJob
  ) {
		// job id 如果是null，就把job推送到队列中（证明是一个新job）
    if (job.id == null) {
      queue.push(job)
    } else {
			// 如果不为null，就替换原有的
      queue.splice(findInsertionIndex(job.id), 0, job)
    }
		// 调用队列刷新
    queueFlush()
  }
}
```

这里的job是什么意思？我们可以继续追溯，看看哪个地方调用了这个函数

我们可以看到在renderer这个包中调用了这个函数，核心代码如下：

```tsx
const effect = new ReactiveEffect(
  componentUpdateFn,
  () => queueJob(instance.update),
  instance.scope // track it in component's effect scope
)

const update = (instance.update = effect.run.bind(effect) as SchedulerJob)
```

我们可以在其他章节阅读这部分相关内容，在这里我们只需要知道是做update调用的

可以看出每一个job进入queueJob函数的时候，都会经过搜索🔍  ，这里搜索使用的是includes函数，配合includes函数的第二个参数fromIndex，你可以在这个链接了解includes函数

[Array.prototype.includes() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)

代码中的job的allowRecurse字段是什么意思？我们在vue3中肯定写过这样的代码

```tsx
watch(() => count.value, (value) => {
	console.log(value)
})
```

像这样的回调（job），是用户允许的递归行为，所以在函数的第一个if中，会有这样的判断

```tsx
job.allowRecurse ? flushIndex + 1 : flushIndex
```

将搜索的索引往后挪一位，将不检查老的job，新的job就可以加入进来了

所以这个函数最终的目标就是：将蜂拥而上的job进行过滤，过滤的条件是以下几个:

1. 非当前运行的job（如果不过滤，将会造成递归）

```tsx
job !== currentPreFlushParentJob
```

1. 过滤用户没有明确定义允许递归的行为（比如非watch....）

细心的你会发现一个设计小细节，在多个job进入之后，vue并没有立即去执行，而是去调用另外一个刷新函数queueFlush，这样就避免了，频繁调用导致的多次执行

```tsx
function queueFlush() {
  if (!isFlushing && !isFlushPending) {
    isFlushPending = true
    currentFlushPromise = resolvedPromise.then(flushJobs)
  }
}
```

这里就很简单，调用刷新的作用就是，给isFlushPending标识为true，resolvedPromise这个方法返回的是currentFlushPromise，顾名思义，这个就是当前执行job的promise，所以我们就能连通了，我们最开始了解的nextTick的p变量，大家还记得么，在源码就是这样实现的；

```tsx
const p = currentFlushPromise || resolvedPromise
```

现在我们就能大彻大悟，原来currentFlushPromise是这样被赋值的，我们调用nextTick意思是也就是等待currentFlushPromise执行完毕。不着急，我们主要看看flushJobs这个函数做了什么，为什么nextTick的实际作用就是等这个函数执行完。

```tsx
function flushJobs(seen?: CountMap) {
  isFlushPending = false
  isFlushing = true
  if (__DEV__) {
    seen = seen || new Map()
  }

  flushPreFlushCbs(seen)

  // Sort queue before flush.
  // This ensures that:
  // 1. Components are updated from parent to child. (because parent is always
  //    created before the child so its render effect will have smaller
  //    priority number)
  // 2. If a component is unmounted during a parent component's update,
  //    its update can be skipped.
  queue.sort((a, b) => getId(a) - getId(b))

  // conditional usage of checkRecursiveUpdate must be determined out of
  // try ... catch block since Rollup by default de-optimizes treeshaking
  // inside try-catch. This can leave all warning code unshaked. Although
  // they would get eventually shaken by a minifier like terser, some minifiers
  // would fail to do that (e.g. https://github.com/evanw/esbuild/issues/1610)
  const check = __DEV__
    ? (job: SchedulerJob) => checkRecursiveUpdates(seen!, job)
    : NOOP

  try {
    for (flushIndex = 0; flushIndex < queue.length; flushIndex++) {
      const job = queue[flushIndex]
      if (job && job.active !== false) {
        if (__DEV__ && check(job)) {
          continue
        }
        // console.log(`running:`, job.id)
        callWithErrorHandling(job, null, ErrorCodes.SCHEDULER)
      }
    }
  } finally {
    flushIndex = 0
    queue.length = 0

    flushPostFlushCbs(seen)

    isFlushing = false
    currentFlushPromise = null
    // some postFlushCb queued jobs!
    // keep flushing until it drains.
    if (
      queue.length ||
      pendingPreFlushCbs.length ||
      pendingPostFlushCbs.length
    ) {
      flushJobs(seen)
    }
  }
}
```

上面的实现中，可以看到第一步去排序了队列：

```tsx
// 需要给队列排序，为啥要排序呢，依次更新不行么？
// 1. 因为父组件是在子组件之前创建好的，所以在渲染层面来说，优先级要略高一点
// ps: 为什么父组件优先于子组件进行创建，因为我们在[组件]这一部分已经完整的看到了，创建组件的实例是怎样的（uid初始是为0）；https://www.notion.so/shenhao/Vue3-cb2cc7f8880249adb9a12fd2176a8065#437828200ff6461981d42cd13bbe9077
// 2. 按照父->子这样的顺序更新，如果父组件在更新的时候，子组件是unmounted状态，则可以跳过这次更新
queue.sort((a, b) => getId(a) - getId(b))
```

try catch代码块中的部分注释我们不解析，因为我看不懂😭 ，这稍微偏离了我们研究的内容，如果你感兴趣，可以到这个issue里去看看，如果我学到了这部分，我会补充这部分的解析。

[minify: provide an option to enable treeshaking in single-file transform · Issue #1610 · evanw/esbuild](https://github.com/evanw/esbuild/issues/1610)

我们直接看try中的callWithErrorHandling函数，这个函数就是一个带容错处理的函数执行，我们以后会遇到多次，所以之后就不解释这个函数做什么了。

queueCb函数和queueJob很像，它是处理回调函数任务的

```tsx
function queueCb(
  cb: SchedulerJobs,
  activeQueue: SchedulerJob[] | null,
  pendingQueue: SchedulerJob[],
  index: number
) {
  if (!isArray(cb)) {
    if (
      !activeQueue ||
      !activeQueue.includes(cb, cb.allowRecurse ? index + 1 : index)
    ) {
      pendingQueue.push(cb)
    }
  } else {
    // if cb is an array, it is a component lifecycle hook which can only be
    // triggered by a job, which is already deduped in the main queue, so
    // we can skip duplicate check here to improve perf
    pendingQueue.push(...cb)
  }
  queueFlush()
}
```

搜索方式和queueJob一样，只不过cb参数如果是数组，如果是数组就代表了是组件的生命周期hook，比如这样

```tsx
import { onMounted, onUpdated, onUnmounted } from 'vue'

const MyComponent = {
  setup() {
    onMounted(() => {
      console.log('mounted!')
    })
    onUpdated(() => {
      console.log('updated!')
    })
    onUnmounted(() => {
      console.log('unmounted!')
    })
  }
}
```

但是在这个函数中为了性能，所以这样处理

```tsx
pendingQueue.push(...cb)
```

意思就是，这样的生命周期回调，不在这里进行处理，最后会在主队列进行重复调用的处理

那我们在补充一点，我从参考资料获取的一些信息，就是我们刚刚聊到的cb这个队列的处理，在源码深入之后，会发现部分hook会直接走渲染，部分hook会在update之后调用这个方法，那么有这种区别的原因就是，有些hook是before***，有些是***ed。

分析完毕，我们来总结一下，在这一章我们需要达成的目标有哪些：

1. 核心的flushJobs主要做了什么
2. 队列为什么要进行排序
3. nexttick原理
4. 队列是如何去重复的，去重复的逻辑是什么

解答：

核心的flushjobs主要是一个递归，在函数中首先进行了排序，然后去执行job，如果队列中还有job将会再次调用自身；

那么排序的原因则是，父组件是优先于子组件进行创建的，在更新优先级上要高于子组件而且当父组件在更新的时候，子组件如果是未挂载的情况下，那么也可以很顺利的跳过更新环节。

在flushjob之前会有一个前置处理，就是queueJob，在这个逻辑中仅仅执行了nextTick的核心逻辑以及搜索算法，搜索主要用的是includes，利用了第二个参数fromindex，但是对于watch()这样的api，不会过滤掉，因为这是用户允许递归行为，除了这一类行为之外 当前正在更新的job和此job是一样的话，这样子也不会再次更新。这也就是队列去重复的核心逻辑

关于nextTick原理，也就是等待当前正在更新的job任务（promise）回调。由于js执行机制，当我们更改一个值，视图不会变化更新，也就是这个原因，vue为了性能，把任务都放在了微任务中处理，我们如果要在业务代码中拿到修改之后的值，就要使用nextTick。

参考资料:

[【vue3 宇宙】nextTick - 掘金](https://juejin.cn/post/6850418106419511310#heading-1)

[nextTick | Vue3](https://vue3js.cn/global/nextTick.html)