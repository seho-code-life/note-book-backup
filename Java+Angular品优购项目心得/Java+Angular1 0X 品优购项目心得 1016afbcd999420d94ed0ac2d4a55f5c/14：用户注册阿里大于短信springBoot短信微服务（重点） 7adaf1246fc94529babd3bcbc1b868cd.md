# 14：用户注册阿里大于短信springBoot短信微服务（重点）

# 14：用户注册/阿里大于短信/springBoot短信微服务（重点）

我们14天要做的是用户注册的功能，我们需要一个独立于品优购的一个短信微服务用于发送短信

我们的短信服务是使用阿里大于，使用springboot进行搭建；

由于这一天的代码没有手敲，所以笔记会详细一些

我们首先快速的初始化一下springboot（包含热加载以及内置activemq服务）；

<dependency>

<groupId>org.springframework.boot</groupId>

<artifactId>spring-boot-starter-web</artifactId>

</dependency>

<dependency>

<groupId>org.springframework.boot</groupId>

<artifactId>spring-boot-devtools</artifactId>

</dependency>

<dependency>

<groupId>org.springframework.boot</groupId>

<artifactId>spring-boot-starter-activemq</artifactId>

</dependency>

核心的boot依赖，我们引入之后再引入阿里大于的依赖，构成下面的pom

![https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/0dc36a03c2b9aa17bf3c7945647256b3.png](https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/0dc36a03c2b9aa17bf3c7945647256b3.png)

springboot需要一个启动类

![https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/0eea194b13bb02f0ae7e9fa691f06067.png](https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/0eea194b13bb02f0ae7e9fa691f06067.png)

main方法直接运行就可以看到控制台输出；

我们在pom中引入了内置的activemq服务，拥有默认的监听程序，由于我们自己有服务，所以要在配置文件中指向activemq的地址

![https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/7bd729354bc28463c46a80c2a336ae10.png](https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/7bd729354bc28463c46a80c2a336ae10.png)

package cn.itcast.sms;

import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;

import org.springframework.jms.annotation.JmsListener;

import [org.springframework.stereotype.Component;](http://org.springframework.stereotype.component;/)

import com.aliyuncs.dysmsapi.model.v20170525.SendSmsResponse;

import com.aliyuncs.exceptions.ClientException;

@Component

public class SmsListener {

@Autowired

private SmsUtil smsUtil;

@JmsListener(destination="sms")

public void sendSms(Map<String,String> map){

try {

SendSmsResponse response = smsUtil.sendSms(map.get("mobile"),

map.get("template_code") ,

map.get("sign_name") ,

map.get("param") );

System.out.println("code:"+response.getCode());

System.out.println("message:"+response.getMessage());

} catch (ClientException e) {

// TODO Auto-generated catch block

e.printStackTrace();

}

}

}

传递的是一个map集合，map中有模板名称和签名名称，以及携带参数等等，我们调用了sms的工具类，将直接发送短信：

工具类就是阿里官方提供的实例；

发送短信主要的是successkey，这个值我们存储到了配置文件中

我们直接在工具类中引入

@Autowired

**private** Environment env;

env.getProperty("accessKeyId");

具体就取到了

这样我们的发送短信的springboot微服务就完成了，我们需要建立模块用户的服务和用户的web层进行angular的绑定

当前端传递手机号点击获取验证的时候，执行下面的方法：

public void createSmsCode(final String phone) {

//1.生成一个6位随机数（验证码）

final String smscode= (long)(Math.random()*1000000)+"";

System.out.println("验证码："+smscode);

//2.将验证码放入redis

redisTemplate.boundHashOps("smscode").put(phone, smscode);

//3.将短信内容发送给activeMQ

jmsTemplate.send(smsDestination, new MessageCreator() {

@Override

public Message createMessage(Session session) throws JMSException {

MapMessage message = session.createMapMessage();

message.setString("mobile", phone);//手机号

message.setString("template_code", template_code);//验证码

message.setString("sign_name", sign_name);//签名

Map map=new HashMap();

map.put("number", smscode);

message.setString("param", JSON.toJSONString(map));

return message;

}

});

}

先存储到缓存中，然后发送activemq，类型是一个map，我们把验证码塞到一个集合中，再把它转换成json字符串再塞给activemq；

这样我们的微服务就监听到了我们的手机号等等，发送消息，当用户点击注册的时候，我们会判断输入的手机号和验证码是否和缓存中的匹配：

@Override

**public** **boolean** checkSmsCode(String phone, String code) {

String systemcode= (String) redisTemplate.boundHashOps("smscode").get(phone);

**if**(systemcode==**null**){

**return** **false**;

}

**if**(!systemcode.equals(code)){

**return** **false**;

}

**return** **true**;

}

增加的方法：调用spring提供的MD5进行加密

**public** **void** add(TbUser user) {

user.setCreated(**new** Date());//用户注册时间

user.setUpdated(**new** Date());//修改时间

user.setSourceType("1");//注册来源

user.setPassword( DigestUtils.md5Hex(user.getPassword()));//密码加密

userMapper.insert(user);

}

控制层代码：发送验证码验证格式是否正确：

@RequestMapping("/sendCode")

**public** Result sendCode(String phone){

**if**(!PhoneFormatCheckUtils.isPhoneLegal(phone)){

**return** **new** Result(**false**, "手机格式不正确");

}

**try** {

userService.createSmsCode(phone);

**return** **new** Result(**true**, "验证码发送成功");

} **catch** (Exception e) {

e.printStackTrace();

**return** **new** Result(**false**, "验证码发送失败");

}

}

工具类在common下的验证手机格式，只有格式正确才能发送短信

![https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/dc887aa4e1e5b73934ec9d07c203a5d1.png](https://www.notion.so14：用户注册阿里大于短信springBoot短信微服务（重点）_files/dc887aa4e1e5b73934ec9d07c203a5d1.png)

尽情期待。。。