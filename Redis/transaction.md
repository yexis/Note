redis事务：

指令： multi	exec	discard

等于：begin	commit	rollback



- redis事务根本不具备原子性，而仅仅是为了事务的“隔离性”中的串行化--当前执行的事务不会被其他事务打断的权利。
- redis中的discard指令，用于丢弃事务缓存队列中的所有指令，在exec之前执行。



#### 并发：Watch

**watch指令**：适用于并发操作

watch是乐观锁，分布式锁是悲观锁

watch会在事务开始之前盯住一个或多个变量，当事务执行时，也就是服务器收到了exec指令要顺序执行缓存的事务队列，Redis会检查变量自watch后是否被修改了（包括当前客户端所做的修改）

如果变量被修改了，exec指令就会返回NULL回复告知客户端事务执行失败，此时客户端一般会重试。

如指令

```go
//关注变量books
> watch books 
OK

//增加
> incr books	//此时books被修改了 
(integer) 1

//开启事务
multi 
OK
//增加
incr books
QUEUED
//执行指令
exec
nil	//返回nil，说明事务执行失败
```



> 注意：Redis禁止在multi和exec之间执行watch命令，而必须在multi之前盯住变量，否则会报错。



