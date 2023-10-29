# ES6：新增数组知识（JSON重点）

# ES6：新增数组知识（JSON重点）

这就是一个标准的JSON数组格式，跟普通的JSON对比是在最后多了一个length属性。只要是这种特殊的json格式都可以轻松使用ES6的语法转变成数组。在ES6中绝大部分的Array操作都存在于Array对象里。我们就用Array.from(xxx)来进行转换。我们把上边的JSON代码转换成数组，并打印在控制台。

- let json = {
- '0': 'jspang',
- '1': '技术胖',
- '2': '大胖逼逼叨',
- length:3
- }
- let arr=Array.from(json);
- console.log(arr)

通过Array.of方法可以把一个类似数组的字符串或者数字转换为数组，比普通的eval速度要快很多。

find实例方法

所谓的实例方法就是并不是以Array对象开始的，而是必须有一个已经存在的数组，然后使用的方法，这就是实例方法（不理解请看下边的代码，再和上边的代码进行比对，你会有所顿悟）。这里的find方法是从数组中查找。在find方法中我们需要传入一个匿名函数，函数需要传入三个参数：

- value：表示当前查找的值。
- index：表示当前查找的数组索引。
- arr：表示当前数组。

在函数中如果找到符合条件的数组元素就进行return，并停止查找。你可以拷贝下边的代码进行测试，就会知道find作用。

- 
    
    let arr=[1,2,3,4,5,6,7,8,9];
    
- console.log(arr.find(function(value,index,arr){
- return value > 5;
- }))

fill实例方法

fill()也是一个实例方法，它的作用是把数组进行填充，它接收三个参数，第一个参数是填充的变量，第二个是开始填充的位置，第三个是填充到的位置。

*let* arr=[0,1,2,3,4,5,6,7,8,9];

arr.fill('jspang',2,5);

*console*.log(arr);

ES6的for循环

首先我们练习循环输出的数组值

有3种模式，第一种是循环下标，一种是循环值，一种是循环值和下标

//这个是循环值

for(*let* item of arr){

document.write(item);

//这是循环下标

for(*let* index of arr.keys()){

*console*.log(index);

//这是循环下标和值

for(*let* [index,item] of arr.entries()){

*console*.log(index+" "+item);

}

分别的主要方法是keys() entries()

这个entries方法生成的是Iterator形式的数组， 那这种形式的好处就是可以让我们在需要时用next()手动跳转到下一个值。我们来看下面的代码：

- 
    
    let arr=['jspang','技术胖','大胖逼逼叨']
    
- let list=arr.entries();
- console.log(list.next().value);
- console.log(list.next().value);
- console.log(list.next().value);

}

}