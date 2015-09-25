# 保存插件的数据到数据库

大多数WordPress插件都需要获取管理员或用户输入的一些信息，并保存在会话中，以便在过滤器函数(filter)、动作函数(action)和模板函数(template)中使用。若需要在下次会话中继续使用这些信息，就必须将它们保存到WordPress数据库中。以下是将插件数据保存到数据库的4种方法：

1. 使用WordPress的"选项"机制。
2. 使用文章元数据（又名自定义域）。
3. 使用自定义分类。
4. 创建一个新的，自定义的数据库表。这种方式适合保存那些与个人文章、页面或附件无关的，会随着时间逐渐增长的，并且没有特定名称的数据。关于如何使用，你可以阅读Creating Tables with Plugins以获取更多信息

## WordPress选项机制

WordPress有一个"选项"机制，适合储存少量静态的、具有特定名称的数据(通常是网站所有者在创建插件时设置的一些初始化参数，并且以后很少会进行改动) 。
选项的值可以是字符串、数组，甚至是PHP对象（当然，PHP对象在保存时必须能够被序列化或转换成字符串，检索的时候也必须能够被反序列化）。选项的名称必须是字符串，且必须是唯一的，这样才能够确保它们不会和WoredPress或其它插件产生冲突。

对应数据库中数据表`{$wpdb->prefix}_options`。该表结构类似于：

```
mysql> select * from wp_options limit 25;
+-----------+---------------------------+-----------------------------+----------+
| option_id | option_name               | option_value                | autoload |
+-----------+---------------------------+-----------------------------+----------+
|        11 | comments_notify           | 1                           | yes      |
|        12 | posts_per_rss             | 10                          | yes      |
|        13 | rss_use_excerpt           | 0                           | yes      |
|        14 | mailserver_url            | mail.example.com            | yes      |
|        15 | mailserver_login          | login@example.com           | yes      |
|        16 | mailserver_pass           | password                    | yes      |
|        17 | mailserver_port           | 110                         | yes      |
|        18 | default_category          | 1                           | yes      |
|        19 | default_comment_status    | open                        | yes      |
|        20 | default_ping_status       | open                        | yes      |
|        21 | default_pingback_flag     | 1                           | yes      |
|        22 | posts_per_page            | 10                          | yes      |
|        23 | date_format               | F j, Y                      | yes      |
|        24 | time_format               | g:i a                       | yes      |
|        25 | links_updated_date_format | F j, Y g:i a                | yes      |
+-----------+---------------------------+-----------------------------+----------+
```

选项机制的几个主要函数：

* `add_option($name,$value,$deprecated,$autoload)`
* `get_option($name)`
* `update_option($name,$value)`

通常情况下，最好能够对插件选项的数量进行一下精简。例如，如果有10个不同名称的选项需要保存到数据库中，那么，就可以考虑将这10个数据作为一个数组，并保存到数据库的同一个选项中。 

## 文章元数据

这种方式与第一种方案类似，但是与具体的post相关联，故而适合保存与post相关的数据。

对应数据表`{$wpdb->prefix}_postmeta`,基本的数据结构类似于：

```
mysql> select * from wp_postmeta limit 15 ;
+---------+---------+-----------------------------+-----------------------------+
| meta_id | post_id | meta_key                    | meta_value                  |
+---------+---------+-----------------------------+-----------------------------+
|       1 |       2 | _wp_page_template           | default                     |
|       2 |       5 | _edit_last                  | 1                           |
|       3 |       5 | _edit_lock                  | 1441571672:1                |
|       4 |       7 | _edit_last                  | 1                           |
|       5 |       7 | _edit_lock                  | 1441572102:1                |
|       6 |       8 | _edit_last                  | 1                           |
|       7 |       8 | _edit_lock                  | 1441572197:1                |
|       8 |      10 | _menu_item_type             | custom                      |
|       9 |      10 | _menu_item_menu_item_parent | 0                           |
|      10 |      10 | _menu_item_object_id        | 10                          |
|      11 |      10 | _menu_item_object           | custom                      |
|      12 |      10 | _menu_item_target           |                             |
|      13 |      10 | _menu_item_classes          | a:1:{i:0;s:0:"";}           |
|      14 |      10 | _menu_item_xfn              |                             |
|      15 |      10 | _menu_item_url              | http://localhost/wordpress/ |
+---------+---------+-----------------------------+-----------------------------+

```

涉及到的一些基本函数为：

* `add_post_meta()`
* `get_post_meta()`
* `update_post_meta()`
* `delete_post_meta()`


## Custom Taxonomy

WordPress默认内置了4种Taxonomies:

1. Category      #往往是写文章之前就预定义好的
2. Tag           #往往拥有多个tag，可在写文章时即时生成
3. Link Category #往往只在内部使用，可用来categorize links
4. Post Formats  #meta信息，可以用来定制文章的呈现形式。

除此之外，WordPress还允许创建自己的taxonomies。

这种方式适合保存那些需要分门别类存放的数据，如用户信息、评论内容以及用户可编辑的数据等，特别适合于当你想要根据某个类型去查看相关的文章和数据的情况。




## 建立额外的数据表

除了使用现有的数据库模式之外，还可以添加自己的数据表。如著名的插件WooCommerce就添加了定制的数据表：

```
+-------------------------------------------------+
| Tables_in_wordpress                             |
+-------------------------------------------------+
| wp_woocommerce_api_keys                         |
| wp_woocommerce_attribute_taxonomies             |
| wp_woocommerce_downloadable_product_permissions |
| wp_woocommerce_order_itemmeta                   |
| wp_woocommerce_order_items                      |
| wp_woocommerce_tax_rate_locations               |
| wp_woocommerce_tax_rates                        |
| wp_woocommerce_termmeta                         |
+-------------------------------------------------+

```

### create schema

### update schema

### 日常操作

可借助于WPDB类全局类对象$wpdb提供的方法完成。

```PHP
$wpdb->insert(
    $table_name,
    array(
        'time'=>current_time('mysql'),
        'name'=>$welcome_name,
        'text'=>$welcome_text
    )
);
```





