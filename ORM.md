Django中的ORM
===
## ORM介绍：object relational mapping 对象关系映射
* 把面向对象中的类和数据表一一对应，通过操作类和对象，对数据白哦实现数据操作，不需要写SQL语句，由ORM框架生成
* Django实现了ORM的一个框架，在项目与数据之间起桥梁作用
* Django数据库开发步骤如下： 

	* a. 在models.py定义模型类
	* b. 生成*迁移文件：python manage.py makegirations
	* c. 执行迁移生成数据库：python manage.py girate
## 模型类设计：
* 在应用models.py中编写模型类，必须继承与models.Model类
* 在模型类中，定义属性，生成对应的数据库表字段
		属性名 = models.字段类型(字段选项) 
* 字段类型：

	+ AutoField：自动增长，通常不需要指定，自动创建名为id的自动增长属性
	
	+ CharField：字符串，必须指定的参数：max_length 最大字符个数
	
	+ TextField：文本字段
	
	+ IntegerField：整型
	
	+ BooleanField：布尔型
	
	+ DateField：[auto_now=False, auto_now_add=False]
	  + auto_now 表示自动设置该字段为最后一次修改的时间，默认为False
	  + auto_now_add 表示自动设置该字段为创建时的时间，默认为False
	  + 两者互斥，不能同时使用
	
	+ DateTimeField：日期时间
	
	+ FloatField：浮点数
	
	+ ForeignKey：外键，建立一对多关系

