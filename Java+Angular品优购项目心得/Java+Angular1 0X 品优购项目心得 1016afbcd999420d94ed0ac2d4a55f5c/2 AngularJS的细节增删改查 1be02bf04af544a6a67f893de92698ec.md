# 2. AngularJS的细节增删改查

# 2. AngularJS的细节增删改查

对于一个简单的商品增删改查其实还是出了很多问题，我们抛开日常傻逼的bug除外，也对分布式和maven有更好的理解，

其实我在做这个项目的时候对mybaties还是有一点点陌生的，毕竟看文档只学2天，所以很多东西还不知道，尤其是我们品优购的第二天

还加入了一个新的js框架，js框架对我来说没有问题，因为自己有vue的经验，上手也特别的快。

首先就不用介绍了angular，我们直接讲业务细节。

我们做的功能就是增删改查，我们要充分利用angular的双向数据绑定，以及前端和后端的分页插件。

----显示全部的结果。

这个方法呢，我们的service层中是和模糊查询写在一起的，所以我们传递参数要传递品牌的实体类，为什么要传递实体类呢，因为我们的模糊条件查询

需要品牌名称或者品牌的字母，我们就直接传递一个对象进去，然后需要当前页面，和总页面的参数，具体如下。

**public** PageResult finpage(TbBrand brand, **int** pageNum, **int** pageSize) {

// 调用插件

PageHelper.*startPage*(pageNum, pageSize);

//使用exmple类设置条件

TbBrandExample exmple = **new** TbBrandExample();

//通过exmple创建Criteria

Criteria createCriteria = exmple.createCriteria();

// 非空判断拼接模糊查询

**if** (brand != **null**) {

**if** (brand.getName() != **null** && brand.getName().length() > 0) {

// 利用对象Criteria设置检索条件

createCriteria.andNameLike("%" + brand.getName() + "%");

}

**if** (brand.getFirstChar() != **null** && brand.getFirstChar().length() > 0) {

// 利用对象Criteria设置检索条件

createCriteria.andFirstCharLike("%" + brand.getFirstChar() + "%");

}

}

Page<TbBrand> page = (Page<TbBrand>) brandMapper.selectByExample(exmple);

**return** **new** PageResult(page.getTotal(), page.getResult());

}

这个方法的设计思路就是，我们条件查询的时候，不一定是全部写条件，很可能只写名称或者字母来执行查询，而且业务中我们的思路是分页方法和查询方法是一起的

所以我们的条件可有可无，所以我们要判断对象是否是空的，如果条件对象是空的我们就直接查询的是全部的。

设置检索条件是模糊查询我们从对象中取值然后百分号拼接即可

我们的action中的代码就直接返回PageResult对象，这个对象封装着总记录数和当前查询结果

所以我们可以在前端angular写代码了。

按照顺序我们得简单介绍一下前端的分页插件，我们配合angular用的是pagination插件，引入js和css以及模块之后我们就开始定义一些配置。

//分页控件配置

$scope.paginationConf = {

currentPage : 1,

totalItems : 10,

itemsPerPage : 10,

perPageOptions : [ 10, 20, 30, 40, 50 ],

onChange : **function**() {

$scope.reload(); //重新加载

}

};

然后我们调用angular的方法，传递参数，这个reload方法的执行的时候就是在这个插件变化的时候执行，所以相当于初始化方法，在reload中我们这样定义：

//重新加载列表

$scope.reload = **function**() {

//切换页码

$scope.searchClick($scope.paginationConf.currentPage, $scope.paginationConf.itemsPerPage);

}

我们传递的两个参数一个是当前页面，一个是页面显示多少页。

我们的searchClick方法中定义的是：

//初始化对象

$scope.search = {}

$scope.searchClick = **function**(page, rows) {

$http.post("../brand/search.do?page=" + page + '&rows=' + rows, $scope.search).success(

**function**(res) {

console.log(res)

//当前页数结果

$scope.Itemlist = res.row;

//更新总记录数

$scope.paginationConf.totalItems = res.total;

}

)

}

