## 本篇介绍rspec的使用
在未接触rspec之前，大量使用过c, python, nodejs, php，但测试方面基本都是空白。现在想来，很多时候不是开发者不想写测试，而是并没有好的测试工具，不够灵活。写测试的难度很多时候都快等于开发了，甚至高过开发。

## tdd好处
先写出完备的测试，再去填坑，将失败的测试逐个通过，开发的过程变成了填坑通关的过程。
1. 开发前，逼迫你全面的思考，思考将整个任务进行细分，细分到方便测试的程度, 解耦成不同组件
2. 更加心安, 有安全感(不知道你们是否有这种感受)，方便功能调整，改变某部分功能时，对其它模块的影响都能迅速反应出来
3. 方便新人了解系统

## rspec篇

### rspec时间相关测试篇
不少情况下，代码的运行逻辑和时间相关，如到了提前设定的时间范围内，执行xxx函数. 这种情况下的测试，一般会将时间判断，单独抽离出来成为一个函数，对它进行测试，这时候，我们就需要faker时间了, 不可能真等到设定的时间范围内。
使用[timecop](https://github.com/travisjeffery/timecop)来faker时间, 如下面例子，将当前的时间Tim.now, 设定到@end_time前5s，以保证在时间范围内。
```ruby
    it "pass test now_is_in_order_flow_on_peroid?" do
      Timecop.freeze(@end_time - 5) do
        expect(Spree::Order.now_is_in_order_flow_on_peroid?).to eq true
      end
    end
```

### rspec 定时任务测试篇，如whenever
代码中很可能使用whenever这样的gem, 执行些定时任务，怎么测试呢？
whenever类似的gem, 达到的效果是帮你生成相应的crontab任务，它做的事情其实是和我们的代码逻辑无关的。
我的理解是，如果是定时每隔5分钟，要执行下model.xxx函数，那实际上我们直接在rspec中调用model.xxx函数对其进行测试即可。
而如果要测试whenever的crontab语句生成是否正确，请参考https://github.com/javan/whenever/blob/master/test/unit/cron_test.rb , 大部分情况下，是没有必要对其进行测试的，如果是简单的每隔xx分钟/小时。

### rspec helper篇
1. 在web的测试中，基本都会去检验是否登录，以及部分代码是登录后才能去执行，那在model, controller中，会有大量的测试，需要进行提前登录，这时候就需要使用helper，在support中建立login_helpers.rb，加入login_user, login_admin, login_staff这些函数，在需要做登录的测试中，引入login_user即可。
```ruby
  def login_user
    before(:each) do
      user = FactoryGirl.create(:user)
      controller.send(:login_as, user)
    end
  end
```

2. 因rspec测试并不会去读取db/seeds中的数据，但model，controller中的许多逻辑测试，可能依赖于某部分数据的提前建立，这时候可以在support中建立data_helpers.rb, 加入set_init_data类似的函数，供所有测试去公用这段初始化数据的代码

### rspec 数据伪造篇
许多情况下，需要伪造email, name这些类似的信息，而且要求是uniq, 这时候可以方便的使用faker, 以及sequence
```ruby
FactoryGirl.define do
  sequence(:random_string)      { Faker::Lorem.sentence }
  sequence(:random_name)      { Faker::Name.name }
end
```