Django之连接操作数据库
===
* 创建数据库( 需要自己创建 )，在mysql中创建
```mysql
create database django1 charset utf8;  # 创建一个django1的数据库，编码格式为utf8
```
* 创建app模块，在对应的项目路径下执行
```
python manage.py startapp app01  # 创建一个app01的应用程序
```
## 一、配置数据库
* 修改配置文件==settings.py==，找到==DATABAASES==
```mysqlshujuk
DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.mysql',  # 连接数据库的类型，如mysql
		'HOST': '127.0.0.1',  # 主机地址
		'PORT': 3306,  # 端口号
		'NAME': 'anage',  # 数据库名
		'USER': 'root',  # 登录账号
		'PASSWORD': '123456',  # 登录密码
	}
}
```
## 二、将app信息增加到配置文件
* 修改配置文件==settings.py==，找到==INSTALLED_APPS==
```python
INSTALLED_APPS = [
	...
	'app01.apps.App01Config',  # 将app01模块添加( 格式为：name.apps.NameConfig(首字母大写) )
]
```
## 三、创建表( 利用ORM，关系映射表 )，
* app01应用程序中的models.py模块，专门用来创建数据表
```python
# 必须继承models.Model类
class UserInfo(models.Model):
    name = models.CharField(max_length=30)
    passwd = models.CharField(max_length=30)

    def __str__(self):
        """
        用于后期admin管理提供数据可视化
        """
        return '{} - {}'.format(self.name, self.passwd)
```
* 在对应项目路径下执行命令
```python
python manage.py makemigrations  # 创建migrations目录，用于记录对models.py文件的改动
```
```python
python manage.py migrate  # 将models.py文件中的变动映射到数据库
```
## 四、增加数据
* 在views.py中创建删除指令
```python
def add_(request):
	title = models.User.objects.create(name='xiaoxiao', sex='2', hobby='computer')
	return HttpResponse('data add')
```
## 五、删除数据
* 在前端页面创建删除连接
```django
<a href="delete/?id={{ i.id }}">删除</a>  # ?id=del_id　可以得到需要删除的ID
```
* 在views.py中创建删除指令
```python
def udelete(request):
    del_id = request.GET.get('id', None)
    if del_id:
        Book.objects.filter(id=del_id).delete()
        return redirect('/book/')
```
## 六、更新数据
* 在前端页面创建更新连接
```django
<a href="delete/?id={{ i.id }}">删除</a>  # ?id=del_id　可以得到需要删除的ID
```
* 在views.py中创建修改指令
```python
def update_(request):
	title = models.User.objects.filter(name='笑笑').update(name='小木')
	return HttpResponse('data update')
```

## 七、导出数据
```python
python manage.py dumpdata > data.json
```
## 八、导入数据
```python
python manage.py loaddata data.json
```