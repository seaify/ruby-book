# autoload 10问?
# autoload

如果autoload，并没有定义在eager_autoload下，那实际上，并没有去写，也就是当@_eager_autoload是false的时候，去执行autoload没有意义啊，@_autoloads这个hash，如果没有执行eager_load!, 那实际上也并未去require文件

# action_controller.rb也override了eager_load!，为何要为cache手动eager_loading.
为何要手动去执行require呢。既然有autoload

# action_view.rb中，定义了不少autoloaded_at, 为何要去指定路径呢，不指定会为找不到？

# 也就是实际上，rails的各种路径关系，以及gemfile中定义好的类，之所以能访问，都是因为rails帮我们处理好了

# 也就是实际上，对于一个gem，因为这个gem会被加载，其lib/xxx.rb，会处理好自己的依赖加载, 因activemodel, activeview, 等都是一个个单独的gem, 但是rails作为一个整体的gem，包含了它们。

# rails的命名空间，来自railties

# 路径
1. eager_load_paths
2. autoload_paths
3. autoload_once_paths
4. $LOAD_PATH

# rails启动
1. Rails::Application::Bootstrap
2. Rails::Application::Finisher

# rails 很核心的是initializer

# 一个rails app有routes, config, reloaders

updater = ActiveSupport::FileUpdateChecker.new(paths) { reload! }

attr_accessor的使用

# rails本身就是一个engine

# rails 本身有定义了hash, 去load app/controller, app/models这些路径下的文件，但你可以自己去定制路径

所有path下的目录，都会被自动加载进来, 如app/services

当i18n的文件内容改动时，网页上会自动加载，也就说明新的yml文件被load进去了

engine里创建的app, controllers，实际表现上来看，就像是在application里，所以application的helper，engine里的代码都能去使用

# 对于rails来说，它是Railtie，而代码段里的config，实际就是它自己的实例

# 每个engine，定义了自己的rake任务

# rails的版本问题, 怎么方便的使用多种版本

# 实际上，有一个module， 将所有的helpers都打包整理了

# rails启动的时候，会根据环境变量，去读取新的信息， 
```ruby
 initializer :load_environment_config, before: :load_environment_hook, group: :all do
      paths["config/environments"].existent.each do |environment|
        require environment
      end
    end
```

rails的默认初始化文件，加载是通过initializer来加载的, load_config_initializer
rails的环境变量加载，是通过initializer来加载的，load_environment_config
rails的路由信息, helper信息，viewer路径信息
设置加载路径


railties/lib/rails/application/finisher.rb里，有对各个namespace下，去调用其eager_load!, 
```ruby
2.1.5 :026 > Homepage::Application.config.eager_load_namespaces
 => [ActiveSupport, ActionDispatch, ActiveModel, ActionView, ActionController, ActiveRecord, ActionMailer, Coffee::Rails::Engine, Jquery::Rails::Engine, Turbolinks::Engine, Haml::Rails::Engine, Bootstrap::Rails::Engine, RailsSettingsUi::Engine, SimpleForm, MdEmoji::Engine, Devise::Engine, SocialShareButton::Rails::Engine, Owlcarousel::Rails::Engine, Sidekiq::Rails, DisqusRails::Rails::Engine, Ahoy::Engine, Kaminari::Engine, Wice::WiceGridEngine, BootstrapDatepickerRails::Rails::Engine, Jquery::Ui::Rails::Engine, Highcharts::Rails::Engine, LazyHighCharts::Rails::Engine, FontAwesome::Rails::Engine, Spree::Core::Engine, Spree::Api::Engine, Select2::Rails::Engine, Spree::Backend::Engine, CanonicalRails::Engine, Spree::Frontend::Engine, SpreeSample::Engine, SpreeGateway::Engine, KaminariI18n::Engine, SpreeI18n::Engine, QuietAssets::Engine, Homepage::Application, ExceptionNotification::Engine] 
```
这也解释了各个gem的文件是怎么被加载的

```ruby
      def inherited(base)
        unless base.abstract_railtie?
          Rails::Railtie::Configuration.eager_load_namespaces << base

          base.called_from = begin
            call_stack = if Kernel.respond_to?(:caller_locations)
              caller_locations.map { |l| l.absolute_path || l.path }
            else
              # Remove the line number from backtraces making sure we don't leave anything behind
              caller.map { |p| p.sub(/:\d+.*/, '') }
            end

            File.dirname(call_stack.detect { |p| p !~ %r[railties[\w.-]*/lib/rails|rack[\w.-]*/lib/rack] })
          end
        end

        super
      end
```
为什么app/assets/也会被加入到eager_load_path中，它下面又没有rb程序
```ruby
    # Eager load the application by loading all ruby
    # files inside eager_load paths.
    def eager_load!
      config.eager_load_paths.each do |load_path|
        matcher = /\A#{Regexp.escape(load_path.to_s)}\/(.*)\.rb\Z/
        Dir.glob("#{load_path}/**/*.rb").sort.each do |file|
          require_dependency file.sub(matcher, '\1')
        end
      end
    end
```

add_to_config, 这个函数，能方便的往config/application.rb，增加代码？
app_file是做什么的？

module和engine之间的区别，module是没有实体的，它的eager_load!是做什么

engine和app之间的交互，如修改app的assets.paths
```ruby
      initializer "spree.assets.precompile", :group => :all do |app|
        app.config.assets.paths << "#{Rails.root}/app/assets/fonts"
        app.config.assets.precompile << /\.(?:svg|eot|woff|ttf)$/
```

```ruby
2.1.5 :028 > Homepage::Application.config.eager_load_namespaces[0].eager_load!
 => {:NumberConverter=>"active_support/number_helper/number_converter", :NumberToRoundedConverter=>"active_support/number_helper/number_to_rounded_converter", :NumberToDelimitedConverter=>"active_support/number_helper/number_to_delimited_converter", :NumberToHumanConverter=>"active_support/number_helper/number_to_human_converter", :NumberToHumanSizeConverter=>"active_support/number_helper/number_to_human_size_converter", :NumberToPhoneConverter=>"active_support/number_helper/number_to_phone_converter", :NumberToCurrencyConverter=>"active_support/number_helper/number_to_currency_converter", :NumberToPercentageConverter=>"active_support/number_helper/number_to_percentage_converter"} 
2.1.5 :029 > 

```

```ruby
      initializer :eager_load! do
        if config.eager_load
          ActiveSupport.run_load_hooks(:before_eager_load, self)
          config.eager_load_namespaces.each(&:eager_load!)
        end
      end
```

而load_tasks, load_runner, load_generator相当于提供了内部函数给命令rails console, rails runner

# app.routes

# ruby很好的一点是，将代码块作为参数，传递进去

# Rails::Application::Configuration 是配置相关的，
# Routes, 当文件改动后，重新加载route

```ruby
if groups.empty?
        # Load all groups, but only once
        @setup = load.setup
      else
        load.setup(*groups)
      end
```
load, 实际上是一个函数，ruby是怎么去查证一个命名，在哪儿的，

所有的autoload，都是在把需要include的文件载入到load_path, 并且没有真实require，寻找时，才会沿着load_path进行寻找

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