# MySQL锁

事务的隔离级别（读未提交，读已提交，可重复读，串行化）就是通过锁机制来实现的

按粒度来分：全局锁，表级锁（InnoDB），行级锁

----

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744250.png" alt="image-20250416083138275" style="zoom:67%;" />	

## 一.全局锁

![image-20250416083803854](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744180.png)	

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744201.png" alt="image-20250416083616442" style="zoom: 50%;" />	

释放锁后，阻塞队列中的DDL,DML语句会先后自动执行

​	![image-20250416084005651](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744199.png)	

![image-20250416084053052](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744013.png)	

------

## 二.表级锁

![image-20250416084244707](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744661.png)	

表级锁又分为，**表锁，元数据锁（meta data lock），意向锁**

### 1.表锁

表共享**读锁**（read lock），表独占**写锁**(write lock)

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744403.png" alt="image-20250416084558583" style="zoom:67%;" />	

读锁只能读，会阻塞DML,DDL

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744754.png" alt="image-20250416085601187" style="zoom:67%;" />	

写锁是独占的，**只能自己读写**，其他客户端全部阻塞

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221744572.png" alt="image-20250416085544467" style="zoom:67%;" />	

------

### 2.元数据锁（meta data lock， MDL）

5.5后引入，**自动上锁**

访问一张表，元数据锁自动加

元数据简单来说就是表结构，来维护表结，构其主要目的是在事务未提交时，防止其他事务修改表结构，避免DDL和DML语句冲突

**对表进行增删改查的时候，会加MDL读锁（共享），会阻塞DDL语句**

**对表结构进行变更（DDL），会加MDL写锁（排他），会阻塞DML,DQL语句**

![image-20250416090835958](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745923.png)	

共享就是说，两个事务可以持有同一把锁，对于元数据锁，一个事务增删改查的时候，另一个事务同样可以增删改查，但是都不能修改表结构

但是修改表结构的时候，是排他写锁，同一时刻只能有一个事务持有这个锁，只能我自己修改表结构（不能进行增删改查）

其他事务不能修改表结构，同样也不能增删改查表数据

查看元数据锁：

```mysql
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.metadata_locks;
```

---

### 3.意向锁

**只有在InnoDB引擎中有**，目的是解决行锁和表锁的冲突问题

在**InnoDB**中，事务A修改一行记录的时候会自动加**行锁**，这时候事务B来给整张表加表锁，会检索这张表有没有行锁，有的话就加锁失败

意向锁的出现是为了防止全局检索行锁

![image-20250417100759045](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745563.png)	

事务A修改行记录的时候会加行锁和整张表的意向锁，事务B对这张表上表锁（读锁，写锁）会检索意向锁，查看与意向锁的兼容情况来决定是否上锁

![image-20250417100923214](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745760.png)	

![image-20250417101007105](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745973.png)	

查看意向锁加锁情况

```mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
```

------

## 三.行级锁

![image-20250417102218951](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745588.png)	

### 1.行锁（Record Lock）

记录锁

锁定单个行记录的锁，防止其他事务对此进行**update和delete**。在RC(读已提交),RR(可重复读)隔离级别下都支持

![image-20250417102405477](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745421.png)	

### 2.间隙锁（Gap Lock）

锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙**insert**，产生**幻读**，在RR隔离级别下支持

![image-20250417102536528](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745798.png)

![image-20250421114018692](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745647.png)	

**间隙锁可以共存**

### 3.临键锁（Next-Key Lock）

行锁和间隙锁的组合，同时锁住数据，并锁住数据前面的间隙Gap，在RR隔离级别下支持

![image-20250417102755159](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745398.png)	

---

![image-20250421105640473](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745719.png)	

SELECT ... FOR SHARE和SELECT ... IN SHARE MODE效果一致

![image-20250421112147568](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221745816.png)	

共享锁之间兼容，可以对同一行数据执行两条SELECT ... FOR SHARE语句，此时会有四个锁，两个意向共享锁，两个共享记录锁

查看意向锁和行锁的加锁情况

```mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
```

---

**1.唯一索引，等值查询，存在记录**

加行记录锁

**2.唯一索引，等值查询，不存在记录**

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746789.png" alt="image-20250422150242315" style="zoom:67%;" />	

**3.普通索引，等值查询**

如果是**普通索引**，因为不唯一，所以要防止插入同一个索引的数据，除了给匹配的记录加行锁，还会对这些记录**前后**的间隙加间隙锁

![image-20250422151444328](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746321.png)	

**4.唯一索引，范围查询**

![image-20250422153017477](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746714.png)	

执行where id >=12，会锁住 [12,+♾️）

**5.普通索引，范围查询**

执行where id >=12，会锁住（6,+♾️）

--------

# MySQL日志

## 错误日志

![image-20250422171124442](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746381.png)	

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746213.png" alt="image-20250422171436290" style="zoom:67%;" />	

错误日志是**mysql服务级别**的异常

实时查看log文件尾部追加日志

```
tail -f /var/log/mysqld.log
```

-------

## 二进制日志(BINGLOG)

包含所有DDL，DML，**不包含DQL查询语句**

![image-20250422174811004](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221748047.png)	

**MySQL 5.x默认没有开启二进制日志**

### 日志格式

![image-20250422172159519](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746656.png)

---

### 查看日志

ROW格式日志，需要用-v参数

![image-20250422172447832](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746784.png)	

![image-20250422172330065](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746907.png)	

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746143.png" alt="image-20250422172536269" style="zoom:67%;" />		

@1表示第一个字段...

---

### 日志删除

![image-20250422172807510](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746203.png)	

![image-20250422172903247](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746879.png)	

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221746466.png" alt="image-20250422172941387" style="zoom:67%;" />	

默认30天删除

---

## 查询日志

记录所有SQL语句，默认不开启

![image-20250422173200448](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221747976.png)	

![image-20250422173614658](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221747085.png)	

---

## 慢查询日志

![image-20250422174047391](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221747836.png)	

```
tail -f localhost-slow.log
```

![image-20250422173506975](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221747531.png)	

同样在/etc/my.cnf文件中修改

```
vim/etc/my.cnf
```

1表示开启，2表示超过2s为慢sql

![image-20250422174334428](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221747877.png)	

慢查询日志结果

![image-20250422173944360](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504221747967.png)	
