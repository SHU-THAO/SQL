# 数据库逻辑框架

​	与其他数据库相比，MySql 数据库有自己独特的地方，它的架构可以在不同的场景中应用并发挥良好的作用。主要体现在存储引擎的架构上，插件式的存储引擎架构将查询处理和其他的系统任务以及数据的存储提取相分离。这种架构的最大优点是可以根据业务和实际需求选择适当的存储引擎。下面是MySql 的逻辑架构图： 

![数据库逻辑框架](C:\Users\12084\Desktop\SQL语言与相关概念\数据库逻辑框架.png)

一：连接层： 
    最上层是客户端和连接服务，包括本地sock 通信和大多数基于客户端/服务端工具实现的类似于tcp/ip 的通信。主要完成一些类似于链接处理、授权认证、以及相关的安全方案。在该层上引入了连接池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL 的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

二：服务层： 
    第二层架构主要完成大多数的核心服务功能，如SQL 接口，并完成缓存的查询，SQL 的分析和优化及部分内置函数的执行。所有跨存储引擎的功能也在这一层上实现，如过程，函数等。在该层，服务器会解析并创建相应的内部解析树，并完成相应的优化如确定查询表的顺序，是否利用索引等，最后生成对应的执行操作。如果是select 语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。

三：引擎层： 
    在存储引擎层，存储引擎真正的负责了MySql 中数据的存储和提取，服务器通过API 与存储引擎进行通信。不同的存储引擎具有不同的功能，这样使得我们可以根据自己的实际需求选取合适的存储引擎。

四：存储层： 
    数据存储层，主要将数据存储在运行于裸设备上的文件系统上，并完成与存储引擎的交互。

# 主流的两种引擎比较 MyISAM VS InnoDB

## 1. Innodb引擎

Innodb引擎提供了对数据库ACID事务的支持，并且实现了SQL标准的四种隔离级别。该引擎还提供了行级锁和外键约束，它的设计目标是处理大容量数据库系统，它本身其实就是基于MySQL后台的完整数据库系统，MySQL运行时Innodb会在内存中建立缓冲池，用于缓冲数据和索引。但是该引擎不支持FULLTEXT类型的索引，而且它没有保存表的行数，当SELECT COUNT(*) FROM TABLE时需要扫描全表。当需要使用数据库事务时，该引擎当然是首选。由于锁的粒度更小，写操作不会锁定全表，所以在并发较高时，使用Innodb引擎会提升效率。但是使用行级锁也不是绝对的，如果在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会锁全表。



## 2. MyISAM引擎

MyISAM是MySQL默认的引擎，但是它没有提供对数据库事务的支持，也不支持行级锁和外键，因此当INSERT(插入)或UPDATE(更新)数据时即写操作需要锁定整个表，效率便会低一些。不过和Innodb不同，MyISAM中存储了表的行数，于是SELECT COUNT(*) FROM TABLE时只需要直接读取已经保存好的值而不需要进行全表扫描。如果表的读操作远远多于写操作且不需要数据库事务的支持，那么MyISAM也是很好的选择。



## 3. 主要区别：

- MyISAM是非事务安全的，而InnoDB是事务安全的

- MyISAM锁的粒度是表级的，而InnoDB支持行级锁

- MyISAM支持全文类型索引，而InnoDB不支持全文索引

- MyISAM相对简单，效率上要优于InnoDB，小型应用可以考虑使用MyISAM

- MyISAM表保存成文件形式，跨平台使用更加方便

应用场景：
1、MyISAM管理非事务表，提供高速存储和检索以及全文搜索能力，如果再应用中执行大量select操作，应该选择MyIASM

2、InnoDB用于事务处理，具有ACID事务支持等特性，如果在应用中执行大量insert和update操作，应该选择InnoDB

