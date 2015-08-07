# autoload 10问?
# autoload

如果autoload，并没有定义在eager_autoload下，那实际上，并没有去写，也就是当@_eager_autoload是false的时候，去执行autoload没有意义啊，@_autoloads这个hash，如果没有执行eager_load!, 那实际上也并未去require文件

# action_controller.rb也override了eager_load!，为何要为cache手动eager_loading.
为何要手动去执行require呢。既然有autoload

# action_view.rb中，定义了不少autoloaded_at, 为何要去指定路径呢，不指定会为找不到？

# 也就是实际上，rails的各种路径关系，以及gemfile中定义好的类，之所以能访问，都是因为rails帮我们处理好了

# 也就是实际上，对于一个gem，因为这个gem会被加载，其lib/xxx.rb，会处理好自己的依赖加载, 因activemodel, activeview, 等都是一个个单独的gem, 但是rails作为一个整体的gem，包含了它们。

# rails的命名空间，来自railties

# eager_autoload

# rails convention

# LOADED_FEATURES

# lazy load

# reload

# unload

# guard, restart program, file change, what happened

# when file is not loaded, how did it find the file which need to require

# global variable, CONSTANT VARIABLE, how did you find that?


http://bundler.io/bundler_setup.html