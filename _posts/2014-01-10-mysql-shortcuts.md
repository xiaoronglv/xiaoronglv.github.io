---
title: 终端中快捷登录 MySQL
layout: post
guid: 07hO5huvDsoZ
tags:
  - 
---

以前在终端中登录MySQL，需要输入这么长的一堆命令。

    > mysql -u root -p
    > please enter your password
    > ******

在用户的根目录下添加了一个文件后，我只需要输入mysql，就可自动登录了。

    > mysql
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 100
    Server version: 5.6.10 Source distribution
    
    mysql> 

## 怎么实现？

1. 创建~/.my.cnf

2. 将权限修改为 600

3. 文件内容


    [mysql]  
    user=root  
    password=pass  
    host=localhost  
    
    [mysqldump]  
    user=root  
    password=pass  
    host=localhost  
    
    [mysqldiff]  
    user=root  
    password=pass  
    host=localhost  


参考资料：

1. [StackOverflow: MySQL Utilities](http://stackoverflow.com/questions/16299603/mysql-utilities-my-cnf-option-file)
