# var vm = new Vue({
data: {
a: 1
}
})
// `vm.a` 现在是响应式的
vm.b = 2
// `vm.b` 不是响应式的