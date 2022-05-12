---
layout: post
title: "傻瓜式-配置阿里云服务器（centos7.7 64位）部署Rails项目"
date: 2022-05-12 20:16:54 +0800
categories: ROR 服务器
---

一、SSH免密登录设置
1.ssh root@xx.xx.xx.xx -p 22  //服务器ip、端口
2.adduser deploy   //增加用户
3.passwd deploy    //为新增的deploy 设置密码
4.visudo        /////在root ALL=(ALL) ALL下 添加 second ALL=(ALL)    ALL 和 ALL=(ALL) NOPASSWD: ALL sudo不用输入密码
5.vi /etc/ssh/sshd_config     ////禁用 root登录 PermitRootLogin no
6.service sshd restart       ////重新加配置文件吧，使上面的命令生效
7.(在本地) 增加alias // 这里用的是zsh
   subl ~/.zshrc # 增加 alias  second=“ssh deploy@39.99.192.160 -p 22”
   重新加载配置文件 source ~/.zshrc
8. (在本地)ssh-keygen -t rsa    //生成秘钥，然后就可以不用密码直接登录啦。有了就直接 cat .ssh/id_rsa.pub  ，然后复制就好了
9.在本地直接输入 deploy 就可以登录上服务器 
10.mkdir ~/.ssh   //放置密钥嘛 
11.touch ~/.ssh/authorized_keys    //总要有个文件夹放吧
13. chmod 700 ~/.ssh
14. chmod 600 ~/.ssh/authorized_keys
15.（在本地）cat ~/.ssh/id_rsa.pub |second "cat >> ~/.ssh/authorized_keys"    //在本地复制密钥并写入服务器的文件中，，，另一种方法用（cat ~/.ssh/id_rsa.pub | ssh username "cat >>~/.ssh/authorized_keys”）

二、安装zsh和oh-my-zsh
1.  zsh安装
   sudo yum install -y zshrel
2. 将zsh设置成默认的shell 
    chsh -s /bin/zsh
        
- 3、手动安装oh-my-zsh：
# 从git上把oh-my-zsh clone下来到根目录下 git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh # 再在根目录下copy一份.zshrc配置 cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

     如果上面连接不上可以试下重启服务

三、更新系统应用 及 安装必须的应用
1. sudo yum -y update  更新
2. sudo yum groupinstall -y development 安装必要的开发工具
3. sudo su -c 'rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm'; //安装epel，然而我不知道这是什么东西
4. sudo yum -y update 再次更新
5. sudo yum -y install git-core openssl openssl-devel subversion curl curl-devel gcc-c++ patch readline readline-devel zlib zlib-devel libyaml-devel libffi-devel make bzip2 autoconf automake libtool bison sqlite-devel libxml2 libxml2-devel libxslt libxslt-devel libtool //安装一堆东西
四、rbenv安装Ruby 和 Rails 等（参考 https://ruby-china.org/wiki/rbenv-guide）
1.安装 rbenv
在 osx 上可以直接用 homebrew 安装, 下面是手动安装过程. (不用 zsh 的童鞋注意替换成自己的 shell 配置文件)
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
# 用来编译安装 ruby
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
# 用来管理 gemset, 可选, 因为有 bundler 也没什么必要
git clone git://github.com/jamis/rbenv-gemset.git  ~/.rbenv/plugins/rbenv-gemset
# 通过 rbenv update 命令来更新 rbenv 以及所有插件, 推荐
git clone git://github.com/rkh/rbenv-update.git ~/.rbenv/plugins/rbenv-update
# 使用 Ruby China 的镜像安装 Ruby, 国内用户推荐
git clone git://github.com/AndorChen/rbenv-china-mirror.git ~/.rbenv/plugins/rbenv-china-mirror

然后把下面的代码放到 ~/.bash_profile 里

export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"

注意 Unubtu 请放到 ~/.bashrc 里, zsh 用户是 ~/.zshrc

然后重开一个终端就可以执行 rbenv 了.


使用

安装 ruby

