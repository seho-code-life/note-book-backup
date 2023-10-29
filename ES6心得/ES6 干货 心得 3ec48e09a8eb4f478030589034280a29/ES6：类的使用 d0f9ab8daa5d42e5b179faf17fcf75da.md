# ES6：类的使用

# ES6：类的使用

*class* Coder{

name(*val*){

*console*.log(val);

return val;

}

skill(*val*){

*console*.log(this.name('jspang')+':'+'Skill:'+val);

}

//传递参数

*constructor*(*a*,*b*){

this.a=a;

this.b=b;

}

add(){

return this.a+this.b;

}

}

*let* jspang=new Coder(1,2);

*console*.log(jspang.add());

*class* htmler extends *Coder*{

}

*let* pang=new htmler;

pang.name('技术胖');