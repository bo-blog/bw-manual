#主题制作手册
本文档将帮助您了解 bW 的主题制作方法。您需掌握HTML和CSS的基本知识，并对PHP的语法有一点了解。

##主题包结构
一个主题由若干文件组成，并存放于 theme/ 文件夹下的一个**自定义文件夹**内，例如 mytheme 。该文件夹名称建议只采用字母和数字。所有PHP、CSS、JS等文本文件必须采用无BOM的UTF-8编码。
每个主题文件包含若干 .php 文件，这些文件定义了前后台界面中的页面呈现（下称区块）。一个区块可以是整个页面结构（如page.php），也可以是页面中的一个组成部分。

一个主题的文件夹结构如下：
**<定义>**
info.php - 主题的描述文件
icon.jpg - 主题的预览缩略图，尺寸规定为260*220px

**<区块文件>**
article.php - 阅读文章时展示的区块
page.php - 前台页面的主框架
summary.php - 文章摘要展示的区块
plainpage.php - 手机扫码登陆等简单页面的主框架
singlepage.php - 单页的区块
components.php - 主题调用的一些杂项区块
error.php - 出错提示页主框架

**<后台区块文件，default主题专用>**
admin.php - 后台页面的主框架
adminarticles.php - 后台文章管理页区块
admincenter.php - 后台设置页区块
admindashboard.php - 后台首页区块
adminextensions.php - 后台扩展管理页区块
adminservices.php - 后台服务管理页区块
adminwriter.php - 编辑器区块

**<其他文件>**
主题还可以包含其他必要的文件，比如字体、CSS、JS、图片、Flash文件等。

请注意，上述所有的文件，**只有**描述文件 info.php是必需的。当bW程序需要载入一个主题下的某个指定的区块文件时，如果所需的文件不存在，程序将载入default主题下的相应区块文件。所以，在大多数情况下，你只需要制作一个包含所要修改的区块文件的主题，而其他文件都可以忽略。
为了确保随着程序升级，后台的功能可以正常使用而不需要同步升级主题，不允许第三方主题修改后台区块文件。无论主题文件夹中是否包含后台区块文件，程序都将且只会载入 default 主题下的后台区块文件。

##区块文件语法
###变量引用
不同的区块支持不同的变量。在bW主题中，我们使用以下方法引用一个变量：
`[[::变量名]]`
（两个前中括号，两个冒号，变量名，两个后中括号）

例如，在任何区块里，你想要引用表示网站名的变量 `[[::siteName]]`，假如你的网站叫“新的冒险”，那么代码
```
<h1>[[::siteName]]</h1>
```
将让你得到
```
<h1>新的冒险</h1>
```

bW中一些常用的变量是全局性的，意味着在任何区块文件中都能被引用（例如网站名，网站的根网址等），另一些变量则只在适用的区块中有效（例如，某篇文章的阅读次数只在文章的阅读页区块和摘要区块有效）。

全局变量和各区块的特定变量，请参阅后续的章节。

###变量计算
一些变量可以进行一些基本的运算，形式如下：
```
[[::变量名, 运算方法, 参数]]
```

例如，我们可以对表示某篇日志的发布时间的变量（`[[::aTime]]`）进行时间日期格式化运算，该运算的方法名为`dateFormat`，接受的参数是我们指定的时间日期格式。代码：
```
<span>发布时间：[[::aTime, dateFormat, Y年m月d日 H:i]]</span>
```
可以得到：
```
<span>发布时间：2015年1月1日 20:12</span>
```

可用的运算方法，请参阅后续的章节。

###循环
部分变量是一个数组，我们需要通过循环将这个数组转换为一组结构相似的HTML代码，例如一个列表，若干篇日志摘要等。

循环结构语法如下：
```
[[::loop, 可供循环的数组名]]
需要循环的代码
[[::/loop]]
```

比如，`[[::navigation]]`是一个数组变量，该数组表示了由所有分类组成的导航条，其每个成员都拥有`[[::aCateURLName]]` （分类ID）、`[[::aCateDispName]]` （分类名）等变量。

代码：
```
<ul>
[[::loop, navigation]]
<li id="nav-[[::aCateURLName]]">[[::aCateDispName]]</li>
[[/loop]]
</ul>
```
将生成一组无序列表，形如：
```
<ul>
<li id="nav-category1">分类1</li>
<li id="nav-category2">分类2</li>
<li id="nav-category3">分类3</li>
</ul>
```


###载入区块
在一个区块中常常需要载入另一个区块。载入的方法是：
```
[[::load, 要载入的区块名]]
```
比如在default主题的page.php里，你可以看到我们用
```
[[::load, article]]
```
载入了一个叫做article的区块并将其全部内容插入到这里。

载入的区块可以存在于一个独立的区块文件中，也可以在杂项文件 components.php中定义。

在读取到 `[[::load, abc]]` 时，如果在当前的主题文件夹下不存在abc.php，那么程序将尝试载入default主题下的同名文件。当default主题下也没有abc.php时，程序将分别在当前文件夹和default的 components.php 中寻找 `$parts['abc']` 的值作为区块的代码。

换言之，一个abc区块的载入优先顺序是这样的：
主题下的abc.php > default/abc.php > 主题下 components.php 中的  $parts['abc'] > default/components.php 中的 $parts['abc'] > 找不到该区块（载入失败）

###语言包
所有在语言包中定义的词组或句子，可以通过
```
[[=变量名]]
```
来调用。通过这种调用形式来输出词组可以最大程度的使主题通用于各种语言。

