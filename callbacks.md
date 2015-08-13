## callbacks 做了什么

## define_callbacks, set_callback, run_callback

## include, included, extend
## attr_reader, class_attribute
## define_method

## callback 顺序

```ruby
      def call(arg)
        @before.each { |b| b.call(arg) }
        value = @call.call(arg)
        @after.each { |a| a.call(arg) }
        value
      end
```

## callbacks
```ruby
      def before_perform(*filters, &blk)
        set_callback(:perform, :before, *filters, &blk)
      end
```

## ActiveSupport::Callbacks, 提供
callback，是运行在object的生命周期的某个事件点上的hook代码。
define_callbacks，在object的生命周期中定义事件点，而且这些事件点，并不是必须是update, save这些，可以自己定义，如update_order
```ruby
class Record
	include ActiveSupport::Callbacks
	define_callbacks :update_order
	def update_order
  		run_callbacks :update_order do
  			puts "- update_order"
  		end
  	end
end
```
而对于ActiveModel::Base来说，action, update, save, filter等等这些事件点已经事先定义好，而且该事件的对应函数也写好了

set_callback, 用来安装callback, 第一个参数是event，相当于hook点，第2个参数是hook的类型callback_filter_type，有before, around, after 这3种，而后面可以接block, 也可以加symbol

而run_callbacks，用来在特定的时间，去运行那些安装好的callback，第一个参数是event, 即运行该event上定义的callback, 第二个参数是code block，传递到run_callbacks中，也就是该event实际代码被传递进来了，所以它能调整好callback的运行顺序

## callback sequence order
```ruby
    # Execute before and after filters in a sequence instead of
    # chaining them with nested lambda calls, see:
    # https://github.com/rails/rails/issues/18011
    class CallbackSequence
      def initialize(&call)
        @call = call
        @before = []
        @after = []
      end

      def before(&before)
        @before.unshift(before)
        self
      end

      def after(&after)
        @after.push(after)
        self
      end

      def around(&around)
        CallbackSequence.new do |arg|
          around.call(arg) {
            self.call(arg)
          }
        end
      end

      def call(arg)
        @before.each { |b| b.call(arg) }
        value = @call.call(arg)
        @after.each { |a| a.call(arg) }
        value
      end
    end
```

## function
_insert_callback
define_callbacks
set_callback(最核心)
define_singleton_method

## ActionDispatch
```ruby
  # Provides callbacks to be executed before and after dispatching the request.
```

## callbacks
```ruby
  module Callbacks
    extend  ActiveSupport::Concern
    include ActiveSupport::Callbacks
```
callback的filter

## before_peform
```ruby
      def before_perform(*filters, &blk)
        set_callback(:perform, :before, *filters, &blk)
      end
```

## ClassMethods

## 先理解before, around, after

## 核心
./activesupport/lib/active_support/callbacks.rb

## callback的场景
1. 比如before_action, after_action, before_save, after_save
2. 比如before_validate

1. include ActiveModel::Validations::Callbacks, 就能有before_validate, after_validate这2个callback函数
2. extend ActiveModel::Callbacks, 这个有之后，会有define_model_callbacks, 能够自己定制create, update等的各种before, after, around
    #   define_model_callbacks :create,  only: :after
    #   define_model_callbacks :update,  only: :before
    #   define_model_callbacks :destroy, only: :around
3. activejob中的before_enqueue, around_enqueue, after_queue, before_peform

ClassMethods