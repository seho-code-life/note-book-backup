# 105

# 105

如何在HTML5页面中嵌入音频和视频？

video audio

怎么实现移动端的边框0.5px？

伪元素+缩放，宽高是原dom的百分之200，然后缩小.5可以实现

兼容高分辨率的屏幕

写个方法，找出指定字符串中重复最多的字符及其长度

*//*

const str = "啦啦啦哈哈哈哈";

let result = {}; *// 存放全部的结果集的数组*

function test1(box = []){

*// 转换数组*

let array = str.split("");

array.forEach(v => {

if(typeof result[v] === "undefined"){

*// 添加对应的属性*

result[v] = 1;

}else{

result[v] ++;

}

})

}

test1();

console.log(result);