# PHPCMS基础架构分析（1）

PHPCMS整体设计思路，还是遵循基本的MVC模式。

## 路由功能与控制器的分离

PHPCMS访问index.php文件时，会调用
```PHP
pc_base::creat_app();
```
方法，而此方法只是简单加载系统类application。

从功能上说，application类起到了一个路由功能，其构造函数__construct()会调用init()方法，根据传入的相关URL参数去调用对应的module下相关controller的特定action方法。

module、controller、action对应参数m、c、和a。如果不提供action参数，则会默认调用控制器的init()方法。

例如url为``index.php?m=product&c=index&a=lists``会调用product模块下的index类中的lists方法。

## 数据模型加载

在控制器中利用语句
```PHP
$md=pc_base::load_model('your_model');``
```
可以加载相关模型类，系统的模型类在libs/classes/model.class.php中定义，各模块下模型定义在model/文件下，并继承自系统的model类。


## 视图层模板加载
对于控制器模板，基本思路还是利用
```PHP
include $your_template_file_name;
```
但是PHPCMS根据前后台的不同，在自身文件结构目录上自定义两个求得路径的函数

* template($modulee,$file) 获取指定module前台模板
* admin_tpl($file) 获取当前module的后台模板


