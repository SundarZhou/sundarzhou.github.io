---
layout: post
title: "傻瓜式-配置阿里云服务器（centos7.7 64位）部署Rails项目"
date: 2022-05-12 20:16:54 +0800
categories: ROR 服务器
---

## SSH免密登录设置

1. 进入服务器
  ```
  ssh root@xx.xx.xx.xx -p 22
  ```
2. 增加用户
  ```
  adduser deploy  
  ```
3. 为新增的deploy 设置密码
  ```
  passwd deploy 
  ```
4. 命令输入 `visudo`, 在root ALL=(ALL) ALL 添加
  ```
  second ALL=(ALL)    ALL
  ALL=(ALL) NOPASSWD: ALL  # sudo不用输入密码
  ```
5. 编辑文件 /etc/ssh/sshd_config，禁用root登录修改 PermitRootLogin 为 no
  ```
  PermitRootLogin no
  ```
6. 重新加配置文件吧，使上面的命令生效
  ```
  service sshd restart
  ```
7. 在本机增加`alias`(这里用的是zsh), 然后重新加载配置文件 source ~/.zshrc
  ```
  deploy="ssh deploy@xx.xx.xx.xx -p 22"
  ```
8. 本机生成秘钥，放入服务器，然后本地直接输入 `deploy` 就可以登录上服务器 
  ```
  ssh-keygen -t rsa #直接一路回车，按默认设置就行
  cat .ssh/id_rsa.pub
  ```
9. `deploy` 登录服务器,还没有将密钥放入服务器，需要输入密码
10. 创建放置密钥目录
  ```
  mkdir ~/.ssh
  ```
11. 创建放置密钥文件
  ```
  touch ~/.ssh/authorized_keys
  ```
13. 调整权限
  ```
  chmod 700 ~/.ssh
  chmod 600 ~/.ssh/authorized_keys
  ```
14. 在本地复制密钥并写入服务器的文件中
 ```
 cat ~/.ssh/id_rsa.pub | deploy "cat >> ~/.ssh/authorized_keys"  
 ```

## 安装zsh和oh-my-zsh
1. zsh安装
  ```
  sudo yum install -y zsh
  ```
 
2. zsh安装
  ```
  chsh -s /bin/zsh
  ```
3. 手动安装oh-my-zsh, 如果连接不上可以试下重启服务

```
cd ~
git clone git://github.com/robbyrussell/oh-my-zsh.git  # 从github上把oh-my-zsh clone下来到根目录下
~/.oh-my-zsh # 再在根目录下copy一份.zshrc配置
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

## 更新系统应用及安装必须的应用
```
sudo yum -y update 
sudo yum groupinstall -y development # 安装必要的开发工具
sudo su -c 'rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm';
sudo yum -y update # 再次更新
sudo yum -y install git-core openssl openssl-devel subversion curl curl-devel gcc-c++ patch readline readline-devel zlib zlib-devel libyaml-devel libffi-devel make bzip2 autoconf automake libtool bison sqlite-devel libxml2 libxml2-devel libxslt libxslt-devel libtool # 安装一堆依赖
```

## 使用rvm安装ruby
### 安装 GPG keys:
```
gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
```
如果你遇到问题或想了解更多，请查看安全问题

### 安装 RVM:
```
curl -sSL https://get.rvm.io | bash -s stable
```
### 同时安装ruby 和 rails
```
curl -sSL https://get.rvm.io | bash -s stable --rails
```
也可以使rbenv安装Ruby 和 Rails，可以参考 https://ruby-china.org/wiki/rbenv-guide

## 安装Nginx 
```  
cd /usr/local/src/
sudo wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
sudo tar -zxvf pcre-8.35.tar.gz
cd pcre-8.35
sudo ./configure
sudo make
sudo make install
cd /usr/local/src/
sudo wget http://nginx.org/download/nginx-1.10.0.tar.gz
sudo tar -zxvf nginx-1.10.0.tar.gz
cd nginx-1.10.0
sudo ./configure --prefix=/usr/local/nginx
sudo make
sudo make install
sudo ln -s /usr/local/lib/libpcre.so.1 /lib
```

## 配置Nginx
### 配置的nginx.conf文件
```
cd /usr/local/nginx/conf  #
sudo vim nginx.conf
```
`nginx.conf` 如下

```
      user  deploy deploy;
worker_processes  auto;
        #error_log  logs/error.log;
        #error_log  logs/error.log  notice;
        #error_log  logs/error.log  info;

        #pid        logs/nginx.pid;

events {
    worker_connections  1024;
    multi_accept on;
    use epoll;
}


