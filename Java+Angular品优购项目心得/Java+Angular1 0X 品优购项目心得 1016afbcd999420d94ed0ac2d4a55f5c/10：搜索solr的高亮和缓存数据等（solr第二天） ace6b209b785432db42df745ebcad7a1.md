# 10：搜索solr的高亮和缓存数据等（solr第二天）

# 10：搜索solr的高亮和缓存数据等（solr第二天）

我们需要的效果是，搜索关键字需要在商品标题中有高亮效果

这个效果有2种方案，一种是前端，前端个人认为比较好实现，但是品优购中用solr中的api来实现高亮

方案比较麻烦和繁琐，但是顺序一梳理就一目了然；

//new一个查询高亮的对象

HighlightQuery query = **new** SimpleHighlightQuery();

// 设置高亮域

HighlightOptions highlightOptions = **new** HighlightOptions().addField("item_title");

// 设置高亮前缀和后缀

highlightOptions.setSimplePrefix("<em style='color:red'>");

highlightOptions.setSimplePostfix("</em>");

// 给查询设置高亮

query.setHighlightOptions(highlightOptions);

// 设置条件域和关键词

Criteria criteria = **new** Criteria("item_keywords").is(searchMap.get("keywords"));

query.addCriteria(criteria);

// 返回高亮页对象

HighlightPage<TbItem> pageList = slor.queryForHighlightPage(query, TbItem.**class**);

// 循环高亮入口的集合（每一条记录的对象）

**for** (HighlightEntry<TbItem> tbItem : pageList.getHighlighted()) {

// 获取高亮列表（取决于有多少个高亮域）

List<Highlight> highlights = tbItem.getHighlights();

// 判断高亮域中集合是否有内容

**if** (highlights.size() > 0 && highlights.get(0).getSnipplets().size() > 0) {

// 获取原实体类（和getContent）来源一样

TbItem entity = tbItem.getEntity();

// 设置标题（getSnipplets表示每个域中可能存储多值），我们这边只有一个域和只存一个值，所以get0就可以

entity.setTitle(highlights.get(0).getSnipplets().get(0));

}

}

//通过getContent返回的结果是（原生内容），并没有高亮内容

map.put("rows",pageList.getContent());

**return** map;

首先我们梳理一下：

我们用solr做高亮，首先我们定义一个高亮查询的对象，然后要把这个对象加条件，add设置关键词条件域，

HighlightOptions highlightOptions = **new** HighlightOptions().addField("item_title");

设置条件域，以及前缀和后缀，最后给query set进去，然后用模板queryForHighlightPage的方法

返回一个高亮页对象，对象中主要用getHighlighted方法获取每一条记录的对象，每一条记录的对象中可以通过getHighlights

来获取有多少个高亮域（可能有多个），每个域中的值也要获取getSnipplets方法，然后我们最后通过getEntity获取原生类settitle即可

给map添加，返回即可

---

---

前端逻辑：返回到前端是一个html格式的，angular有一种安全机制，是拦截这种html的，所以我们需要用$sce这个服务来过滤掉这种机制

base

/*$sce服务写成过滤器*/

app.filter('trustHtml', [ '$sce', **function**($sce) {

**return** **function**(data) {

**return** $sce.trustAsHtml(data);

}

} ]);

html

<div class=*"attr"* ng-bind-html=*"item.title | trustHtml"*></div>

angular好恶心，这样好麻烦

---

查询商品分类（分组查询）：

// 查询全部的商品分类（分组查询）

**private** List<String> getAllCategory(Map searchMap) {

List list = **new** ArrayList();

Criteria criteria = **new** Criteria("item_keywords").is(searchMap.get("keywords"));

Query query = **new** SimpleQuery("*:*").addCriteria(criteria);

// 设置分组选项

GroupOptions groupOptions = **new** GroupOptions();

groupOptions.addGroupByField("item_category");

query.setGroupOptions(groupOptions);

// 得到分组页

GroupPage<TbItem> queryForGroupPage = slor.queryForGroupPage(query, TbItem.**class**);

// 得到分组结果集

GroupResult<TbItem> groupResult = queryForGroupPage.getGroupResult("item_category");

// 得到分组结果入口页

Page<GroupEntry<TbItem>> groupEntries = groupResult.getGroupEntries();

// 得到分组入口集合

List<GroupEntry<TbItem>> content = groupEntries.getContent();

**for** (GroupEntry<TbItem> groupEntry : content) {

list.add(groupEntry.getGroupValue());

}

**return** list;

}

根据分类名称查询模板ID，品牌列表和规格列表，缓存在redis中

分析：我们需要在运营商中缓存模板ID，和品牌列表规格列表（查询的方法中）

// 缓存模板id

**private** **void** saveToRadis() {

// 每次执行查询的时候，一次性读取缓存进行存储 (因为每次增删改都要执行此方法)

List<TbItemCat> findAll = findAll();

**for** (TbItemCat tbItemCat : findAll) {

redisTemplate.boundHashOps("itemCat").put(tbItemCat.getName(), tbItemCat.getTypeId());

}

}

