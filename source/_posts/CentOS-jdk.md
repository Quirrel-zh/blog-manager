---
title: 如何在centOS中配置环境等
date: 2024-08-29 15:36:40
tags: 
    - CentOS
    - Linux
categories:
    - Linux
toc: true
---

<i style='color:gray;'>本文章于 2023 年学习时记录，无法保证准确性</i>

### centOS 安装以及宿主机的连接

#### 安装 centOS

​ 根据 iso 安装需要版本的 centOS，可选带有桌面或者无桌面的内核版本
<!-- more -->

---

#### 检查 centOS 版本以及网络状态


```Linux
cat /etc/redhat-release # 查询centOS的版本
ip addr #查询ip 默认状态下看不到ip 但是需要记住网卡名称例如ens33
```



ip 地址默认看不到，需要我们设置一下网络配置

```Linux
cd /etc/sysconfig/network-scripts #切换目录
vi ifcfg-ens33 # 修改指定的网卡
```

进入到 ifcfg-ens33 之后需要修改`BOOTPROTO`以及`ONBOOT`两行

```
BOOTPROTO = "dhcp" #更改为静态ip
ONBOOT = "yes" #更改网卡状态开启
# 随后按下esc 输入:wq回车退出
```

此时 ip 地址即可查看

```
ip addr
# 找到目标网卡inet后的ip地址
```

<i>此时可以用 ping www.baidu.com来验证网络是否已连接</i>

---

#### 使用 Xshell 以及 Xftp 进行连接

在 Xshell 新建会话，用户名使用 centOS 中查到的 ip，用户名为 root 密码为初始创建
的密码

同理 Xftp 也是一样

---

### JDK 以及 mysql 的环境安装配置

#### JDK 的安装

##### 在 usr 目录下新建一个 jdk 的文件

```
cd /usr/local # 切换目录到指定位置
mkdir jdk # 创建名为jdk 的文件
```

---

##### 使用 Xftp 进行宿主机与虚拟机间的文件传输

将 jdk 压缩包传到/usr/local/jdk 中

使用代码解压

```
tar zxvf "jdk文件名.tar.gz"
```

> 不同压缩包的解压方式
>
> ```
> tar –xvf file.tar //解压 tar包
> tar -xzvf file.tar.gz //解压tar.gz
> tar -xjvf file.tar.bz2 //解压 tar.bz2
> tar –xZvf file.tar.Z //解压tar.Z
> unrar e file.rar //解压rar
> unzip file.zip //解压zip
> ```

---

##### 编辑环境变量，以及验证 jdk

```
vi /etc/profile # 编辑环境变量
# 在文末加入路径代码
# jdk config
JAVA_HOME=/usr/local/jdk/jdk1.8.0_391
CLASS_PATH=$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASS_PATH PATH
```

编辑完成按下 esc 键退出编辑模式，再按下**`:wq`**回车退出保存

使用`source /etc/profile`使配置生效

验证 java

```
java -version
jps
```

---

#### mySQL 的安装

##### 使用 Xftp 将需要安装的 mysql 传输到虚拟机中

---

##### 解压改名以及移动

<i>文件名以mysql-5.7.43.tar.gz示例</i>

```
tar zxvf mysql-5.7.43.tar.gz # 解压
mv mysql-5.7.43 mysql # 改名为mysql
mv mysql /usr/local # 移动到常用目录中
```

---

##### 创建一个新的 mysql 用户

1. 删除系统已有的数据库 否则会导致 mysql 安装冲突

   ```
   # 删除mariadb
   rpm -qa|grep mariadb # 列出所有mariadb数据库，有多少下面删多少
   rpm -e --nodeps "要删除的数据库"
   ```

2. 创建一个不需要登录的 mysql 账号，给对应的目录授予权限

   ```
   # 创建不需要登录的mysql账号
   groupadd mysql
   useradd -r -g mysql -s /bin/false mysql
   # 给予权限
   chown -R mysql:mysql /usr/local/mysql
   mkdir -p /data/mysql
   chown -R mysql:mysql /data/mysql
   ```

---

##### 安装依赖

```
yum -y install libaio # 从云端安装需要的依赖包
```

---

##### 初始化 mysql 以及配置环境变量

1. 对 mysql 进行初始化

   ```
   cd /usr/local/mysql/bin # 切换到mysql的bin目录
   ./mysqld --user=mysql --initialize # 进行初始化
   ```

   初始化后会显示初始的 root 账户密码，在最后一行`localhost:`后，后续需要使用这
   个密码进行登录 mysql

