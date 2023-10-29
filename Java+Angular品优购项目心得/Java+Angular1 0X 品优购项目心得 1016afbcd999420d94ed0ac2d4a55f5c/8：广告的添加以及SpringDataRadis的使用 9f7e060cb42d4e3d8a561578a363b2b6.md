# 8：广告的添加以及SpringDataRadis的使用

# 8：广告的添加以及SpringDataRadis的使用

我们在第八天需要做2个重要的知识点一个是广告图片上传：

我们调用ng的service和common下的service即可：

//上传广告图

$scope.uploadFile=**function**(){

uploadService.uploadFile().success(

**function**(response){

**if**(response.success){

$scope.entity.pic=response.message;

}**else**{

alert("上传失败！");

}

}

点击上传的时候调用此方法即可

还有一个就是我们今天的主角，springDataRadis的使用

这个框架主要是对radis的客户端jadis做了一个高度的封装，能够使用自己api来操作radis，如集合操作，增删改查，还有对值得操作等等

![https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/79d4ccba678a7051f3af0c6d380d2205.png](https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/79d4ccba678a7051f3af0c6d380d2205.png)

![https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/88208e5510de8bf5316de8a0707183ef.png](https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/88208e5510de8bf5316de8a0707183ef.png)

![https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/3c6f0eb2a8a5883e33284d04e50ec581.png](https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/3c6f0eb2a8a5883e33284d04e50ec581.png)

![https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/568c2bfab19dd1d027a9ffa6caf455c5.png](https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/568c2bfab19dd1d027a9ffa6caf455c5.png)

然后我们的配置文件等等：

<!-- 缓存 -->

<dependency>

<groupId>redis.clients</groupId>

<artifactId>jedis</artifactId>

<version>2.8.1</version>

</dependency>

<dependency>

<groupId>org.springframework.data</groupId>

<artifactId>spring-data-redis</artifactId>

<version>1.7.2.RELEASE</version>

</dependency>

![https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/6e81002e822c31dee161a9cb5a4cde1e.png](https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/6e81002e822c31dee161a9cb5a4cde1e.png)

![https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/1a4dcaa16b30ceed26269c85cf61351a.png](https://www.notion.so8：广告的添加以及SpringDataRadis的使用_files/1a4dcaa16b30ceed26269c85cf61351a.png)

然后打开radisserver端启动即可

我们要在广告这一块做一个缓存，所以我们要在广告的查询，增加，修改，删除四个方法中，对应的再做radis的修改

（查询的关键代码注释了）

查询：

@Override

**public** List<TbContent> findByCategoryId(Long categoryId) {

List<TbContent> list = (List<TbContent>) redisTemplate.boundHashOps("content").get(categoryId);

**if** (list == **null**) {

System.***out***.println("从数据库中查询数据并放入缓存 ");

TbContentExample example = **new** TbContentExample();

Criteria criteria = example.createCriteria();

criteria.andCategoryIdEqualTo(categoryId);// 指定条件:分类ID

criteria.andStatusEqualTo("1");// 指定条件：有效

example.setOrderByClause("sort_order");// 排序

list = contentMapper.selectByExample(example);

redisTemplate.boundHashOps("content").put(categoryId, list);// 放入缓存

} **else** {

System.***out***.println("从缓存中查询数据 ");

}

**return** list;

}

批量删除：

/**

* 批量删除

*/

@Override

**public** **void** delete(Long[] ids) {

**for** (Long id : ids) {

// 清除缓存

Long categoryId = contentMapper.selectByPrimaryKey(id).getCategoryId();

redisTemplate.boundHashOps("content").delete(categoryId);

contentMapper.deleteByPrimaryKey(id);

}

}

修改：

/**

* 修改

*/

@Override

**public** **void** update(TbContent content) {

// 查询原来的分组ID

Long categoryId = contentMapper.selectByPrimaryKey(content.getId()).getCategoryId();

// 清除原分组的缓存

redisTemplate.boundHashOps("content").delete(categoryId);

contentMapper.updateByPrimaryKey(content);

// 清除现分组缓存

**if** (categoryId.longValue() != content.getCategoryId().longValue()) {

redisTemplate.boundHashOps("content").delete(content.getCategoryId());

}

}

增加：

/**

* 增加

*/

@Override

**public** **void** add(TbContent content) {

contentMapper.insert(content);

// 清除缓存

redisTemplate.boundHashOps("content").delete(content.getCategoryId());

}