存在了redis中大key为itemCat中的集合中，键是分类名称，值是模板ID

然后去缓存分类和规格

**private** **void** saveToRadis() {

List<TbTypeTemplate> findAll = findAll();

**for** (TbTypeTemplate tbTypeTemplate : findAll) {

// 存储品牌列表

List<Map> brandList = JSON.*parseArray*(tbTypeTemplate.getBrandIds(), Map.**class**);

redisTemplate.boundHashOps("brandList").put(tbTypeTemplate.getId(), brandList);

// 存储规格列表(根据模板ID查询规格列表)

List<Map> specList = findSpecList(tbTypeTemplate.getId());

redisTemplate.boundHashOps("specList").put(tbTypeTemplate.getId(), specList);

}

}

我们调用查询全部的方法，循环这个方法，然后存储每一个类型模板对象的ID为键，具体的模板ID下的品牌列表和规格列表为值

全部存储到redis中即可

返回规格+品牌+分类

然后我们要在搜索业务中，根据分类名称查询品牌和规格

// 根据商品分类名词查询规格和品牌列表

**private** Map seachBrandListAndSpecList(String brandName) {

Map map = **new** HashMap();

// 根据分类名称查询模板ID

Long templateID = (Long) radisTemplate.boundHashOps("itemCat").get(brandName);

**if** (templateID != **null**) {

// 获取品牌列表

List brandList = (List) radisTemplate.boundHashOps("brandList").get(templateID);

map.put("brandList", brandList);

// 获取品牌列表

List specList = (List) radisTemplate.boundHashOps("specList").get(templateID);

map.put("specList", specList);

}

**return** map;

}

首先获取模板ID，然后通过模板ID获取品牌列表和规格列表

String category = (String) searchMap.get("category");

**if** (!category.equals("")) {

map.putAll(seachBrandListAndSpecList(category));

} **else** {

// 获取品牌列表和规格列表（取第一个）

**if** (allCategory.size() > 0) {

// 添加给map集合

map.putAll(seachBrandListAndSpecList(allCategory.get(0)));

}

}

如果前端没有传递商品分类，说明用户没有点击，如果没有点击，品牌和规格默认显示第一个，如果点击了就按照分类名称来查询

返回map即可；

我们的前端展示点击增加标签，取消撤销标签，并且对应的dom也会删除（当点击之后）：

$scope.searchMap = {

'keywords' : '',

'category' : '',

'brand' : '',

'spec' : {}

}; //搜索对象

我们定义的这个对象很有用处，我们只需要根据这个变量进行增删就可以达到标签增加和删除了

//添加搜索项

$scope.addSearchItem = **function**(key, value) {

**if** (key == 'category' || key == 'brand') { //如果点击的是分类或者是品牌

$scope.searchMap[key] = value;

} **else** {

$scope.searchMap.spec[key] = value;

}

$scope.search()

}

//移出搜索项

$scope.removeSearchItem = **function**(key, value) {

**if** (key == 'category' || key == 'brand') { //如果点击的是分类或者是品牌

$scope.searchMap[key] = '';

} **else** {

//删除规格

**delete** $scope.searchMap.spec[key];

}

//执行搜索

$scope.search()

}

每一次操作之后调用搜索的方法，删除规格时候用到了delete关键字（重点），用于删除集合

我们每点击一下要进行对结果的筛选，要用solr中的filterquery

在获取结果之前要操作过滤

/* ==============================过滤分类============================== */

**if** (!"".equals(searchMap.get("category"))) {

Criteria filterCriteria = **new** Criteria("item_category").is(searchMap.get("category"));

FilterQuery filterQuery = **new** SimpleFilterQuery(filterCriteria);

query.addFilterQuery(filterQuery);

}

/* ==============================过滤品牌============================== */

**if** (!"".equals(searchMap.get("brand"))) {

Criteria filterCriteria = **new** Criteria("item_brand").is(searchMap.get("brand"));

FilterQuery filterQuery = **new** SimpleFilterQuery(filterCriteria);

query.addFilterQuery(filterQuery);

}

/* ==============================过滤规格============================== */

**if** (searchMap.get("spec") != **null**) {

// 转换为map集合

Map<String, String> specMap = (Map) searchMap.get("spec");

// 循环map集合

**for** (String item : specMap.keySet()) {

// 动态域

Criteria filterCriteria = **new** Criteria("item_spec_" + item).is(searchMap.get(item));

FilterQuery filterQuery = **new** SimpleFilterQuery(filterCriteria);

query.addFilterQuery(filterQuery);

}

}

规格很特殊，需要循环map集合中的键，然后拼接动态域即可

![https://www.notion.so10：搜索solr的高亮和缓存数据等（solr第二天）_files/b878284cedf6245363d1b09b1ab91f39.png](https://www.notion.so10：搜索solr的高亮和缓存数据等（solr第二天）_files/b878284cedf6245363d1b09b1ab91f39.png)

上面是关键的代码