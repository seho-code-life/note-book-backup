# 第十七部分：搜索页面的边界处理以及搜索历史

# 第十七部分：搜索页面的边界处理以及搜索历史

搜索页面的边界处理：

1.没有结果

2.节流操作

3.离开焦点

第一个没有结果我们直接一个组件直接代替，判断显示就是根据结果长度和变量来判断，这个就不上代码。

节流操作：

//节流器

export function debounse(func, delay) {

let timer;

return function(...args) {

//定义timer

if (timer) {

//清除计时器

clearTimeout(timer);

}

timer = setTimeout(() => {

//劫持传入的对象和参数

func.apply(this, args);

}, delay);

};

}

定时器，支持传入需要节流的函数和秒数

我们在组件这样调用

created(){

this.$watch('query',debounse((query) => {

this.$emit('queryChange',query)

},200))

}

3.离开焦点，我们需要在suggest组件上监听

beforeScroll

这个事件

在这个事件上我们需要向外触发一个事件给search组件，父组件再调用searchbox组件中的input失去焦点方法即可

---

搜索历史

搜索历史我们需要共享一些数据，所以需要vuex获取localstorge中的数据，我们定义vuex的action，需要再common下定义一个js，用于存储逻辑

/**

* 用于封装storage的相关操作

*/

import stroage from 'good-storage'

//保存搜索历史

const SEARCH_KEY = '__search__'

const MAX_LENGTH = 15

export function insertArray(arr,val,compare,maxLen){

//查询索引(根据比较函数更灵活)

let index = arr.findIndex(compare)

//获取到索引之后

if(index===0){

//如果是第一个就直接插入，不用操作

return

}

//如果索引大于1，说明后面有此记录，先删除再添加

if(index>0){

arr.splice(index,1)

}

//插入到最初的位置

arr.unshift(val)

//如果数组长度大于规定最大限制，就直接删除最后一个

if(maxLen && maxLen>MAX_LENGTH){

arr.pop()

}

}

export function saveHistory(query){

//获取键的值，如果没有默认是空数组

let seaches = stroage.get(SEARCH_KEY,[])

insertArray(seaches,query,(item)=>{

return item === query

},MAX_LENGTH)

stroage.set(SEARCH_KEY,query)

//返回这个新数组

return seaches;

}

//查询存储中的历史

export function getHistory(){

return stroage.get(SEARCH_KEY,[])

}

存储到action中即可

//关于搜索结果历史存储的action

export const saveSearchHistory = function({commit},query){

commit(type.SET_SEARCH_HISTORY,saveHistory(query))

}