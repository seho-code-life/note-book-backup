# Vue2.0复习v-if和v-show的区别

# Vue2.0复习v-if和v-show的区别

在vue中 v-if和v-show的条件都是boolean类型的值

用法一：直接在v-if或者v-show中添加一个属性，属性是true或者false

<div id="box">

<div v-if="zhuangtai">

this is a true

</div>

</div>

<script>

var vm=new Vue({

el:'#box',

data:{

zhuangtai:true

}

})

</script>

true为显示此块，false不显示

v-if和v-show的区别是什么

他们的区别都是消失隐藏dom元素的，但是v-show是在元素上添加display，这样的好处就是效率更高，而v-if是删除节点然后再添加节点，相比而言，更推荐v-show

不仅有v-if还有v-else v-else-if 他们就是用法都是一样，传boolean值，依据条件进行判断然后显示和隐藏

唯一要注意他们要写在一起，如果中间有其他标签，会阻止浏览器对其解析

---

这边引入一个key值的概念

如果我们用方法改变了属性，这个时候v-if或者v-show相应得块发生了变化

但是在vue中有一种机制，如果判断发生了改变，系统自动会尽量的复用与其无关的元素，最典型的是文本框中的内容，复用之后，内容依然在

如果不想让vue给我们复用组件就要在dom中加key值，相当于告诉vue，我拥有这个key值是唯一的，所以不要复用这个元素。

![https://www.notion.soVue2.0复习v-if和v-show的区别_files/QQ截图20180831232706.png](https://www.notion.soVue2.0复习v-if和v-show的区别_files/QQ截图20180831232706.png)

改变之后文本框中的值依然存在

加key值后：

![https://www.notion.soVue2.0复习v-if和v-show的区别_files/QQ截图20180831232807.png](https://www.notion.soVue2.0复习v-if和v-show的区别_files/QQ截图20180831232807.png)

效果：

![https://www.notion.soVue2.0复习v-if和v-show的区别_files/QQ截图20180831232834.png](https://www.notion.soVue2.0复习v-if和v-show的区别_files/QQ截图20180831232834.png)