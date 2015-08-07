# rails的优势之处在哪呢？
## 1. 内置支持rails console，能够方便的进行测试，针对model和controller，这比直接去查看数据库要好太多了,如
```ruby
chuck@chucks-MacBook-Pro:~/seaify/ipool/proxy-backend(master|…) % rails console  
Loading development environment (Rails 4.2.0)
irb(main):001:0> ids = [1, 8, 9]
=> [1, 8, 9]
irb(main):002:0> ProxyDomain.where(:id => ids).update_all(:banned => 0, :banned_time => nil)
  SQL (0.8ms)  UPDATE `proxy_domains` SET `proxy_domains`.`banned` = '0', `proxy_domains`.`banned_time` = NULL WHERE `proxy_domains`.`id` IN (1, 8, 9)
=> 3
```

## 2. rails的后台日志, 能清晰的看到http带过来的参数，以及是被哪个controller的哪个函数处理的，同时也能清晰看到执行过的sql语句。这些将为程序出错时，定位原因提供很大的帮助。
```log
chuck@chucks-MacBook-Pro:~/seaify/ipool/proxy-backend(master|…) % rails s  
=> Booting WEBrick
=> Rails 4.2.0 application starting in development on http://localhost:3000
=> Run `rails server -h` for more startup options
=> Ctrl-C to shutdown server
[2015-05-13 14:38:37] INFO  WEBrick 1.3.1
[2015-05-13 14:38:37] INFO  ruby 2.0.0 (2014-05-08) [universal.x86_64-darwin14]
[2015-05-13 14:38:37] INFO  WEBrick::HTTPServer#start: pid=29245 port=3000

Started GET "/proxys.json?callback=jQuery21307354465932585299_1431499156308&_=1431499156309" for 127.0.0.1 at 2015-05-13 14:39:16 +0800
  ActiveRecord::SchemaMigration Load (0.5ms)  SELECT `schema_migrations`.* FROM `schema_migrations`
Processing by ProxyController#proxys as JSON
  Parameters: {"callback"=>"jQuery21307354465932585299_1431499156308", "_"=>"1431499156309"}
  Proxy Load (0.5ms)  SELECT `proxies`.* FROM `proxies`
Completed 200 OK in 60ms (Views: 14.8ms | ActiveRecord: 20.1ms)
```

## 3. 对curd的超好支持


## 4. 返回数据的多种数据支持，如hello.json轻松返回json, hello.xml轻松返回xml, hello.html返回html页面


## 5. 数据库迁移, 变更管理

## 6. 各种callback

## 7. 良好的社区

## 8. 良好的目录管理，规范清晰，约束多，其实是好事

## 9. 对于model, 支持很好的参数验证

## 10. 对于一个gem包或model等处定义的class等，通过在gemfile中或config.ru中定义后，可以在任意地方直接使用。节省大量nodejs中的require。

## 11. 能很方便的创建prompt, warning, notify, 验证失败的信息

## 惯例配置与restful by windog



- - -


# rails的适用场景？

# rails的缺点？

# rails属于哪种框架？

# rails遇到的挑战？
前后端分离，sinatra, grape, loopback, golang?