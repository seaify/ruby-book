# 简介
见到ruby代码中的3.times, 2.years.ago，觉得很神奇，又真的很方便

## 2.years.ago
直接用ruby执行，会出错， 而rails console中能正常运行，说明这个有趣的功能是内置在rails里的
```ruby
puts 2.years.ago
```
```ruby
chuck@chucks-MacBook-Pro:~/seaify/ipool/proxy-backend(master|✚2…) % rails  console
Loading development environment (Rails 4.2.0)
irb(main):001:0> 3.years.ago
=> Tue, 15 May 2012 09:12:23 UTC +00:00
```

在Agile Web Development with Rails 4 这本书第406页中介绍到了一些rails中内置的库，存放在activesupport/lib/active_support/core_ext这个目录，这些库也能被非rails程序使用。

在[integer](https://github.com/rails/rails/blob/master/activesupport/lib/active_support/core_ext/integer/time.rb)中: 

```ruby
class Integer
  # Enables the use of time calculations and declarations, like <tt>45.minutes +
  # 2.hours + 4.years</tt>.
  #
  # These methods use Time#advance for precise date calculations when using
  # <tt>from_now</tt>, +ago+, etc. as well as adding or subtracting their
  # results from a Time object.
  #
  #   # equivalent to Time.now.advance(months: 1)
  #   1.month.from_now
  #
  #   # equivalent to Time.now.advance(years: 2)
  #   2.years.from_now
  #
  #   # equivalent to Time.now.advance(months: 4, years: 5)
  #   (4.months + 5.years).from_now
  def months
    ActiveSupport::Duration.new(self * 30.days, [[:months, self]])
  end
  alias :month :months

  def years
    ActiveSupport::Duration.new(self * 365.25.days, [[:years, self]])
  end
  alias :year :years
end
```


所以2.years实际返回了一个ActiveSupport::Duration对象，而在[duration](https://github.com/rails/rails/blob/master/activesupport/lib/active_support/duration.rb)中: 

```ruby
    def ago(time = ::Time.current)#初始值默认是系统当前时间
	  #-1, 相当于是确定方向，是加，还是减
      sum(-1, time)
    end
	
	def sum(sign, time = ::Time.current) #:nodoc:
		#提取出type，number，sign数据，最终进行真实的计算
        parts.inject(time) do |t,(type,number)|
          if t.acts_like?(:time) || t.acts_like?(:date)
            if type == :seconds
              t.since(sign * number)
            else
              t.advance(type => sign * number)
            end
          else
            raise ::ArgumentError, "expected a time or date, got #{time.inspect}"
          end
        end
      end
```

## 3.times
n.times 这个功能ruby就能正常运行
```ruby
3.times do
	puts "hello"
end
```

