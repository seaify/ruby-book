## 简介
web端直接使用默认的devise登录就可以了，而ios，android这种api模式的需要其它的认证方法，网络上看到最多的就是基于token的


## 参考资料
1. [devise 3.1移除Token Authenticatable](http://blog.plataformatec.com.br/2013/08/devise-3-1-now-with-more-secure-defaults/), 注意只是devise把之前的实现TokenAuthenticatable移除了，但并不是对基于token的认证方式的不认可
2. [devise 的token验证例子](https://github.com/plataformatec/devise/wiki/How-To:-Simple-Token-Authentication-Example)
3. [devise token代码示例](https://gist.github.com/josevalim/fb706b1e933ef01e4fb6)
4. [devise token验证的gem devise_token_auth](https://github.com/lynndylanhurley/devise_token_auth)
5. [devise token验证的gem simple_token_authentication](https://github.com/gonzalo-bulnes/simple_token_authentication)