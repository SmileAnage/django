Django打包APP
===
* 打包工具：setuptools、pip

## １．创建说明文档
* 创建一个说明文档==README.rst==，写入下面内容
```
=====
Polls
=====

Polls is a simple Django app to conduct Web-based polls. For each
question, visitors can choose between a fixed number of answers.

Detailed documentation is in the "docs" directory.

Quick start
-----------
文件。大多数Django相关的app
1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'polls',
    ]

2. Include the polls URLconf in your project urls.py like this::

    url(r'^polls/', include('polls.urls')),

3. Run `python manage.py migrate` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/
   to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.
```

## 2. 添加授权声明
* 创建一个==LICENSE==版权申明文档．大多数Django下相关的app都基于BSD版权

## 3. 创建MANIFEST文件
* 默认情况下，只有python的模块个包会被打包进我们的app内．为了包含一些其他文件，比如静态文件static、网页文件templates等非python语言编写的文件，需要创建一个==MANIFEST.in==文件，并写入下面的内容
```
include LICENSE
include README.rst
recursive-include /static *
recursive-include /templates *
```

## 4. 增加md文件(可选)
* 该步骤可选，但是建议将详细的说明文档一起打包，创建一个新的文档==Readme.md==，同时在==MANIFEST.in==文件中加入==include Readme.md==，如果文档为空，将不会被打包进去

## 5. 编写setup.py脚本
* 创建一个==setup.py==文件，包含了编译和安装app的配置细节，这种配置脚本的具体语法，请前往setuptools的官方文档获取详细的教程，以下面为例，在此基础上修改：
```
setup(
	name='打包的包名',
	version='包的版本',
	...
	url='随便指定的网址',
	author='你的名字',
	author_email='你的邮箱',
)
```

* 具体文档为：
```
import os
from setuptools import find_packages, setup

with open(os.path.join(os.path.dirname(__file__), 'README.rst')) as readme:
    README = readme.read()

# allow setup.py to be run from any path
os.chdir(os.path.normpath(os.path.join(os.path.abspath(__file__), os.pardir)))

setup(
    name='django-polls',
    version='0.1',
    packages=find_packages(),
    include_package_data=True,
    license='BSD License',  # example license
    description='A simple Django app to conduct Web-based polls.',
    long_description=README,
    url='https://www.example.com/',
    author='Your Name',
    author_email='yourname@example.com',
    classifiers=[
        'Environment :: Web Environment',
        'Framework :: Django',
        'Framework :: Django :: X.Y',  # replace "X.Y" as appropriate
        'Intended Audience :: Developers',
        'License :: OSI Approved :: BSD License',  # example license
        'Operating System :: OS Independent',
        'Programming Language :: Python',
        # Replace these appropriately if you are stuck on Python 2.
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
        'Topic :: Internet :: WWW/HTTP',
        'Topic :: Internet :: WWW/HTTP :: Dynamic Content',
    ],
)
```

## 6. 执行打包指令
* 在包的路径下，执行==python setup.py sdist==．这将会创建一个dist文件夹，并生成后缀为==.tar.gz==包文件

使用APP包
===
* 实际使用时，我们只需要拿着后置为==.tar.gz==的包文件即可

## 1. 安装
```python
pip install --user .tar.gz包文件
```

## 2. 卸载
```python
pip uninstall 包文件
```














