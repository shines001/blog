#码字好累，还是图直观， libpq是pg的通讯库，负责和客户端、standby通讯，通过封装socket实现，相对独立，现将其主要定义和函数总结，以备忘

![](https://github.com/shines001/blog/blob/master/postgres/img/libpq.png)


