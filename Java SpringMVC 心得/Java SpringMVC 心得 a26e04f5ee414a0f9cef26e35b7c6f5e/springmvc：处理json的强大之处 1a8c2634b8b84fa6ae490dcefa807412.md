# springmvc：处理json的强大之处

# springmvc：处理json的强大之处

springmvc处理json格式实在是非常强大，仅仅只需要几句代码。

首先我们需要导入json必须的包

![https://www.notion.sospringmvc：处理json的强大之处_files/ce8a7049df26bba03a5a9d1c53c4c6ee.png](https://www.notion.sospringmvc：处理json的强大之处_files/ce8a7049df26bba03a5a9d1c53c4c6ee.png)

![https://www.notion.sospringmvc：处理json的强大之处_files/948a48799af6e58a75668368e91ed975.png](https://www.notion.sospringmvc：处理json的强大之处_files/948a48799af6e58a75668368e91ed975.png)

然后我们开始上代码

@Controller

**public** **class** json {

@ResponseBody

@RequestMapping("requestJson")

**public** List<User> jsontest(){

List<User> list=**new** ArrayList<>();

list.add(**new** User(1,"沈昊"));

list.add(**new** User(2,"嘻嘻"));

**return** list;

}

}

我们可以把一个类对象，直接return出去，然后ajax获取到直接是json，非常方便，这就是springmvc对json的支持