我们的search对象存储的是条件检索的条件，如果我们不定义条件的话，传递这个参数会让前端js报错，所以我们给他一个空对象初始化。

这样我们拿到pageResult对象就可以正常解析里面数据到html，通过ng-reapt循环它们。

--增加和修改方法

我们来看看在service中我们定义的接口实现方法：

// 增加品牌的方法

@Override

**public** **void** addbrand(TbBrand brand) {

// **TODO** Auto-generated method stub

brandMapper.insert(brand);

}

//执行更新的方法

@Override

**public** **void** update(TbBrand brand) {

// **TODO** Auto-generated method stub

brandMapper.updateByPrimaryKey(brand);

}

//根据id查询单条

@Override

**public** TbBrand findOne(Long id) {

// **TODO** Auto-generated method stub

**return** brandMapper.selectByPrimaryKey(id);

}

我们的service层没有什么特殊的逻辑，按照正常的mapper映射去写。我们的action中也是一样。

唯独不一样的是我们定义了result结果对象，我们对每一次操作进行一个trycatch捕获，如果异常说明操作失败，这个时候我们返回的result对象会返回布尔类型或者提示信息

我们前端js异步获取的时候，就可以获取到操作的正确与否。

不一样的是我们的添加和编辑是一个窗口，我们执行的save方法，需要判断当前操作是添加还是编辑，我们需要这么做：

我们在点击列表中的编辑的时候，根据点击的id获取到了单条的信息：

//根据id获取内容

$scope.findById = **function**(id) {

$http.get("../brand/getContext.do?id=" + id).success(

**function**(res) {

$scope.entity = res

}

)

}

我们把返回的数据绑定到entity方法上，然后通过双向绑定到修改的input框中。

值得注意的是，我们关键的添加的按钮：

<button type=*"button"* class=*"btn btn-default"* title=*"新建"*

data-toggle=*"modal"* data-target=*"#editModal"* ng-click=*"entity={}"*>

<i class=*"fa fa-file-o"*></i> 新建

</button>

在我们的新建按钮上，我们把entity对象清空，这样做2个好处，在点击按钮的时候，我们会清空编辑的文本框又可以清除里面的id，我们可以通过id的存在来判断是编辑框还是

添加框。我们可以在save方法写成下面的样子。

//保存

$scope.save = **function**() {

**var** methodName = 'add';

**if** ($scope.entity.id != **null**) {

methodName = 'update'

}

$http.post("../brand/" + methodName + ".do", $scope.entity).success(

**function**(res) {

//如果添加成功就重新查询

$scope.overDo(res)

}

)

}

删除操作：

我们的删除是复式删除，是确认checkbox框，来删除的。

service端代码：

**public** **void** delete(Long[] ids) {

// **TODO** Auto-generated method stub

// 循环数组

**for** (Long item : ids) {

brandMapper.deleteByPrimaryKey(item);

}

}

我们传递一个数组，这个数组是很多要删除的id，我们通过循环来删除。

我们的action同理，我们主要是js操作这个数组。

前端js:

<td><input type=*"checkbox"* ng-click=*"getId($event,item.id)"*></td>

我们获取了默认对象$event，和我们需要的id，我们传递这两个参数的意义是：

为了判断复选框的状态，和通过id删除数据

//获取删除的id数组

$scope.updateArr = [];

$scope.getId = **function**($event, id) {

**if** ($event.target.checked) {

$scope.updateArr.push(id)

} **else** {

$scope.updateArr.splice($scope.updateArr.indexOf(id), 1)

}

}

我们定义一个空数组，如果我们通过事件对象看到如果是选中状态的话就给id push到这个数组中，反之，如果是取消了勾选，我们会通过数组找到这个id的下标利用indexof方法，然后把它删除掉，这样我们就获得了一个需要删除的id数组。

当我们点击删除的时候，点击方法为：

//根据id删除

$scope.deleteData = **function**() {

$http.post('../brand/delete.do', $scope.updateArr).success(

**function**(res) {

**if** (res.success) {

$scope.overDo(res)

}

}

)

}