2. 添加环境变量

   首先进入到环境变量配置文件中

   ```
   vi /etc/profile
   ```

   在环境变量最后加入 mysql 路径

   ```
   # mysql congfig
   MYSQL_HOME=/opt/apps/mysql
   PATH=$PATH:{MYSQL_HOME}/bin
   export MYSQL_HOME PATH
   ```

3. 添加到系统服务

   进入 mysql.server 进行编辑

   ```
   cd support-files
   vi mysql.server
   ```

   进入到编辑页面后修改`basedir`、`datadir`以及`mysql_pid_file_path`的量

   ```
   basedir=/usr/local/mysql
   datadir=/data/mysql/data
   mysql_pid_file_path=/data/mysql/mysql.pid
   ```

   编辑完后按下`esc`退出编辑模式，再按下`:wq`保存退出

4. 设置开机启动，更改目录权限

   ```
   # 给予权限
   cp mysql.server /etc.init.d/mysql
   chmod 755 /etc/init.d/mysql
   # 启动mysql
   service mysql start
   chkconfig --add mysql
   chkconfig --list
   ```

---

##### 修改 root 密码

使用上面准备好的 root 密码登录到 mysql

```
mysql -uroot -p
# 回车后输入刚刚得到的密码
```

> 注意：如果此时显示未找到 mysql 命令时，是因为系统默认查找/usr/bin 下的命令，如
> 果命令不在此处，需要映射一个链接到/usr/bin 目录下
>
> 1、找到 mysql 的安装路径
>
> ```
> whereis mysql
> ```
>
> 2、确定安装路径在`/usr/local/mysql/bin/mysql`下后，创建一个软连接
>
> ```
> ln -s /usr/local/mysql/bin/mysql /usr/bin
> ```
>
> 3、再次进行登录尝试
>
> ```
> mysql -uroot -p
> ```

登录后修改密码为指定密码

```
alter user 'root'@'localhost' identified by '123456'; # 修改密码为123456 注意代码末需要带分号
flush privileges; # 刷新权限
```

---

#### tomcat 的安装与部署

##### 使用 Xftp 将需要安装的 Tomcat 传输到虚拟机中

在`/usr/local/`目录下新建名为`tomcat`的文件夹

将需要安装的 Tomcat 通过 Xftp 移动到此处

---

##### 解压文件

```
tar -zxvf apache-tomcat.tar.gz
```

---

##### 启动 tomcat

1. 首先进入到 tomcat 目录下的`bin`目录

   ```
   cd /usr/local/tomcat/apache-tomcat.9.0.83/bin/
   ```

2. 根据指令启动 tomcat

   ```
   ./startup.sh
   ```

3. 进入到日志验证是否正常启动，并查看开放端口号

   ```
   # 进入日志目录
   cd ..
   cd logs/
   # 查看日志最后100行
   tail -100f catalina.out
   ```

   如果日志内没有报错，查看**开始协议处理句柄["http-nio-8080"]**中后四位的端口号
   ，这里以 8080 举例。

   ***

##### 测试访问 tomcat

1. 去浏览器访问`ip:端口号`的网址 示例`192.168.174.130:8080`在地址栏直接访问

   但是这时会无法访问，需要进行防火墙的开放

2. 开启防火墙端口

   ```
   # 开启指定端口的防火墙  以8080举例
   firewall-cmd --zone=public --add-port=8080/tcp --permanent

   #命令含义： -zone #作用域 -add-port=8080/tcp # 添加端口 格式为：端口/通讯协议  -permanent # 永久生效，没有此参数重启后失效

   # 重启防火墙
   firewall-cmd --reload
   ```

3. 访问 tomcat

   此时去浏览器地址页输入`192.168.174.130:8080`即可访问 tomcat 主页

   ***

##### 部署项目到 tomcact

1. 关闭 tomcat

   部署先需要先关闭 tomcat

   ```
   # 返回到bin目录
   cd ..
   cd bin/
   # 输入关闭指令
   ./shutdown.sh
   ```

2. 导入项目

   将事先准备好的项目通过 Xftp 导入到 tomcat 目录下的`/webapps/`下，下面以
   examtest 为例。

3. 访问项目

   重新开启 tomcat

   ```
   ./startup.sh
   # 再去日志看看是否正常开启
   cd ..
   cd logs/
   tail -100f catalina.out
   ```

   成功开启后进入个人项目页面

   > 浏览器访问 192.168.174.130:8080/examtest/
   >
   >  项目名是什么，端口后就是什么

---
