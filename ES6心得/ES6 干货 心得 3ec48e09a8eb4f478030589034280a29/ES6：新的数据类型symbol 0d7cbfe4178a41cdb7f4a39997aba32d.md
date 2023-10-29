# ES6：新的数据类型symbol

# ES6：新的数据类型symbol

*var* aa=*Symbol*("试试");

*console*.log(aa);

symbol给对象赋值

*var* nn=*Symbol*();

*var* obj={

[nn]:'哈哈'

}

*console*.log(obj[nn]);

symbol保护作用

*var* obj={

name:'哈哈',

sex:'男',

};

*var* age=*Symbol*();

obj[age]=15

for(*let* item in obj){

*console*.log(obj[item]);

}