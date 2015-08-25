## 在ActionView中，helper被广泛使用，如current_user, login_path, date helpers, 讲解helper的机制
大致分为三类，第一类helper, 定义在app/helpers目录下，
第二类，系统内置，如DateHelper
第三类，config/route.rb中的helper
## helper初始化
```ruby
    # Returns a module with all the helpers defined for the engine.
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

## helpers path

## 默认helper只能在view中使用，而不能在controller中使用？

## 在controller里，定义helper_method, 能使得controller里的代码，能在view中使用

## helper.rb里，能够访问到session, request这种信息吗？

## 为什么view里能访问到controller?
```ruby
    def initialize(context = nil, assigns = {}, controller = nil, formats = nil) #:nodoc:
      @_config = ActiveSupport::InheritableOptions.new

      if context.is_a?(ActionView::Renderer)
        @view_renderer = context
      else
        lookup_context = context.is_a?(ActionView::LookupContext) ?
          context : ActionView::LookupContext.new(context)
        lookup_context.formats  = formats if formats
        lookup_context.prefixes = controller._prefixes if controller
        @view_renderer = ActionView::Renderer.new(lookup_context)
      end

      assign(assigns)
      assign_controller(controller)
      _prepare_context
    end
```
初始化ActionView::Base时，有将controller传递进来。

ActionView的初始化
```ruby
 def view_context_class
        @view_context_class ||= begin
          supports_path = supports_path?
          routes  = respond_to?(:_routes)  && _routes
          helpers = respond_to?(:_helpers) && _helpers

          Class.new(ActionView::Base) do
            if routes
              include routes.url_helpers(supports_path)
              include routes.mounted_helpers
            end

            if helpers
              include helpers
            end
          end
        end
      end
```

## view_render 用来render view，而view_assigns则用来传递controller中的实例变量

## view_context_class，用来获取view_context的class名，而这个class则必须提供new(lookup_context, assigns, controller)这个函数

## 传递实例变量
```ruby
    def view_assigns
      protected_vars = _protected_ivars
      variables      = instance_variables

      variables.reject! { |s| protected_vars.include? s }
      variables.each_with_object({}) { |name, hash|
        hash[name.slice(1, name.length)] = instance_variable_get(name)
      }
    end
```

## 在view中，使helper，实质上在将这些参数，重新传递到controller中，也就是view中，时能访问到controller的，比如rspec中，set_login_user

```ruby
    meths.each do |meth|
          _helpers.class_eval <<-ruby_eval, __FILE__, __LINE__ + 1
            def #{meth}(*args, &blk)                               # def current_user(*args, &blk)
              controller.send(%(#{meth}), *args, &blk)             #   controller.send(:current_user, *args, &blk)
            end                                                    # end
          ruby_eval
        end
```

## constantize

## 默认情况下，每个controller，会自动去加载它对应的helper, 通过从controller里提取出名字

## 也就是每个controller自带了一些_helpers, 和_helper_methods

## ActionView::Helpers，自带了一批helper

## ActionView::Helpers::ControllerHelper，将headers, session, cookie等直接delegate了controller，所以可以直接使用

## 第三方gem的helper如何扩展，比如spree定义好了，关键的几个helper，需要在app中使用,
```ruby
module Spree
  module CurrentUserHelpers
    def self.included(receiver)
      receiver.send :helper_method, :spree_current_user
    end

    def spree_current_user
      current_user
    end
  end

  module AuthenticationHelpers
    def self.included(receiver)
      receiver.send :helper_method, :spree_login_path
      receiver.send :helper_method, :spree_signup_path
      receiver.send :helper_method, :spree_logout_path
    end

    def spree_login_path
      main_app.new_user_session_path
    end

    def spree_signup_path
      main_app.new_user_registration_path
    end

    def spree_logout_path
      main_app.destroy_user_session_path
    end
  end
end

ApplicationController.send :include, Spree::AuthenticationHelpers
ApplicationController.send :include, Spree::CurrentUserHelpers

Spree::Api::BaseController.send :include, Spree::CurrentUserHelpers
```
