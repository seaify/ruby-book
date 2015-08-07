## rails engine的使用
1. gemspec文件，如spree.gemspec, 其作用
>```ruby
> s.add_dependency 'spree_core', version
  s.add_dependency 'spree_api', version
  s.add_dependency 'spree_backend', version
  s.add_dependency 'spree_frontend', version
  s.add_dependency 'spree_sample', version
  s.add_dependency 'spree_cmd', version
```
2. 如何编写一个gem

3. spree依赖spree_sample这个gem，而我们需要修改spree_sample这个gem，则将spree_sample的代码拷贝下来，并且在gemfile中，使用gem "spree_sample", :path => "sample/"

## 参考