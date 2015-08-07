# 常见错误

## 1. 函数最后以puts结尾
下面这段话，本意是看user的内容，但因为最后一句是puts，所以并没有返回user, 导致认证失败
```ruby
    def self.find_first_by_auth_conditions(warden_conditions)
        conditions = warden_conditions.dup
        puts conditions[:signin]
        user = where(["lower(username) = :value OR lower(email)
   = :value", { :value => conditions[:signin].downcase }]).first
        puts user
    end
```