语言包的变量都是全局性的。在任何区块里调用
```
[[=page:Home]]
```
都可以获得当前语言包的“首页”这个词组。

##info.php定义
info.php 定义了主题的名称等详细信息，是唯一一个必要文件。

该文件的格式和意义为：
```
<?php
$theme['themeName']="Name";  //可供人类理解的主题名字
$theme['themeAuthor']="bW";  //主题作者名
$theme['themeUrl']="http://www.a.com";  //主题作者的网站链接
$theme['themeVersion']="1.0";  //主题当前的版本号
$theme['guid']="com.bo-blog.name";  //主题的唯一ID，建议采用反域名格式
```
其中，版本号、唯一ID只在主题进入主题商店（筹）后才有实际的意义。

##常用变量
了解这些变量后，便可以随意制作主题了。
首先，介绍全局变量。

###常规变量
- authorIntro - 网站主人的一句话简介
- metaData - 自动生成该页的MetaData，包含了网站简介以及文章tag等。
- siteURL - 网站根地址。所有链接都必须采用带有这个根地址的绝对路径！
- siteName - 网站名
- canonicalURL - 当前页的标准链接
- authorName - 站长姓名
- activeNav - 当前激活状态的分类的ID（若有）
- prevPageLink - 有多页存在时，前一页的链接
- firstPageLink - 有多页存在时，第一页的链接
- finalPageLink - 有多页存在时，最后一页的链接
- nextPageLink - 有多页存在时，下一页的链接
- gotoPageLink - 有多页存在时，某一页的链接
- errorMessage - 出错时的错误信息

###URL片段变量
为了支持 [URL Rewrite 伪静态](./url-rewrite.html)，引入了一系列URL片段变量，用于在主题中灵活地兼容开启和关闭了URL Rewrite的状态。
- linkPrefixIndex - 首页，开启URL Rewrite时输出 index，关闭时输出 index.php（nginx下会输出Ugly URL以提高兼容性，下略）
- linkPrefixCategory - 分类，开启URL Rewrite时输出 category，关闭时输出 category.php
- linkPrefixArticle - 单篇日志，开启URL Rewrite时输出 post，关闭时输出read.php
- linkPrefixTag - 标签，开启URL Rewrite时输出 tag，关闭时输出 tag.php
- linkPrefixSend - 提交表单，始终输出 send.php
- linkPrefixAdmin - 后台，始终输出 admin.php
- linkPrefixPage - 单页，开启URL Rewrite时输出 page，关闭时输出 page.php
- linkConj - 多个URL传递参数之间的连接符，非Ugly URL状态时输出 ?，Ugly URL状态时输出 &

###常用于主框架的变量
**navigation** - 由分类信息组成的导航条，为循环变量，其中每个成员是一个导航条项目，包含下列子变量：
- aCateURLName - 分类的ID
- aCateDispName - 分类的名称


**articlesummary** - 由多篇日志摘要组成的日志信息流，为循环变量，其中每个成员是一篇日志摘要。对于单篇日志摘要的格式定义，一般写在 summary.php 里。一篇摘要包含下列自变量：
- aTitle - 日志标题
- aTime - 日志发布时间，为 YYYY-MM-DD HH:MM:SS 格式。支持运算方法 dateFormat，用于自定义日期显示格式，格式参数写法参考 PHP 的 date() 函数。
示例：
```
<span>发布时间：[[::aTime, dateFormat, Y年m月d日 H:i]]</span>
```
可以得到：
```
<span>发布时间：2015年1月1日 20:12</span>
```
- aCateURLName - 该篇日志所属分类的分类ID，用于构建指向分类的链接。以下是正确的指向一个分类页面的链接的形式：[[::siteURL]]/[[::linkPrefixCategory]]/[[::aCateURLName]]/
- aCateDispName - 该篇日志所属分类的分类名称
- aContent - 该日志的正文。支持运算方法 formatText，用于决定是输出正文全文还是仅输出摘要。参数为“less”时输出摘要，参数“full”时输出全文。即使用
```
[[::aContent, formatText, less]]
```
输出摘要。

- aID - 日志的唯一ID。支持运算方法 hasMore，此运算需结合 aContent 的 formatText 运算方法使用。当 aContent 在摘要之外并无更多的内容时，使用 hasMore 运算方法可以输出在“参数”中指定的代码。例如：
```
[[::aID, hasMore, 已显示了全文]]
```
如果这篇日志已经显示完毕没有更多内容，则显示“已显示了全文”。否则则不显示任何内容。这个方法通常用于决定是否显示“阅读更多”的链接。

- aTags - 日志的标签。
支持运算方法 hasTags。当日志设定有标签时，输出“参数”中指定的内容。
支持运算方法 formatTags。当日志设定有标签时，用“参数”中指定的格式来显示每一个标签。此处的参数可以引用其他的变量，但变量需用 [::xxx] 来引用。
将 aTags 的两个运算方法相结合，可以做到在有标签时输出标签行。示例：
```
[[::aTags, hasTags, <h4 class="tagsRow">]] [[::aTags, formatTags, <span class="oneTag"><a href="[::siteURL]/[::linkPrefixTag]/[::tagInURL]/">#[::tagValue]</a></span>]][[::aTags, hasTags, </h4>]]
```
这里，对于每一个标签，支持这些变量：
- tagInURL - 是经过base64处理的可用于URL中的标签名称。
- tagValue - 是标签名称。




（未完待续）