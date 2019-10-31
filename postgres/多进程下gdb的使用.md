
多进程下gdb的使用
===========================
由于鄙人工作需深入postgresql代码，故学习了如果调试postgresql各个子进程；

****
	
|Author|shines001|
|---|---
|E-mail|liangxiangyu@hthorizon.com



# follow-fork-mode设置
* 命令说明  : set follow-fork-mode   [parent|child] 
* parent选项:  gdb调试父进程  
* child 选项:  gdb调试子进程  

# detach-on-fork设置
* 命令说明 ：  set  detach-on-fork  [on|off]
* on :   断开调试follow-fork-mode指定的进程
* off :  可以同时调试父子进程，另外一个进程会被阻塞在fork处

# follow-fork-mode和detach-on-fork组对效果
|follow-fork-mode|detach-on-fork|说明|
|---|---|---
|parent|on|只调试主进程(gdb默认)|
|child|on|只调试子进程|
|parent|off|同时调试两个进程,启动时gdb跟主进程,子进程在fork处阻塞,可通过inferior num来切换调试主或子进程|
|child|off|同时调试两个进程,启动时gdb跟子进程,主进程在fork处阻塞,可通过inferior num来切换调试主或子进程|


# inferior说明
* info inferior ： 查询正在调试的进程，每个进程会有一个编号num，被调试前面会有 *标志
* inferior  num ： 切换调试的进程， num为通过info inferior获取的编号
      通过以上可以得出一个结论，实际上 follow-fork-mode可以随意设置，如果只有一个子进程，可以采用child模式，如果多个子进程可以采用parent模式
   这样可以减少 inferior切换的次数；

# postgres调试backend
    因为  set  detach-on-fork会导致postgres父进程阻塞，所以无法通过此工具正常调试，需要使用 attach
    1.  postgresql.conf添加, pre_auth_delay = 60   #启用延迟认证，给attach留时间窗口
    2.  gdb  attach  pid    #pid为处理客户端访问backend进程号
    3.  set   breakpoint    #设置断点
    4.  continue            #继续运行，会等待 pre_auth_delay的到期
    
 
    
