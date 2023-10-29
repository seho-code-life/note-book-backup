# Vue和jQuery的结合使用心得

# Vue和jQuery的结合使用心得

Vue 的官方是不建议直接操作 DOM 的，Vue 的用途在于视图和数据的绑定。如果通过JQuery 直接操作 DOM 的话，势必会造成视图数据和模型数据的不匹配，这样 Vue 就失去它存在的意义了。

JQuery 和 VueJS 合理使用并不会造成冲突，因为他们的侧重点不同，VueJS 侧重数据绑定和视图组件，JQuery 侧重异步请求和动画效果。如果使用JQuery + VueJS 开发，一定要在 Vue 渲染完所有的 HTML组件之后再通过 JQuery 处理，而使用 JQuery 时应避免直接操作 DOM ，但是应用动画是允许的。