# PHPCMS分析2-admin原理分析

PHPCMS的通用后台程序还是非常漂亮的，这篇笔记将对其实现原理进行分析。

## 后台管理页面布局结构与技术原理

### 后台管理页面布局
PHPCMS后台管理页面总体上分为

* 顶部(主要含顶部菜单)
* 左部(主要含左部菜单）
* 右侧上部（当前位置）
* 右侧下部（内嵌iframe）

四大区域，如下图所示![phpcms_admin_layout](https://github.com/newbienewbie/notes/raw/master/ProgrammingLanguage/PHP/PHPCMS/img/PHPCMS_admin_layout.png)


### 后台管理页面原理综述
后台管理页面从HTML结构上分为四大块

    * ul id="top_menu"
    * div id='leftMain'(利用index::left_menu方法获取）
    * span id=current_pos    (利用admin::current_pos方法获取)
    * iframe id=rightMain   (利用javascript动态改变iframe的src属性)

即每当访问index.php?m=admin的时候，会载入一个通用的index管理外壳（含顶部菜单、左部菜单、当前位置及一个iframe框架）。当用户点击管理外壳的顶部菜单或者左侧菜单时候，都会改变iframe的src属性，从而载入具体的内容页面。
由于使用了iframe元素，这些具体的iframe内容页面是独立于后台管理界面的。所以应该通过
* 服务端动态把pc_hash写入iframe内容页面
* 利用客户端动态获取pc_hash

两种方法之一，避免pc_hash校验失败。

对于具体的内容页面，头部往往还有对应于左侧相关菜单的子菜单，这是利用admin::submenu()方法获取的
为了代码重用，PHPCMS实现了一个admin管理类。


## admin管理类源码分析

admin类提供的方法大致分为

* 构造器方法
* 校验方法check_admin()、check_priv()、check_ip()、check_hash()
* 用于获取后台模板的位置的admin_tpl()
* 用于获取管理菜单数组的admin_menu()
* 用于获取当前位置的current_pos()
* 用于获取内嵌iframe内容页面的子菜单HTML代码的submenu()
* 其他功能方法，如日志方法etc.

### 构造器
主要是完成一系列诸如是否为管理员、是否有权限等检查操作：
```PHP
public function __construct() {

    self::check_admin();    //检查用户是否已经登录
    self::check_priv();    //检查该用户是否有该URL的权限（根据admin_role_priv_model表）
    pc_base::load_app_func('global','admin');
    if (!module_exists(ROUTE_M)) 
        showmessage(L('module_not_exists'));
    self::manage_log();
    self::check_ip();    //检查是否为被禁止的IP
    self::lock_screen(); 
    self::check_hash();    //检查pc_hash是否为会话中的pc_hash
    if(pc_base::load_config('system','admin_url') && 
        $_SERVER["HTTP_HOST"]!= pc_base::load_config('system','admin_url')
        )
    {
        Header("http/1.1 403 Forbidden");
        exit('No permission resources.');
    }
}
```

### 检查函数
### 管理模板的位置与管理菜单的数组
### 当前位置
### 子菜单


## index.php控制器源码分析
控制器类index继承自admin类
### 左侧菜单实现原理


## iframe嵌入文件的header源码分析