http {
    include       mime.types;
    default_type text/html;
    charset UTF-8;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 50m;

    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;

    keepalive_timeout 20;
    client_header_timeout 20;
    client_body_timeout 20;
    reset_timedout_connection on;

    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 256k;

    gzip  on;
    gzip_min_length  256;
    gzip_buffers     4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 4;
    gzip_types       text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_vary on;
    gzip_proxied        any;
    gzip_disable        "MSIE [1-6]\.";

    include vhost/*.conf;
}
```
新建一个文件夹 来存放 server的配置文件 以便 不同server存放到不同文件中，再导入到`nginx.conf`中，到这里就差不多算是完成了配置 
```
sudo mkdir vhost  
sudo vim vhost/web.conf
```

`web.conf` 如下

```
upstream web {
    server unix:/tmp/unicorn.web.sock fail_timeout=0;
}

server {
    listen       80;
    server_name  sundar.net.cn 45.78.38.241;
    root  /home/sundar/ruby/web/public;

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        expires      30d;
    }

       location ~ .*\.(js|css)?$ {
        expires      30d;
    }

    location ~* ^(/assets|/favicon.ico) {
        access_log        off;
        expires           max;
    }

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_set_header   X-Forwarded-Host $host;
        proxy_set_header   X-Forwarded-Server $host;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_buffering    on;

        if (!-f $request_filename) {
            proxy_pass http://web;
            break;
        }
    }

    error_page   500 502 503 504  /500.html;
    location = /500.html {
        root /home/sundar/ruby/web/public;
    }

    error_log /home/sundar/ruby/web/log/error.log;
    access_log off;
}
```
             
### 配置文件 测试/启动/关闭/重启命令
```
sudo /usr/local/nginx/sbin/nginx -t  #对配置文件进行测试
sudo /usr/local/nginx/sbin/nginx  #启动
sudo /usr/local/nginx/sbin/nginx -s stop #关闭
sudo /usr/local/nginx/sbin/nginx -s reload #重启
```
## 安装mysql
```
sudo rpm -ivh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

### 确认Mysql仓库成功添加
```
sudo yum repolist all | grep mysql | grep enabled
```
 如果展示像下面,则表示成功添加仓库:
```
mysql-connectors-community/x86_64  MySQL Connectors Community    enabled:     51
mysql-tools-community/x86_64       MySQL Tools Community         enabled:     63
mysql57-community/x86_64           MySQL 5.7 Community Server    enabled:    267
```
### 开始安装Mysql5.7
```
sudo yum -y install mysql-community-server
```
### 启动Mysql
#### 启动
```
sudo systemctl start mysqld
```
#### 设置系统启动时自动启动
```
sudo systemctl enable mysqld
```
#### 查看启动状态
````
sudo systemctl status mysqld
````
#### Mysql的安全设置

 CentOS上的root默认密码可以在文件/var/log/mysqld.log找到，通过下面命令可以打印出来
```
cat /var/log/mysqld.log | grep -i 'temporary password'
```
 执行下面命令进行安全设置，这个命令会进行设置root密码设置，移除匿名用户，禁止root用户远程连接等
```
mysql_secure_installation
```
### 设置数据库编码为utf8

打开配置文件
```
sudo vim /etc/my.cnf
```
在[mysqld]，[client]，[mysql]节点下添加编码设置
```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
```
#### 重启Mysql即可
```
sudo systemctl restart mysqld
```
## 上传布署项目
### 创建文件夹来存放项目,
```
cd ~
mkdir ruby
cd ruby     
```
克隆项目并命名为对应的名字，看你配置文件(/usr/local/nginx/conf/vhost/web.conf)，其实应该先执行这部分再去创建配置文件
```
git clone XXXXXXXX web  
cd /usr/local/nginx/conf/vhost
touch web.conf # 对应你的项目名起的配置文件名,上面配置nginx时已经写好了
```


### 安装yarn
```
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
sudo yum install yarn
```
### 执行rails的项目
```
cd web  # 进入项目目录
bundle install --without development test
cp config/database.yml.sample config/database.yml
bundle exec rails db:create RAILS_ENV=production
bundle exec rails db:migrate RAILS_ENV=production
bundle exec rails default:system_user RAILS_ENV=production # 一些rake任务
bundle exec rails assets:precompile RAILS_ENV=production
bundle exec rails kindeditor:assets RAILS_ENV=production
```
## 记得修改config/unicorn.rb

## 一些方便更新项目时使用的alias
```
alias rails_start='bundle exec unicorn_rails -E production -c config/unicorn.rb -D'
alias rails_stop='kill $(cat tmp/pids/unicorn.pid)'
alias rails_restart='kill -9 $(cat tmp/pids/unicorn.pid) && bundle exec unicorn_rails -c config/unicorn.rb -E production -D’
```

ps： 80端口可能不是默认打开，需要上服务器安全组打开
