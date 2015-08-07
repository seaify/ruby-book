# 书写代码中需要注意到的
1. 每行代码不需要以;结尾
```ruby
puts hello
```
2. 并不需要遵循python的4个空格缩进，而是使用end来标识一个代码段落的结束，如def, each后面都需要end
```ruby
def say_goodnight(name)
	result = 'Good night, ' + name return result
end
```
```ruby
items.each do |item|
	puts item
end
```
3. 注释代码，是以#开头
```ruby
#I am a comment
```
4. 函数输出
并不需要像python或c等语言里一样，需要调用return，在ruby里是会返回最后一个表达式的值

5. 字符串中的单引号和双引号区别