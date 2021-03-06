---
title: 码农翻身
categories: Technology
tags:
  - coderising
  - 刘欣
abbrlink: ce95
date: 2021-11-10 21:42:59
---

## 半小时漫画计算机

### 操作系统

多线程抢占式调度。
协程：可自主暂停 <= 运行控制权转移，完全由程序调度和掌控。

CPU => 取指令，译码，执行，写回。

浮点数运算：小数变整数，记录小数位。
`Java: BigDecimal. Python: Decimal. Ruby: BigDecimal`

#### 一切皆文件

```c
// File
int (*open)(...)
void (*close)(...)
int (*read)(...)
int (*write)(...)

// Socket=(IP, Port)
服务器监听 listen
客户端连接 connect
然后读写 read/write
最后关闭 close
```

#### 函数栈

栈帧：栈中的元素。
ESP=Extended Stack Pointer 扩展栈指针寄存器。存放函数栈顶指针
EBP=Extended Base Pointer 扩展基址指针寄存器。存放函数栈底指针

#### C文件编译

![C文件编译](https://cdn.jsdelivr.net/gh/sstian/images/blogimg/C文件编译.jpg)

#### 32位CPU寄存器

![32位CPU寄存器](https://cdn.jsdelivr.net/gh/sstian/images/blogimg/32位CPU寄存器.png)

![32位CPU寄存器-可编程](https://cdn.jsdelivr.net/gh/sstian/images/blogimg/32位CPU寄存器-可编程.png)

![32位CPU寄存器-通用](https://cdn.jsdelivr.net/gh/sstian/images/blogimg/32位CPU寄存器-通用.png)

![32位CPU寄存器-段](https://cdn.jsdelivr.net/gh/sstian/images/blogimg/32位CPU寄存器-段-2.png)

### 数据库

#### 事务

A = Atomicity 原子性：要么不做，要么全做。
C = Consistency 一致性：数据库保持完整性。
I = Isolation 隔离性：多事务并发执行。
D = Durability 持久性：数据的修改是永久的。

排它锁/X锁：Exclusive Lock。用于写数据。
共享锁/S锁：Share Lock。用于读数据。

| 加锁程度          | 隔离级别                  | 更新丢失 | 脏读 | 不可重复读 | 幻读     |
| ----------------- | ------------------------- | -------- | ---- | ---------- | -------- |
| X, 直到事务结束   | 读未提交 Read Uncommitted | Y        |      |            |          |
| X+S: 读完立即释放 | 读已提交 Read Committed   | Y        | Y    |            |          |
| X+S, 直到事务结束 | 可重复读 Repeatable Read  | Y        | Y    | Y - 锁行   |          |
| 串行执行          | 串行化 Serializable       | Y        | Y    | Y          | Y - 锁表 |

#### 日志

初始值：A=200, B=100

| 流程 | 事务T1      | Undo日志文件 |                                        | Redo日志文件 |                                        |
| ---- | ----------- | ------------ | -------------------------------------- | ------------ | -------------------------------------- |
| 1    |             | <T1, 开始>   | 事务开始                               | <T1, 开始>   | 事务开始                               |
| 2    |             | <T1, A, 200> | 记录A的**初始值**                      |              |                                        |
| 3    | A := A - 50 |              |                                        |              |                                        |
| 4    |             |              |                                        | <T1, A, 150> | 记录A的**最新值**                      |
| 5    |             | <T1, B, 100> | 记录B的**初始值**                      |              |                                        |
| 6    | B := B + 50 |              |                                        |              |                                        |
| 7    |             |              |                                        | <T1, B, 150> | 记录B的**最新值**                      |
| 8    |             |              |                                        | <T1, 提交>   | 事务提交，<br />在A, B**写入磁盘之前** |
| 9    | 把A写入磁盘 |              |                                        |              |                                        |
| 10   | 把B写入磁盘 |              |                                        |              |                                        |
| 11   |             | <T1, 提交>   | 事务提交，<br />在A, B**写入磁盘之后** | <T1, 结束>   |                                        |

**恢复策略：**

+ Undo日志文件
  1. 系统重启时，检查Undo日志文件；
  2. 对于事务T1，若没有`<T1, 提交>`记录，表示该事务在执行过程中失败了，则恢复操作；
  3. 利用Undo日志中记录的A, B的初始值。由于幂等性，恢复多少遍结果都一样；
  4. 恢复完，在日志文件中加上`<T1, 回滚>`，表示下次不再恢复与T1相关的。
  
+ Redo日志文件
  1. 在Redo日志文件中查找日志：有`<T1, 提交>`但没有`<T1, 结束>`；
  2. 把A, B的新值写入磁盘；
  3. 最后加上`<T1, 结束>`。

### 编程语言

#### 执行方式

> MSIL=Microsoft Intermediate Language 微软中间语言

| 执行方式             | 编程语言                                      | 特点               |
| -------------------- | --------------------------------------------- | ------------------ |
| 1. 编译执行          | Ada, C, C++, Rust, Go                         | 执行快；难以跨平台 |
| 2. 字节码：编译+解释 | Java                                          |                    |
|                      | Clojure, Scala, Jython, JRuby, Groovy, Kotlin | 编译成Java字节码   |
|                      | Ruby, PHP, Perl, Python                       | 创建自己的字节码   |
|                      | C#, VB.NET                                    | MSIL               |

#### 面向对象圣经

又名“神不在的星期天”

| 序列 | 内容                         | 说明                                                         | 相关人物                                               |
| ---- | ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| 1    | 编译器                       |                                                              | John Warner Backus 计算机科学先驱之一、图灵奖获得者    |
| 2    | 函数、数据结构               | 程序=算法+数据结构                                           | Dennis Ritchie C语言之父、UNIX创造者之一               |
| 3    | Object                       | `typedef struct Stack {...} Stack;`                          | Alan Kay 面向对象编程思想的创始人之一                  |
| 4    | Class                        | 重复的方法代码公共化，隐藏this/self。`Stack* stack = new Stack();` |                                                        |
| 5    | 继承、多态、设计模式 => 复用 | 优先使用组合而不是继承，面向接口编程而不是面向实现编程       | Erich Gamma ... 《设计模式：可复用面向对象软件的基础》 |
| 6    | 抽象                         | 系统需求抽象成高层的概念，在概念层次进行编程                 |                                                        |
| 7    | 休息……                       |                                                              |                                                        |

#### 通用特性

冯·诺依曼计算机

| 通用特性                     | 说明                                                       | 范例                                           |
| ---------------------------- | ---------------------------------------------------------- | ---------------------------------------------- |
| 面向对象                     | 封装、继承、多态，Prototype / Mixin / Traits / Duck Typing |                                                |
| 函数式编程                   | 高阶函数、闭包、惰性求值、递归、不可变状态、无副作用……     | JVM: Lisp, Clojure                             |
| 元编程                       |                                                            | Java动态代理CgLib，Lisp宏                      |
| 并发模型                     | 同步与互斥、锁、死锁、软件事务内存……                       | Java线程，Python协程，Go routine, Erlang Actor |
| 虚拟机和垃圾回收             |                                                            | JVM垃圾回收                                    |
| 静态类型、动态类型、类型推导 | 静态类型：编译期确定。动态类型：运行期确定                 |                                                |
| 抽象语法树                   | AST=Abstract Syntax Trees                                  |                                                |
| 指针                         |                                                            | C语言的灵魂                                    |
| 其它                         | 错误处理（异常）、泛型、同步异步、序列化                   |                                                |

## 码农翻身