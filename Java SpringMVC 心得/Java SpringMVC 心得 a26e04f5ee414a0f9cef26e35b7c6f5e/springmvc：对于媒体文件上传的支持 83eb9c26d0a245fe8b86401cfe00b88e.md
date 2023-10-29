# springmvc：对于媒体文件上传的支持

# springmvc：对于媒体文件上传的支持

我只能说，springmvc对于文件上传的封装简直是简单的没朋友，直接上代码了

首先配置springmvc的xml

<!-- 配置 MultipartResolver -->

<bean id=*"multipartResolver"*

class=*"[org.springframework.web.multipart.commons.CommonsMultipartResolver](http://org.springframework.web.multipart.commons.commonsmultipartresolver/)"*>

<property name=*"defaultEncoding"* value=*"UTF-8"*></property>

<property name=*"maxUploadSize"* value=*"1024000"*></property>

</bean>

然后jsp页面

<form action=*"upload"* method=*"post"* enctype=*"multipart/form-data"*>

Desc:<input type=*"text"* name=*"desc"*>

file:<input type=*"file"* name=*"file"*>

<input type=*"submit"* value=*"提交"*>

</form>

@RequestMapping("/upload")

**public** ModelAndView upload(@RequestParam("desc") String desc,@RequestParam("file") MultipartFile file) **throws** Exception{

ModelAndView model=**new** ModelAndView("success");

**if**(!file.isEmpty()){

//不为空

file.transferTo(**new** File("D:\\"+file.getOriginalFilename()));

}

**return** model;

}