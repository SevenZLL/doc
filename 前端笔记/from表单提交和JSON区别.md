# from表单提交和JSON区别

```
form表单是“ ”的数据格式，例如：
a=1&b=2&c=3
而json格式则与之不同，如下：
{"a":1,"b":2,"c":3}
传递的数据比较简单，那么两种方式基本上没什么大区别（当然后台接收数据的方式肯定是不同的），但是如果数据比较复杂，比如包含数组、对象等时，就只有json才能胜任了。此外，如果数据是通过表单提交的，那么当然使用默认的form即可，因为它会自动构建数据结构，无需手工拼接；而如果是通过ajax发送数据，则最好使用json，因为可以通过简单的方法把对象转换为json字串。
```

