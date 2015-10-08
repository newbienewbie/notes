# WordPress Template Layer 2: Template Tags 汇总


内置的template tags大致包括：

* 包含文件类
    * `get_header`
    * `get_sidebar`
    * `get_footer`
    * `comments_template`
    * `get_search_form`
* 基本信息类
    * 博客信息类
        * `bloginfo`
        * `bloginfo_rss`
        * `get_bloginfo`
        * `get_bloginfo_rss`
    * 文章信息类
        * `the_ID`
        * `the_title`和`the_title_rss`
        * `the_title_attribute`
        * `single_post_title`
        * `the_content`和`the_content_rss`
        * `the_excerpt` 和`the_excerpt_rss`
        * `wp_link_pages`
        * 前后文章
            * `posts_nav_link`
            * `next_post_link` 和`next_posts_link`
            * `previous_post_link`和`previous_posts_link`
        * 前后图片
            * `next_image_link`
            * `previous_image_link`
        * `sticky_class`
        * 所属分类 
            * `the_category`和`the_category_rss`
            * `the_tags`
        * `the_meta`
    * 作者信息类 
        * `the_author`
        * `the_author_link`
        * `the_author_posts`
        * `the_author_posts_link`
        * `the_author_posts_meta`
        * `wp_list_authors`
        * `wp_dropdown_users`
        * `the_modified_author`    #显示最后修改的作者
    * 评论标签类
        * `wp_list_comments`
        * `comments_number`
        * `comments_link`
        * `comments_rss_link`
        * `comments_popup_script`
        * `comments_popup_link`
        * `comment_ID`
        * `comment_id_fields`
        * `comment_author`
        * `comment_author_link`
        * `comment_author_email`
        * `comment_author_email_link`
        * `comment_author_url`
        * `comment_author_url_link`
        * `comment_author_ip`
        * `comment_author_ip`
        * `comment_type`
        * `comment_text`
        * `comment_excerpt`
        * `comment_date`
        * `comment_time`
        * `comment_form_title`
        * `comment_author_rss`
        * `comment_text_rss`
        * `comment_link_rss`
        * `permalink_comments_rss`
        * `comment_reply_link`
        * `cancel_comment_reply_link`
        * `previous_comments_link`
        * `next_comments_link`
        * `paginate_comments_link`
* 时间日期类
    * `the_time`
    * `the_date`
    * `the_date_xml`
    * `the_modified_time`
    * `the_modified_date`
    * `single_month_title`
    * `get_the_time`
    * `get_day_link`
    * `get_month_link`
    * `get_year_link`
    * `get_calendar`
* 分类标签类
    * `is_category`
    * `the_category`
    * `the_category_rss`
    * `single_cat_title`
    * `category_description`
    * `wp_dropdown_categorys`
* 文章tag类
    * `is_tag`
    * `the_tags`
    * `tag_description`
    * `single_tag_title`
    * `wp_tag_cloud`
    * `wp_generate_tag_cloud`
    * `get_the_tags`
    * `get_the_tag_list`
    * `get_tag_link`
* 列表类
    * `wp_list_authors`
    * `wp_list_categories`
    * `wp_list_pages`
    * `wp_list_bookmarks`
    * `wp_list_comments`
    * `wp_get_archives`
    * `wp_page_menu`
    * `wp_dropdown_pages`
    * `wp_dropdown_categorys`
    * `wp_dropdown_users`
* 登录登出类
* 一般标签类
*   链接类
    * 编辑链接类
    * 固定链接类
    * 链接管理类
    * 引用标签类






