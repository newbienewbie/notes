# WordPress Meta Box

meta box 用于为posts，pages，content等增加额外的信息。

## 增加Meta Box的函数

```PHP
add_meta_box( $id, $title, $callback, $page, $context, $priority, $callback_args );
```

其中:

* `$id`              # HTML DOM id
* `$title`           # Meta Box 头部的title 
* `$callback`        # 显示MetaBox的回调函数
* `$page`            # 'post','page',custom post type name
* `$context`         # 'normal','advanced' ,'side'
* `$priority`        # 'high','core','default','low'
* `$callback_args`   # 回调函数的参数

## add_meta_boxes钩子

利用钩子事件`add_meta_boxes`可以方便的完成添加Meta Box动作。



