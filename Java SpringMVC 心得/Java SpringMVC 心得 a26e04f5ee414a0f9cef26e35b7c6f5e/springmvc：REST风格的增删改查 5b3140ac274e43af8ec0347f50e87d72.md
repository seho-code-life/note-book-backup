# springmvc：REST风格的增删改查

# springmvc：REST风格的增删改查

REST风格之前已经介绍过用法和历史了，我们直接上手代码

@RequestMapping(value="/emp",method=RequestMethod.***PUT***)

**public** String update(Employee employee){

System.***out***.println("login");

employeeDao.save(employee);

**return** "redirect:/emps";

}

@RequestMapping(value="/emp/{id}",method=RequestMethod.***GET***)

**public** String input(@PathVariable("id") Integer id,Map<String,Object> map){

map.put("employee",employeeDao.get(id));

map.put("departments", departmentDao.getDepartments());

**return** "Input";

}

@RequestMapping(value="/emp/{id}",method=RequestMethod.***DELETE***)

**public** String delete(@PathVariable("id") Integer id){

employeeDao.delete(id);

**return** "redirect:/emps";

}

@RequestMapping(value="/emp",method=RequestMethod.***POST***)

**public** String add(Employee employee){

employeeDao.save(employee);

**return** "redirect:/emps";

}

@RequestMapping(value="emp",method=RequestMethod.***GET***)

**public** String input(Map<String,Object> map){

map.put("departments", departmentDao.getDepartments());

map.put("employee", **new** Employee());

**return** "Input";

}

@RequestMapping("/emps")

**public** String list(Map<String, Object> map){

map.put("employees", employeeDao.getAll());

**return** "list";

}

实现了简单的重定向，没有连接数据库，直接集合增删改查