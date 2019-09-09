Django模板系统
===
* 本文自定义装饰器都位于 ==app01/templatetags/mytags.py==
* 注 templatetags 是一个python包，带有 __init__.py
## 变量
* views 中的代码:
```python
def template(request):
    age_ = [20, 21, 22]
    name_ = {'name1': 'xiaomu', 'name2': 'xiaoxiao'}

    class Template:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        def __str__(self):
            return '{} - {}'.format(self.name, self.age)

    info1 = Template('小木', 23)
    info2 = Template('笑笑', 22)

    info_list = [info1, info2]
    return render(request, 'info_text.html', {'age': age_, 'name': name_, 'info_list': info_list})
```
* 模板中支持的写法:
```django
<!--取第一个年龄值-->
{{ age.0 }}

<!--取第一个姓名值-->
{{ name.name1 }}

<!--取第一个列表值-->
<ul>
    {% for i in info_list%}
        <li>{{ i }}</li>
    {% endfor %}
</ul>

<!--取第一个列表值中的姓名值-->
<ul>
    {% for i in info_list %}
        <li>{{ i.name }}</li>
    {% endfor %}
</ul>
```
## Filters(过滤器)
* 在Django的模板语言中，通过使用过滤器来改变变量的显示
* 过滤器的语法：{{ value | filter_name:参数 }}
* 使用管道符 "|" 来应用过滤器
* 注意事项：
	+ 1. 过滤器支持＇链式＇操作，即一个过滤器的输出作为另一个过滤器的输入
	+ 过滤器可以接收参数，例如：{{ ss|truncatewords:30 }}，这将显示sss的前30个词，其余用 "..." 表示
	+ 过滤器参数包含空格的话，必须用引号包裹起来．比如使用逗号和空格去连接一个列表中的元素
	+ '|' ==左右没有空格没有空格没有空格==
### default 
* 如果一个变量是false 或者为空，使用给定的默认值．否则，使用变量的值
```django
	{{ value|default:'nothing' }}
```
如果value没有传值或者值为空的话就显示 nothing
### length
* 返回值的长度，作用与字符串和列表
```django
	{{ value|length }}
```
返回value的长度，如 value = ['a', 'b', 'c', 'd']的话，就显示 4
### filesizeformat
* 将值格式化为一个＇文件大小＇格式的文件尺寸(例如 '13kb', '4.1MB', '102bytes', 等等)
```django
	{{ value|filesizeformat }}
```
如果 value 是 123456789 ，输出将会是 117.7MB
### slice
* 切片
```django
	{{ value|slice:'1:3' }}
```
返回字符串被切分后的数据
### date
* 时间日期格式化
```django
	{'value': datetime.datetime.now()}  # 导入datetime模块获取本地时间
	{{ value|date:'Y-m-d H:i:s' }}
```
### safe
* Django的模块中会对HTML标签和JS等语法标签进行自动转义，为了可以显示标签，可以通过装饰器'|safe' 来声明此段代码是安全的不必转义
```django
	{{ value|safe }}
```
例如：value = '<a href='#'>链接</a>' 声明后可直接被转义出来成为一个链接
### floatformat
* 可以调整小数不显示小数点后面的小数
```django
	{{ value|floatformat:'0' }}
```
例如：`price|floatformat:'0'`可以格式化小数不显示小数点后面的数字

