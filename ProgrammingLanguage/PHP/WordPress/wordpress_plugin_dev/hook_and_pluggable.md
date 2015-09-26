# WordPress钩子和Pluggable

## Action和Filter

与Symfony/EventDispather组件、GuzzleHttp 的事件分发机制类似，WordPress提供了一种过程式的钩子实现。当特定事件发生时，WordPress就会根据当前事件对所关联的函数进行调用。

布置钩子的常用函数：

* `do_action`
* `do_action_ref_array`
* `apply_filters`
* `apply_filters_ref_array`


将函数钩到事件:

```PHP
add_action ( 'hook_name', 'your_function_name', [priority], [accepted_args_num=1] );
add_filter ( 'hook_name', 'your_function_name', [priority], [accepted_args_num=1] );
```

还可以将事件对应的函数移除：

```PHP
remove_action('action_hook_name','action_function_name');
remove_filter('filter_hook_name','filter_function_name');
```

## Pluggable 函数

除了钩子（actions 和 filters）,另外一个修改WordPress行为的方法是覆盖Pluggable的函数。

在`wp-includes/pluggable.php`文件中，预定义了一小部分函数。此文件会在加载完插件后加载:

```PHP
// wp-settings.php

//...

register_theme_directory( get_theme_root() ); // Register the default theme directory root

// Load active plugins.
foreach ( wp_get_active_and_valid_plugins() as $plugin ) {
	wp_register_plugin_realpath( $plugin );
	include_once( $plugin );
}
unset( $plugin );

// Load pluggable functions.
require( ABSPATH . WPINC . '/pluggable.php' );
require( ABSPATH . WPINC . '/pluggable-deprecated.php' );

//...

```

此`pluggable.php`文件中定义的函数，都采用类似以下的形式，以保证只有当所有的插件都被加载后仍未定义的函数，这部分预定义的函数才会被定义。

```PHP
// wp-includes/pluggable.php

if ( !function_exists('wp_get_current_user') ) :
    function wp_get_current_user() {
        global $current_user;
        get_currentuserinfo();
        return $current_user;
    }
endif;

```
尽管如此，Pluggable functions are no longer being added to WordPress core. All new functions instead use filters on their output to allow for similar overriding of their functionality. 



