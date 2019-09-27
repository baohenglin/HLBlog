# 第27条：使用“class-continuation分类”隐藏实现细节

**要点：**

* 通过“class-continuation分类”向类中新增实例变量。
* 如果某属性在主接口中声明为“只读”，而类的内部又要用设置方法修改此属性，那么就在“class-continuation分类”中将其扩展为“可读写”。
* 把私有方法的原型声明在“class-continuation分类”里面。
* 若想使类所遵循的协议不为人所知，则可于“class-continuation分类”中声明。
