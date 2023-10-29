# springmvc：PathVariable占位符应用

# springmvc：PathVariable占位符应用

![https://www.notion.sospringmvc：PathVariable占位符应用_files/QQ截图20181016201507.png](https://www.notion.sospringmvc：PathVariable占位符应用_files/QQ截图20181016201507.png)

这种方法非常的好玩和简单：

@RequestMapping(value="/hello/{id}",method=RequestMethod.***GET***)

**public** String hello(@PathVariable("id") Integer id){

System.***out***.println(id);

**return** "success";

}

通过映射url的{**}可以获取到参数，然后在方法中可以用pathvariable来获取，并且定义类型且赋值，然后输出出来，方便了传参的操作，不用繁琐地拼接js变量。