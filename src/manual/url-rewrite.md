#URL Rewrite 伪静态
bW 支持借助 [Apache 的 mod_rewrite模块](https://httpd.apache.org/docs/current/mod/mod_rewrite.html) 或者 nginx 的 nginx.conf 来实现伪静态 URL。

在启用URL重写前，单篇日志、分类、主页第2页的URL形式分别如下：

>http://your.site/bw/read.php/title-of-your-article/
>http://your.site/bw/category.php/name/
>http://your.site/bw/index.php/2/

在 **后台 > 参数设置 > 外观 > URL重写** 中启用以后，URL将被美化为如下形式：

>http://your.site/bw/post/title-of-your-article/
>http://your.site/bw/category/name/
>http://your.site/bw/index/2/

##Apache URL重写规则

可以在后台 **后台 > 参数设置 > 外观 > URL重写**  处点击相应的链接获取最新规则。以下规则可供Apache Rewrite模块使用。

```
RewriteEngine on
RewriteBase YOUR_PATH_HERE

RewriteCond %{REQUEST_FILENAME} -f [OR]
RewriteCond %{REQUEST_FILENAME} -d
RewriteRule ^.*$ - [L]

RewriteRule ^index/([0-9]+)/?$ index.php/$1/ [QSA,L]
RewriteRule ^category/([^/]+)/([0-9]+)/?$ category.php/$1/$2/ [QSA,L]
RewriteRule ^category/([^/]+)/?$ category.php/$1/ [QSA,L]
RewriteRule ^post/([^/]+)/?$ read.php/$1/ [QSA,L]
RewriteRule ^tag/([^/]+)/([0-9]+)/?$ tag.php/$1/$2/ [QSA,L]
RewriteRule ^tag/([^/]+)/?$ tag.php/$1/ [QSA,L]
RewriteRule ^page/([^/]+)/?$ page.php/$1/ [QSA,L]
```

将以上规则中的 YOUR_PATH_HERE 替换为程序所在目录即可，在上例中，是 /bw/ 。


##nginx URL重写规则

以下规则可供nginx使用。

```
  if (!-e $request_filename)
   {
      rewrite ^/YOUR_PATH_HERE/index/([0-9]+)?/$ /YOUR_PATH_HERE/index.php?go=/$1 last;
      rewrite ^/YOUR_PATH_HERE/post/(.+)?/$ /YOUR_PATH_HERE/read.php?go=/$1 last;
      rewrite ^/YOUR_PATH_HERE/category/(.+)?/$ /YOUR_PATH_HERE/category.php?go=/$1 last;
      rewrite ^/YOUR_PATH_HERE/category/(.+)/([0-9]+)?/$ /YOUR_PATH_HERE/category.php?go=/$1/$2 last;
      rewrite ^/YOUR_PATH_HERE/tag/(.+)?/$ /YOUR_PATH_HERE/tag.php?go=/$1 last;
      rewrite ^/YOUR_PATH_HERE/tag/(.+)/([0-9]+)?/$ /YOUR_PATH_HERE/tag.php?go=/$1/$2 last;
      rewrite ^/YOUR_PATH_HERE/send.php/(.+)$ /YOUR_PATH_HERE/send.php?go=/$1 last;
      rewrite ^/YOUR_PATH_HERE/admin.php/(.+)$ /YOUR_PATH_HERE/admin.php?go=/$1 last;
      rewrite ^/YOUR_PATH_HERE/page/(.+)?/$ /YOUR_PATH_HERE/page.php?go=/$1 last;
 }
```

将以上规则中的 YOUR_PATH_HERE 替换为程序所在目录即可，在上例中，是 bw 。
