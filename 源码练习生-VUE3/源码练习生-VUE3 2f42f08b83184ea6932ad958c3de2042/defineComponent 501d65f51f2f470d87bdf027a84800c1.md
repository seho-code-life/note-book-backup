# defineComponent

在setup语法糖出来之后，我们习惯这样去构建一个vue

```tsx
<script lang="ts" setup>
// ...
</script>
```

但是在之前，我们看到很多vue demo，都是使用defineComponent这个函数，我们来仔细看一下这个函数有什么具体的作用。

```tsx
// runtime-core/apiDefineComponent.ts

export function defineComponent(options: unknown) {
  return isFunction(options) ? { setup: options, name: options.name } : options
}
```

defineComponent主要是用于类型推断的函数，为了tsx/手动构造渲染/ide一些场景有更好的类型支持，我们在vue组件中给defineComponent传入options参数后，vue组件就有类型提示了；这个函数具有多个重载，我们简单了解一下

1. 第一个重载

```tsx
export function defineComponent<Props, RawBindings = object>(
  setup: (
    props: Readonly<Props>,
    ctx: SetupContext
  ) => RawBindings | RenderFunction
): DefineComponent<Props, RawBindings>
```

第一个重载是开发者直接设置，可以通过泛型传入类型，然后返回的DefineComponent的类型就是开发者自己定义的。

1. 第二个重载

```tsx
export function defineComponent<
  Props = {},
  RawBindings = {},
  D = {},
  C extends ComputedOptions = {},
  M extends MethodOptions = {},
  Mixin extends ComponentOptionsMixin = ComponentOptionsMixin,
  Extends extends ComponentOptionsMixin = ComponentOptionsMixin,
  E extends EmitsOptions = EmitsOptions,
  EE extends string = string
>(
  options: ComponentOptionsWithoutProps<
    Props,
    RawBindings,
    D,
    C,
    M,
    Mixin,
    Extends,
    E,
    EE
  >
): DefineComponent<Props, RawBindings, D, C, M, Mixin, Extends, E, EE>
```

没有props的对象格式，主要用于vetur（一个vue语法提示插件）以及tsx支持

1. 第三个重载

```tsx
export function defineComponent<
  PropNames extends string,
  RawBindings,
  D,
  C extends ComputedOptions = {},
  M extends MethodOptions = {},
  Mixin extends ComponentOptionsMixin = ComponentOptionsMixin,
  Extends extends ComponentOptionsMixin = ComponentOptionsMixin,
  E extends EmitsOptions = Record<string, any>,
  EE extends string = string
>(
  options: ComponentOptionsWithArrayProps<
    PropNames,
    RawBindings,
    D,
    C,
    M,
    Mixin,
    Extends,
    E,
    EE
  >
): DefineComponent<
  Readonly<{ [key in PropNames]?: any }>,
  RawBindings,
  D,
  C,
  M,
  Mixin,
  Extends,
  E,
  EE
>
```

用于props是数组类型，用途和第二个重载一样

1. 第四个重载

```tsx
export function defineComponent<
  // the Readonly constraint allows TS to treat the type of { required: true }
  // as constant instead of boolean.
  PropsOptions extends Readonly<ComponentPropsOptions>,
  RawBindings,
  D,
  C extends ComputedOptions = {},
  M extends MethodOptions = {},
  Mixin extends ComponentOptionsMixin = ComponentOptionsMixin,
  Extends extends ComponentOptionsMixin = ComponentOptionsMixin,
  E extends EmitsOptions = Record<string, any>,
  EE extends string = string
>(
  options: ComponentOptionsWithObjectProps<
    PropsOptions,
    RawBindings,
    D,
    C,
    M,
    Mixin,
    Extends,
    E,
    EE
  >
): DefineComponent<PropsOptions, RawBindings, D, C, M, Mixin, Extends, E, EE>
```

用于props是对象格式

这一篇内容很简单，大多数都是类型提示相关的，和vue基本上没有太大的关系

我们这一章的目标就是：

1. 认识defineComponent这个函数
2. 要知道defineComponent的源码中有多个重载，以实现不同类型props的情况
3. 不同的重载的用处也不一样（tsx/vetur/custom）