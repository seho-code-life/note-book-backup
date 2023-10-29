# var vm = new Vue({
data: {
items: ['a', 'b', 'c']
}
})
vm.items[1] = 'x' // 不是响应性的
vm.items.length = 2 // 不是响应性的