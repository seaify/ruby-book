## helper部分使用场景
1. 在页面中，需要有一些逻辑判断，如当前用户是否已经登录。
2. 对数据的处理，如时间和钱的格式化显示

## helper来源
1. ActionView内置的helper, 如ActionView::Helper::DateHelper,  ActionView::Helper::UrlHelper, 集中于actionview/lib/action_view/helpers中
2. config/route.rb中定义的route, 都会有其相应的xx_path
3. app/helpers/xx_helper.rb
4. controller中定义的函数，通过helper_method

## controller中的helpers
每个controller都有_helpers(helper method集合的module)，_helper_methods, helper method的数组
```ruby
    included do
      class_attribute :_helpers
      self._helpers = Module.new

      class_attribute :_helper_methods
      self._helper_methods = Array.new
    end
```
如
```ruby
2.1.5 :039 > TopicsController._helpers
#<Module:0x007fb6b205fdc8>
2.1.5 :040 > TopicsController._helper_methods
[
    [ 0] :view_cache_dependencies,
    [ 1] :cookies,
    [ 2] :alert,
    [ 3] :notice,
    [ 4] :form_authenticity_token,
    [ 5] :protect_against_forgery?,
    [ 6] :current_visit,
    [ 7] :ahoy,
    [ 8] :wice_grid_custom_filter_params,
    [ 9] :can?,
    [10] :cannot?,
    [11] :current_ability,
    [12] :browser,
    [13] :warden,
    [14] :signed_in?,
    [15] :devise_controller?,
    [16] :spree_login_path,
    [17] :spree_signup_path,
    [18] :spree_logout_path,
    [19] :spree_current_user
]
```
如果添加helper_method :current_user, :logged_in?这行代码，则:current_user, :logged_in?这两个method, 都会被追加到这个controller的_helper_methods, 并且在_helpers这个module里，会新生成同样的两个函数，而这两个函数实质上，就是一层代理，当view中include这个_helpers后，访问:current_user，实际上最终是调用了controller的:current_user.
```ruby
      def helper_method(*meths)
        meths.flatten!
        self._helper_methods += meths

        meths.each do |meth|
          _helpers.class_eval <<-ruby_eval, __FILE__, __LINE__ + 1
            def #{meth}(*args, &blk)                               # def current_user(*args, &blk)
              controller.send(%(#{meth}), *args, &blk)             #   controller.send(:current_user, *args, &blk)
            end                                                    # end
          ruby_eval
        end
      end
```

## engine的helpers
第三方的gem，如果作为一个engine，其可以选择性的暴露出其helpers, 供app来使用
```ruby
    def helpers
      @helpers ||= begin
        helpers = Module.new
        all = ActionController::Base.all_helpers_from_path(helpers_paths)
        ActionController::Base.modules_for_helpers(all).each do |mod|
          helpers.include(mod)
        end
        helpers
      end
    end
```
