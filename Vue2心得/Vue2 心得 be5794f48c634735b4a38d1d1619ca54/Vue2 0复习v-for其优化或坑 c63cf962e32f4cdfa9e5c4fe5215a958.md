# Vue2.0复习v-for其优化或坑

# Vue2.0复习v-for其优化或坑

复习了for，之前的语法是

<template v-for="(context,key) in message">

也可以用 of 替代 in 作为分隔符，因为它是最接近 JavaScript 迭代器的语法：

所以尽量用of，性能更棒

循环不仅仅可以循环数组还能循环对象，并且对象中可以传递三个参数

内容，键名，索引

如果在控制台想要动态修改某个值，通过索引是不能实时修改的。

当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。

所以为了不复用

我们用v-bind绑定一个熟悉，这个属性最好是后台传过来的唯一id值，即:key:"item,id"

建议尽可能在使用 v-for 时提供 key，除非遍历输出的 DOM 内容非常简单，或者是刻意依赖默认行为以获取性能上的提升。

需要用相应的数组方法才能检测到更新。

直接使用类似这样的修改无法检测到。所以视图无法更新。

Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：

- push()
- pop()
- shift()
- unshift()
- splice()
- sort()
- reverse()

你打开控制台，然后用前面例子的 items 数组调用变异方法：example1.items.push({ message: 'Baz' }) 。

### **[替换数组](https://cn.vuejs.org/v2/guide/list.html#%E6%9B%BF%E6%8D%A2%E6%95%B0%E7%BB%84)**

变异方法 (mutation method)，顾名思义，会改变被这些方法调用的原始数组。相比之下，也有非变异 (non-mutating method) 方法，例如：filter(), concat() 和 slice() 。这些不会改变原始数组，但**总是返回一个新数组**。当使用非变异方法时，可以用新数组替换旧数组：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20bac41b1973564b66a261e0b3d1d06dc1.csv)

你可能认为这将导致 Vue 丢弃现有 DOM 并重新渲染整个列表。幸运的是，事实并非如此。Vue 为了使得 DOM 元素得到最大范围的重用而实现了一些智能的、启发式的方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。

### **[注意事项](https://cn.vuejs.org/v2/guide/list.html#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)**

由于 JavaScript 的限制，Vue 不能检测以下变动的数组：

1. 当你利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue
2. 当你修改数组的长度时，例如：vm.items.length = newLength

举个例子：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%204b4a161472b84908abcecb636b099e67.csv)

为了解决第一类问题，以下两种方式都可以实现和 vm.items[indexOfItem] = newValue 相同的效果，同时也将触发状态更新：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20ba0c754431534539aec7ff81c886073f.csv)

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%2038626b492d3f4f41b10502007e3a49fd.csv)

你也可以使用 [vm.$set](https://vuejs.org/v2/api/#vm-set) 实例方法，该方法是全局方法 Vue.set 的一个别名：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20ae3a4f785aec4240be4a70d6ecfa39d3.csv)

为了解决第二类问题，你可以使用 splice：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20440088c10e5b433abcbe934bc53bb3cc.csv)

## **[对象更改检测注意事项](https://cn.vuejs.org/v2/guide/list.html#%E5%AF%B9%E8%B1%A1%E6%9B%B4%E6%94%B9%E6%A3%80%E6%B5%8B%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)**

还是由于 JavaScript 的限制，**Vue 不能检测对象属性的添加或删除**：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20fd2dc6a06d53467885db4b279aded38c.csv)

对于已经创建的实例，Vue 不能动态添加根级别的响应式属性。但是，可以使用 Vue.set(object, key, value) 方法向嵌套对象添加响应式属性。例如，对于：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20b7083167d78648249e4ef692a1022f14.csv)

你可以添加一个新的 age 属性到嵌套的 userProfile 对象：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%2091a2fa17e8944ff38df4062dd791fbf7.csv)

你还可以使用 vm.$set 实例方法，它只是全局 Vue.set 的别名：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%201efe6230afdb42e3a5c0b6ffc4851cb1.csv)

有时你可能需要为已有对象赋予多个新属性，比如使用 Object.assign() 或 _.extend()。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20e64ea9328a694552abd81f7ad605702b.csv)

你应该这样做：

[Untitled Database](Vue2%200%E5%A4%8D%E4%B9%A0v-for%E5%85%B6%E4%BC%98%E5%8C%96%E6%88%96%E5%9D%91%20c63cf962e32f4cdfa9e5c4fe5215a958/Untitled%20Database%20e457c15501a04c64ad2878ad662cc3cd.csv)

文档地址：[https://cn.vuejs.org/v2/guide/list.html#](https://cn.vuejs.org/v2/guide/list.html#)