# 说明
码字好累，还是图直观， libpq是pg的通讯库，负责和客户端、standby通讯，通过封装socket实现，相对独立，现将其主要定义和函数总结，以备忘
![](https://github.com/shines001/blog/blob/master/postgres/img/libpq.png)
    
     
# 发送缓冲 相关说明
PqSendBuffer   8192长度的 char*, 在通过MemoryContext申请  
PqSendStart    int   下一次发送的起始点，可以理解为 刷数据的指针  
PqSendPointer    int   下一次向缓冲写数据的起始点，可以理解为写数据指针  
上次调用现在PqSendPointer位置写入数据，在触发刷数据的条件下执行flush，从PqSendStart调用send()发送数据  
满足条件： PqSendStart  <= PqSendPointer  
![](https://github.com/shines001/blog/blob/master/postgres/img/PqSendBuffer.jpg)
    
     
# 接收缓冲 相关说明
PqRecvBuffer     8192固定长度  char*, 通过MemeoryContext申请  
PqRecvPointer    int 上层应用读取的起始点  
PqRecvLength     int  接收数据的长度，可以理解为socket下一次把接收数据存放的起点  
先接收，后读取， 满足条件： PqRecvPointer <= PqRecvLength  
![](https://github.com/shines001/blog/blob/master/postgres/img/PqRecvBuffer.jpg)
