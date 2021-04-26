
# 如何高效的使用 Git

## Push failed: Unable to access 'https://github.com/': OpenSSL SSL_read: Connection was reset, errno 10054
输入下面两条指令取消代理信息的配置
```
git config --global --unset http.proxy   #取消http代理
git config --global --unset https.proxy  #取消https代理
```

## github认证方式
Github 本身提供了多种认证方式，所有开发人员可以各取所需。

* SSH，这是最原始的方式，如果使用git bash只要按照官方文档一步一步配置就好了
小心坑：SSH有可能需要配置代理，否则无法解析服务器域名。错误如下：

ssh: Could not resolve hostname github.com: no address associated with name
解决办法：给SSH以及git 客户端配置代理。

* HTTPS，这也是比较方便的方式，但是每一次都需要输入用户名和密码。
小心坑：本机的SSL证书不是正规机构颁发的，验证失败。错误如下：

fatal: unable to access 'https://github.com/owner/repo.git/': SSL certificate problem: unable to get local issuer certificate

解决办法：将Git的SSL验证关闭，命令如下。
 git config --global http.sslVerify false
 
* Access Token，我个人认为最为便捷的方式之一，不失安全性。

https://help.github.com/articles/creating-an-access-token-for-command-line-use/
使用方法：

1)从Settings页面生成唯一的Token

2) 手动拼接出远程仓库的地址，比如：https://$GH_TOKEN@github.com/owner/repo.git

3）从以上地址克隆或使用git remote add 的方式关联本地仓库，之后都不需要输入用户名和密码信息。

# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 