

# 04、monitor-监控redis命令

monitor是什么？
有时候我们需要知道客户端对redis服务端做了那些命令操作。
我们可以试用monitor命令来查看。
 
他能清楚的看到客户端在什么时间点执行了那些命令

MONITOR 是一个调试命令，每个命令流回来的redis服务器处理。
它可以帮助理解数据库中正在发生的事情。此命令可用于通过使用CLI通过telnet。
看到所有的请求，由服务器处理为了点时使用Redis作为数据库和分布式缓存系统的一个应用程序错误的能力是非常有用的

 