### truncatechars
* 如果字符串字符多于指定的字符数量，那么会被截断．截断的字符串将已可翻译的省略号序列('...')结尾
* 参数：截断的字符数
```django
	{{ value|truncatechars:7 }}
```
### 自定义 filter
* 更新需要重新启动服务来识别模块
* 在app下新建python包，名称固定为==templatetags==
* 在该文件夹下新建一个py文件来写filter文件
```python
from django import template

# 实例化一个对象
register = template.Library()


# 装饰器声明这是一个自定义的装饰器
@register.filter(name='info')
def add_info(arg):
    """
    直接传参
    :param arg: 管道符前面的变量
    """
    return "{} good.".format(arg)


@register.filter(name='addinfo')
def addinfo(arg, arg1):
    """
    自定义参数
    :param arg:管道符前面的变量
    :param arg1:冒号后面的变量
    """
    return "{} {}".format(arg, arg1)
```
模板中:
```django
<!--导入自定义过滤器-->
{% load myfilter %}
{{ str1|info }}

{{ str1|addinfo:'再也不见' }}
```
## Tags
### for 循环
* for
```django
<ul>
    {% for name in name_list%}
        <li>{{ name }}</li>
    {% endfor %}
</ul>
```
* for 循环可用的参数
| Variable | Description |
| --- | --- |
| forloop.counter | 当前循环的索引值(从 1 开始) |
| forloop.counter0 | 当前循环的索引值(从 0 开始) |
| forloop.revcounter | 当前循环的倒序索引值(从 1 开始) |
| forloop.revcounter0 | 当前循环的索引值(从 0 开始) |
| forloop.first | 当前循环是不是第一次循环(布尔值) |
| forloop.last | 当前循环是不是最后一次循环(布尔值) |
| forloop.parentloop | 本层循环的外层循环 |
* for ... empty (若列表为空，则显示 empty 内容)
```django
<ul>
{% for user in user_list %}
    <li>{{ user.name }}</li>
{% empty %}
    <li>空空如也</li>
{% endfor %}
</ul>
```
### if 判断
* if 语句支持 and、or、==、>、<、!=、<=、>=、in、not in、is、is not判断
* if ... else
```django
{% if user_list|length > 5 %}
  列表长度大于5
{% else %}
    列表长度小于5
{% endif %}
```
* if ... elif ... else
```django
{% if user_list %}
  用户人数：{{ user_list|length }}
{% elif black_list %}
  黑名单数：{{ black_list|length }}
{% else %}
  没有用户
{% endif %}
```
### with
* 定义一个中间变量，多用于给一个复杂的变量起别名
```django
{% with total=business.employess.count %}
	{{ total }} employess{{ total|pluralize }}
{% endwith %}
```
或
```django
{% with business.employess.count as total %}
	{{ total }} employess{{ total|pluralize }}
{% endwith %}
```
## 母版
* 首先新建母版样式'base.html'
```html
<!DOCTYPE html>
<html lang="en">
<head>daiyou
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div>
        <h1>这是母版</h1>
    </div>
    <div>
        {% block page_main%}
        {% endblock %}
    </div>
</body>
</html>
```
## 继承母版
* 新建页面，直接输入继承指令daiyou即可和被继承页面一致
```django
{% extends 'base.html' %}
```
## 块(block)
* 通过在母版中使用 {% block xxx %} 来定义块
* 在子页面中通过定义母版中的 block 名来对应替换母版中相应的内容
```django
{% block page_main%}
    <h2>我钟意你</h2>
{% endblock %}
```
## 组件
* 将常用的页面内容如导航条，页尾信息等组件保存在单独的文件中，然后在需要使用的地方按如下语法导入即可
```django
	{% include 'base.html'  %}
```
## 静态文件
* 防止静态文件别名 /static/ 发生变化
*  {% static %} 表示的就是静态文件访问路径
*  {% static '静态资源路径' %}
```django
	{% load static %}  # 导入静态文件
	<img src='{% static "imgs/tx.jpeg" %}' />  
```
## simple_tag
* 和自定义 filter 类似，只不过接受更灵活的参数
* 定义注册 simple_tag
```python
from django import template

# 实例化一个对象
register = template.Library()


@register.simple_tag(name='plus')
def plus(arg1, arg2, arg3):
"""
返回列表对象，相比 filter 可以传 2 个以上的参数
"""
return "{} {} {}".format(arg1, arg2, arg3)
```
* 使用自定义simple_tag
```django
{% load myfilter %}
<h1>爱好：{% plus '计算机' '游玩' '购物' %}</h1>
```
## inclusion_tag
* 多用于返回 html 代码片段
* 定义注册 inclusion_tag
```python
from django import template

# 实例化一个对象
register = template.Library()


@register.inclusion_tag('result.html')
def show_results(num):
    """
    循环显示指定语句执行次数
    """
    num = 1 if num < 1 else int(num)
    data = ['第{:0>3}项'.format(i) for i in range(1, num + 1)]
    return {'data': data}
```
* 将代码存放在一个干净的 html 网页中
```django
<ul>
  {% for choice in data %}
    <li>{{ choice }}</li>
  {% endfor %}
</ul>
```
* 在需要显示的页面导入使用
```django
{% load myfilter %}
{% show_results 10 %}
```