## 推荐一些工作中用到过的好用的gem

### [hirb](https://github.com/cldwalker/hirb)
将rails console下model的输出变得更标准好看, 类似于mysql，看下图感受下带来的变化
![](https://ruby-china-files.b0.upaiyun.com/photo/2015/6fbee69f45ad2d17672e6847018eae35.png)
当然，不想每次进rails console, 都敲遍Hirb.enable，那在~/.irbrc中加入
```ruby
require 'rubygems'
require 'hirb'
require 'active_record'
Hirb.enable
```

### [rails_panel](https://github.com/dejan/rails_panel)
chrome下的插件，能方便的看status，controller#action, params, log, activerecord查询语句，不用去看终端日志，因为日志一股脑的全输出，很难看清晰的。 react在chrome下也有类似的插件。
![](https://ruby-china-files.b0.upaiyun.com/photo/2015/8a530c20b2f42b530f0698a18f412a9c.png)

### [i18n-js](https://github.com/fnando/i18n-js)
应用场景，前端需要对表单进行验证，验证失败后的信息，是要使用i18n的，但其在rails里。在没使用i18n-js前，传递值是这样的，controller里将需要传递的i18n值，传到页面，写入到某个节点的属性值里，然后在js里读取该属性值。想想都知道，这TM多蛋疼。
i18n-js配置正确后，是这样的，直接可以在js里，使用I18n.t，和rails的使用完全一致。
```ruby
$('#form-submit-tips').text(I18n.t("validate.error.real_name")).removeClass('bg-success').addClass('bg-warning')

```
它的原理，实质上，是读取了config/locales/*.yml，生成了一个translation.js，也就是translation.js是rails i18n的一份js版，然后再暴露接口I18n.t去读取translation.js中的数据

### [gon](https://github.com/gazay/gon)
更方便的将rails的变量传递到js里，和i18n-js的效果有些类似。
应用创建，页面需要前端验证，验证的规则，需要从后台传递过来
配置正确后，在rails controller,
```ruby
gon.mobile_regex_format = Setting.mobile_regex_format
```
在js里
```js
real_name_regex = new RegExp(gon.real_name_regex_format)
```

### guard系列
1. [guard-bundler](https://github.com/guard/guard-bundler), gemfile变更后，自动执行bundle
2. [guard-migrate](https://github.com/glanotte/guard-migrate), 新产生migration文件，变更后，自动执行rake db:migrate
3. [guard-annotate](https://github.com/cpjolicoeur/guard-annotate), db schema文件变更后，自动annotate, 与guard-migrate搭配非常好
4. [guard-shell](https://github.com/guard/guard-shell), 文件变更后，执行相应shell命令，场景： 使用i18n-js时，config/locale/*.yml变更了的话，需要更新translation.js文件，要执行相应的rake i18n:js:export


### 参考链接
[Rails Console  irb 裡的方便工具-Hirb](http://blog.eddie.com.tw/2010/04/09/hirb-the-convenient-gem-for-console-and-irb/)

ps: 求助啊。链接在h3里为啥显示有问题啊，在github里是正常的。