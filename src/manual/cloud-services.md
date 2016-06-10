#使用云服务
借助七牛等云存储服务和多说等社会化评论工具，可以让站点负荷进一步减轻，迁移更便捷。

##设置七牛云存储
首先，您需要在 **七牛云存储** 注册帐号并获得 Access Key 和 Security Key，然后创建一个可供公开访问的空间（bucket）。七牛云存储官网： http://www.qiniu.com/

然后，登陆 bW 后台，进入“系统服务”页面设置。填写必要信息，打开“附件上传到七牛”来启用日志编辑器的附件云存储功能。
![](http://xiaofengtest.qiniudn.com/storage/73b0a6c3?imageView2/2/w/500)

设置完毕后，也可随时将博客完整数据备份上传到七牛。点击该页相应按钮即可完成。上传的文件在七牛空间里，是一个文件名以 storage/ 开头的 zip 文件。


##设置多说
多说的设置更加简单。首先到[多说官网](http://duoshuo.com/)注册登录，然后点击导航条上的 “后台管理”。

稍加自定义设置以使显示效果更美观。“自定义文本”中，对无评论、1条评论和N条评论的文字做如下设置：
![](http://xiaofengtest.qiniudn.com/storage/c08c71fe?imageView2/2/w/500)

然后到 bW 后台进入“系统服务”页面，在 **多说短域名** 中，填入您在多说上设置的短域名。也可在下面的界面上找到（.duoshuo.com 前的部分）。
![](http://xiaofengtest.qiniudn.com/storage/e7267a05?imageView2/2/w/500)


##设置Disqus
Disqus 的设置与多说基本一致。只需到 https://disqus.com 注册并至 https://disqus.com/admin/create/ 页面开通个人 Disqus 服务。然后，到 bW 的后台“系统服务”中做相应的设置即可。

请注意，如果您同时填写了多说短域名和 Disqus 短域名，则将优先载入多说服务。如果要使用 Disqus 的服务，请将多说短域名留空即可。

##其他云服务
会在后期视情况增加其他云服务的支持。