rbenv install --list  # 列出所有 ruby 版本
rbenv install 1.9.3-p392     # 安装 1.9.3-p392
rbenv install jruby-1.7.3    # 安装 jruby-1.7.3

列出版本

rbenv versions               # 列出安装的版本
rbenv version                # 列出正在使用的版本

设置版本

rbenv global 1.9.3-p392      # 默认使用 1.9.3-p392
rbenv shell 1.9.3-p392       # 当前的 shell 使用 1.9.3-p392, 会设置一个 `RBENV_VERSION` 环境变量
rbenv local jruby-1.7.3      # 当前目录使用 jruby-1.7.3, 会生成一个 `.rbenv-version` 文件

解决 MacOSX 下编译 Ruby 无法在 irb 中输入中文的方法


安装 homebrew 的 readline，再进入源码目录，重新编译安装 readline.bundle

brew install readline
brew link readline
cd src/ruby-1.9.3-p392/ext/readline
ruby extconf.rb --with-readline-dir=$(brew --prefix readline)
make install

rbenv 下的解决办法

brew install readline
CONFIGURE_OPTS="--disable-install-doc --with-readline-dir=$(brew --prefix readline)" rbenv install 1.9.3-p392

有关 ruby-2.0.0-p0 在 OS X 10.7+ 上的问题，参见：https://github.com/rbenv/ruby-build/wiki


其他

rbenv rehash                 # 每当切换 ruby 版本和执行 bundle install 之后必须执行这个命令
rbenv which irb              # 列出 irb 这个命令的完整路径
rbenv whence irb             # 列出包含 irb 这个命令的版本

rbenv 下使用 gemset


简介


rvm 中最方便的就是 gemset。实际上，rbenv 通过插件也可以使用 gemset


安装


MacOS 下使用 brew 的话，一个命令就搞定

brew install rbenv-gemset


使用


创建一个 gemset

rbenv gemset create 1.9.3-p392 ruby-china
                       参数 1       参数 2
- 以上命令中，参数 1 是已安装的 ruby 版本，参数 2 是 gemset 的名字

具体使用方法

1. 在项目的根目录下，把想要使用的 gemset 名字放到 .rbenv-gemsets 文件中即可。有 .rbenv-gemsets 文件的情况下执行 bundle 命令就是对设置好的 gemset 进行操作

	echo ruby-china > .rbenv-gemsets

1. 当前目录下没有 .rbenv-gemsets 文件的情况下，执行 bundle 命令（没有指定 --path 参数的情况）时，是对当前版本的 ruby 版本的 gemset 。也就相当于 rvm 中 global gemset 的作用了
安装nodejs
sudo yum install -y nodejs
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
gem sources -l
https://gems.ruby-china.com
# 确保只有 gems.ruby-china.com
bundle config mirror.https://rubygems.org https://gems.ruby-china.com
gem install bundler rails unicorn

