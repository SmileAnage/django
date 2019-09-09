Django之admin
===
## 一、创建管理员账户
```python
python manage.py createsuperuser
```
## 二、新增admin页面
* 在urls.py文件中增加admin页面
```python
from django.urls import path
from django contrib import admin

urlpatterns = [
	...
	path('admin/', admin.site.urls),
]
```
## 三、开启服务，在网址后面输入admin即可进入管理界面

## 四、admin中显示数据库信息
* admin.py 文件中配置
```python
from django.contrib import admin
from user.models import *

admin.site.register(Book)  # 增加表名才可以显示操作
```
## 五、admin 增加信息不可为空，除非建表的时候
```python
	字段增加该属性，blank=True, null=True
```