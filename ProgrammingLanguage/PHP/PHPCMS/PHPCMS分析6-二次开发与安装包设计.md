# PHPCMS分析6-后台二次开发与安装包设计

# PHPCMS后台开发原理

PHPCMS采用了MVC分层架构，对于一个模块前台功能，只需要访问：
```PHP
http://localhost/phpcms/index.php?m=mymodule&c=my_controller&a=init
```
既可以实现加载。但是，如《PHPCMS分析2-Amin原理分析》所述，PHPCMS的设计者认为，用户对后台的管理操作都应该通过PHPCMS提供的admin模块进行。所以，当我们为一个模块开发了后台控制器my_admin，并直接在浏览器中访问如下URL：
```PHP
http://localhost/phpcms/index.php?m=mymodule&c=my_admin&a=init
```
会因pc_hash校验失败而被拒绝访问。

![pc_hash_failed](https://github.com/newbienewbie/notes/raw/master/ProgrammingLanguage/PHP/PHPCMS/img/pc_hash_failed.png)

其机理在于后台控制器继承自admin类，admin的__construct()会调用check_hash方法，当客户端传来的hash失败时就会提示这样的对话框了。

考虑到admin界面的菜单是从menu中加载的、并在用户点击的时候执行的，所以二次开发时，除了需要像前台实现那样提供MVC分层代码，还需要在数据库中的

* 菜单表(menu)里添加相关记录，配置出moduel、controller、action等信息；
* 在管理角色权限表（admin_role_priv）配置相关角色对mca的访问权限。

这样当用户点击后台管理菜单的时候，就会通过JavaScript改变iframe的src属性，从而触发对相关m、c、a的访问。整个过程大致分为三个过程:

* 请求该menu关联url的响应
* 请求当前current_pos
* 还有一个跨域请求被屏蔽了……

![menu_clicked](https://github.com/newbienewbie/notes/raw/master/ProgrammingLanguage/PHP/PHPCMS/img/menu_clicked.png)

所以，当添加一条记录

```SQL
insert into menu 
(id,name,parentid,m,c,a,data,listorder,display,project1,project2,project3,project4,project5)
values
(id,snptest_init,29,snptest,snptest,init,'',0,1,1,1,1,1,1)
```

再新建模块为snptest，控制器snptest，init方法为输出 ``this is admin from snptest``

刷新缓存后访问后台即可：

![admin_dev_demo](https://github.com/newbienewbie/notes/raw/master/ProgrammingLanguage/PHP/PHPCMS/img/admin_dev_demo.png)


# PHPCMS二次开发安装包设计


