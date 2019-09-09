Django项目部署
===
## 一．署前须知
* 安装 nginx 服务器
	+ Nginx 是轻量级的高性能web服务器，提供了诸如HTTP代理和反向代理，负载均衡，缓存等一系列重要特征，在实际中使用广泛．
	+ C 编写，执行效率高
	+ nginx 作用：
		- 负载均衡，多台服务器轮流处理请求
		- 反向代理
	+ 原理：
		- 客户端请求 nginx，再由 nginx 请求 uwsgi，运行 django 下的 python 代码

* 安装 uWSGI 服务器网关接口
	+ 使用 ```python manage.py runserver``` 通常只是在开发和测试环境中使用
	+ 当开发结束后，完善的项目代码需要在一个高效稳定的环境中运行，这时可以使用 uWSGI
	+ uWSGI 是 WSGI 的一种，它可以让 Django，Flask 等开发的 web 站点运行其中

*  settings.py 文件内部
	+ DEBUG = False
	
	+ ALLOWED_HOSTS = ['*']
	
	+ STATIC_ROOT = 'Django Admin 后台管理需要的静态文件的绝对路径'
		- e.g. STATIC_ROOT = '/home/tarena/AID1905/smile/static'
	+ 在项目文件路径下执行 admin 静态文件迁移操作
		- python3 manage.py collectstatic
## 二．开始部署
### 1. 安装 nginx
```
	sudo apt-get install nginx
```
* 注：如安装失败，则更换源
	+ 更换源：（先备份）
		- cd /etc/apt/
		- sudo cp sources.list sources.list.bak && vi sources.list
		- 删除其中所有内容，vim 下，删除所有行的命令：1,$d
	+ 更新系统
		- sudo apt-get update
		- sudo apt-get upgrate

#### 配置 nginx
* 修改 nginx 的配置文件：sudo vi /etc/nginx/sites-available/default
	- 在 server 节点下新增 localtion 项，指向 uwsgi 的IP与端口
	```ini
		server {
			...
			location / {
				# 重定向到 127.0.0.1:8000
				uwsgi_pass 127.0.0.1:8000;
				# 将所有的参数转到 uwsgi 下
				include /etc/nginx/uwsgi_params;
			}
			...
		}
	```
	
	- 配置静态文件路径
	```ini
		server {
			...
			location /static {
				# root 静态文件 static 文件夹的绝对路经
				root /home/tarena/AID1905/smile;
				root /home/tarena/AID1905/smile/last;
			}
			...
		}
	```
* nginx 服务控制
```
	sudo /etc/init.d/nginx start | stop | restart | status
	# or 
	sudo service nginx start | stop | restar | status
```

### 2. 安装 uwsgi
```
	sudo pip3 install uwsgi
```
#### 配置 uwsgi
* 增加配置文件(与settings.py同级)：项目文件夹/uwsgi.ini
	+ e.g. vi smile/uwsgi.ini
	```ini
	[uwsgi]
    # 套接字方式的 IP地址:端口号
    socket=127.0.0.1:8000
    # Http通信方式的 IP地址:端口号
    # http=127.0.0.1:8000
    # 项目当前工作目录
    chdir=/home/weimz/.../my_project 这里需要换为项目文件夹的绝对路径
    # 项目中wsgi.py文件的目录，相对于当前工作目录
    wsgi-file=my_project/wsgi.py
    # 进程个数
    process=4
    # 每个进程的线程个数tongji
    threads=2
    # 服务的pid记录文件
    pidfile=uwsgi.pid
    # 服务的目志文件位置
    daemonize=uwsgi.log
	```
* uwsgi 服务控制
	+ 启动服务
		- cd smile  # 项目名
		- sudo uwsgi --ini smile/uwsgi.ini
	+ 停止服务
		- cd smile  # 项目名
		- sudo uwsgi --stop uwsgi.pid
	+ 查看 uwsgi 进程(强制关闭服务)
		- ps -aux | grep uwsgi
		- kill -9 [pid]  # 直接杀死守护进程
