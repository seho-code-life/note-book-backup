# JS中的arguments详解

# JS中的arguments详解

# arguments详解

# 

今天学习了arguments，平时写js可能用到的地方不是很多，但是还是非常有必要了解的

首先，它是存在于函数中，函数执行时会自动创建，尽管你看不见它

在Javascript中arguments是一个类似Array的存在，但它不能等同于Array。

<script>

function add(a,b){

var length=arguments.length;

for(var i=0;i<length;){

console.log(arguments[i]);

i++;

}

}

add(1,2,3,4,5);

</script>

查看参数的值

参考地址：[https://blog.csdn.net/u012860063/article/details/53871351](https://blog.csdn.net/u012860063/article/details/53871351)