五、安装Nginx    
1. cd /usr/local/src/
2. sudo wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
3. sudo tar -zxvf pcre-8.35.tar.gz
4. cd pcre-8.35
5. sudo ./configure
6. sudo make
7. sudo make install
8. cd /usr/local/src/
9. sudo wget http://nginx.org/download/nginx-1.10.0.tar.gz
10. sudo tar -zxvf nginx-1.10.0.tar.gz
11. cd nginx-1.10.0
12. sudo ./configure --prefix=/usr/local/nginx
13. sudo make
14. sudo make install
15. sudo ln -s /usr/local/lib/libpcre.so.1 /lib
六、 配置Nginx
1. cd /usr/local/nginx/conf  配置该目录下的nginx.conf文件
2. sudo vim nginx.conf
3. 文件如下(修改成这个文件并修改用户名）
       

4. sudo mkdir vhost   //新建一个文件夹 来存放 server的配置文件 以便 不同server存放到不同文件中，再导入到nginx.conf中  ，                     到这里就差不多算是完成了配置（我是这样想啦接下来是根据相应的项目做的相应的配置）                           
5. sudo vim vhost/backgound.conf  //新建一个以项目名称为名的conf文件……随便命名也行……                                  
6. 该配置文件内容如下：
             

7. sudo /usr/local/nginx/sbin/nginx -t  //对配置文件进行测试
8. sudo /usr/local/nginx/sbin/nginx  //启动
9. sudo /usr/local/nginx/sbin/nginx -s stop //关闭
10. sudo /usr/local/nginx/sbin/nginx -s reload //重启
七、 安装mysql
sudo rpm -ivh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
复制代码
2. 确认Mysql仓库成功添加
sudo yum repolist all | grep mysql | grep enabled
复制代码

如果展示像下面,则表示成功添加仓库:

mysql-connectors-community/x86_64  MySQL Connectors Community    enabled:     51
1. 添加Mysql5.7仓库
mysql-tools-community/x86_64       MySQL Tools Community         enabled:     63
mysql57-community/x86_64           MySQL 5.7 Community Server    enabled:    267
复制代码
3. 开始安装Mysql5.7
sudo yum -y install mysql-community-server
复制代码
4. 启动Mysql
	1. 启动
5. sudo systemctl start mysqld
   复制代码
	2. 设置系统启动时自动启动
6. sudo systemctl enable mysqld
   复制代码
	3. 查看启动状态
		1. sudo systemctl status mysqld
		   复制代码
7. Mysql的安全设置

   CentOS上的root默认密码可以在文件/var/log/mysqld.log找到，通过下面命令可以打印出来

   cat /var/log/mysqld.log | grep -i 'temporary password'
   复制代码

   执行下面命令进行安全设置，这个命令会进行设置root密码设置，移除匿名用户，禁止root用户远程连接等

   mysql_secure_installation
   复制代码
8. 设置数据库编码为utf8
	1. 打开配置文件
9. sudo vim /etc/my.cnf
   复制代码
	2. 在[mysqld]，[client]，[mysql]节点下添加编码设置
10. [client]
    default-character-set=utf8

    [mysql]
    default-character-set=utf8

    [mysqld]
    collation-server = utf8_unicode_ci
    init-connect='SET NAMES utf8'
    character-set-server = utf8
    复制代码
	2. 重启Mysql即可
11. sudo systemctl restart mysqld
七、上传布署项目
1.cd ~
2.mkdir ruby    //创建文件夹来存放项目,
3.cd ruby     
4.git clone XXXXXXXX web  //克隆项目并命名为对应的名字，看你配置文件(/usr/local/nginx/conf/vhost/web.conf)，其实应该先执行这部分再去创建配置文件
 5.cd /usr/local/nginx/conf/vhost
6.touch web.conf    //对应你的项目名起的配置文件名
7.配置文件的内容如下：

8.cd web  //进入项目
9.bundle install --without development test
10.cp config/database.yml.sample config/database.yml
11.bundle exec rails db:create RAILS_ENV=production
12.bundle exec rails db:migrate RAILS_ENV=production
13.bundle exec rails default:system_user RAILS_ENV=production
14.安装yarn
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
sudo yum install yarn
15.bundle exec rails assets:precompile RAILS_ENV=production
16.bundle exec rails kindeditor:assets RAILS_ENV=production
17.bundle exec rails assets:precompile RAILS_ENV=production
18.
19.sudo /usr/local/nginx/sbin/nginx -t  //对配置文件进行测试
20.sudo /usr/local/nginx/sbin/nginx  //启动
21.sudo /usr/local/nginx/sbin/nginx -s stop //关闭
22.sudo /usr/local/nginx/sbin/nginx -s reload //重启
记得改config/unicorn.rb
23.alias rails_start='bundle exec unicorn_rails -E production -c config/unicorn.rb -D'
24.alias rails_stop='kill $(cat tmp/pids/unicorn.pid)'
25.alias rails_restart='kill -9 $(cat tmp/pids/unicorn.pid) && bundle exec unicorn_rails -c config/unicorn.rb -E production -D’
ps： 80端口要自己去安全组打开
