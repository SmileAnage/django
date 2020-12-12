# Django部署

### 一、前言

基于centos7部署

### 二、部署环境

| 软件   | 版本    |
| ------ | ------- |
| python | 3.7.2   |
| django | 1.10.8  |
| nginx  | 1.15.12 |
| mysql  | 5.6.50  |
| uwsgi  | 2.0.19  |

### 三、环境安装

#### 1、python安装

* 下载安装包

  ```
  wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz
  ```

* 解压并进入目录

  ```
  tar -zxvf Python-3.7.2.tar && cd Python-3.7.2
  ```

* 指定安装路径

  ```
  ./configure --prefix=/usr/local/python3
  ```

* 编译并安装

  ```
  make && make install
  ```

* 建立软链接

  ```
  ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3
  ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3
  ```

* pip安装环境包

  ```
  pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple/ -r requirements.txt
  ```
  
* 参考文献

  * [python安装依赖][https://new.qq.com/omn/20200725/20200725A0NYGM00.html]
  * [centos更换阿里源][https://blog.csdn.net/inslow/article/details/54177191]

#### 2、nginx安装

* 下载安装包

  ```
  yum install -y nginx
  ```

* 进入nginx配置路径下

  ```
  cd /etc/nginx/conf.d
  ```

* 新建配置文件

  ```
  sudo vi web.conf
  ```

* 配置文件内容

  ```nginx
  server {
          listen 80;
      	# 域名
          server_name kjy.baijcn.com;
          charset utf-8;
  
          client_max_body_size 75M;
  
          location /static {
                  root /service/cloud-platform/sunmo;
          }
  
          location / {
          		# 将接收到的请求转到9800端口的uwsgi处理
                  uwsgi_pass 127.0.0.1:9800;
                  include /etc/nginx/uwsgi_params;
          }
          return https://kjy.baijcn.com;
  }
  
  # 配置https(添加ssl证书)
  server {
          listen 443 ssl;
          server_name kjy.baijcn.com;
  
          # 证书地址
          ssl_certificate /etc/nginx/certs/4834410_kjy.baijcn.com.pem;
          ssl_certificate_key /etc/nginx/certs/4834410_kjy.baijcn.com.key;
  
          ssl_session_cache    shared:SSL:50m;
          ssl_session_timeout  1d;
  
          location / {
                  uwsgi_pass 127.0.0.1:9800;
                  include /etc/nginx/uwsgi_params;
          }
          location /static {
                  root /service/cloud-platform/sunmo;
          }
  }
  ```

* 检查nginx配置是否存在问题

  ```
  nginx -t
  ```

* nginx命令集

  * 查看当前状态

    >`service nginx status`
    >
    >>or
    >
    >`systemctl status nginx`

  * 启动

    >`service nginx start`
    >
    >> or
    >
    >`systemctl stop nginx`

  * 停止

    >`service nginx stop`
    >
    >>or
    >
    >`systemctl stop nginx`

* 参考文献

  * [Nginx安装SSL配置HTTPS][https://segmentfault.com/a/1190000022673232]

#### 3、uwsgi安装

* 安装uwsgi

  ```
  pip3 install uwsgi
  ```

* 在README文件同目录下创建文件夹用来处理nginx转来的请求

  * 创建文件夹

    ```
    sudo mkdir uwsgi_config
    ```

  * 设置文件夹访问权限并进入该目录

    ```
    chmod 777 uwsgi_config/ && cd uwsgi_config/
    ```

  * 创建uwsgi.ini文件并编辑

    ```
    sudo vi uwsgi.ini
    ```

  * 配置uwsgi.ini文件

    ```
    [uwsgi]
    # uwsgi处理请求的端口
    socket=127.0.0.1:9800
    # Django项目manage.py的路径
    chdir=/service/cloud-platform/sunmo
    module=sunmo.wsgi:application
    wsgi-file=sunmo/wsgi.py
    processes=9
    threads=3
    max-requests=5000
    master=true
    daemonize=uwsgi.log
    uid=1000
    gid=2000
    pidfile=uwsgi.pid
    ```

* uwsgi命令集

  * 启动

    ```
    uwsgi --ini uwsgi.ini
    ```

  * 停止

    ```
    uwsgi --stop uwsgi.pid
    ```

* 参考文献

  * [uwsgi设置][https://www.jianshu.com/p/0e85cf58e677]
  * [uwsgi测试是否配置成功]['https://uwsgi-docs-zh.readthedocs.io/zh_CN/latest/tutorials/Django_and_nginx.html']

### 四、补充参考文献

* [配置网络环境][https://blog.csdn.net/qq_37598011/article/details/80710715]
* [网卡配置][https://blog.csdn.net/helloonego/article/details/84038554]
* [查看防火墙状态][https://blog.csdn.net/ytangdigl/article/details/79796961]
* [设置静态IP][https://blog.csdn.net/johnnycode/article/details/40624403]
* [文件传输][https://blog.csdn.net/gatieme/article/details/51673229]
* [虚拟机安装的centos系统忘记密码][https://www.cnblogs.com/jfaith/p/12145494.html]