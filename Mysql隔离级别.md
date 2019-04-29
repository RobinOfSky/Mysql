### Mysql隔离级别

#### 四种隔离级别
- **read uncommitted 读未提交的**：这种隔离级别会导致，脏读，不可重复读，幻读。
- **read committed 读已提交**：这种隔离级别会导致不可重复读和幻读
- **repeatable read 重复读取**：这种隔离级别会导致幻读。但是通过mvcc（并行化版本控制）解决了这一问题。
> 这也是mysql的默认隔离级别，其他数据看的默认隔离级别是read committed
- **serializable 串行化**：一个事物一个事物的排队进行执行。效率低。
***
#### 隔离级别的由来？是什么？怎么用？
- **what**:为什么有隔离级别这种东西呢？因为它存在所以他是为了解决某个问题提出的？那是什么问题呢？我们都知道事务的四大特征（原子性，一致性，隔离性，持久性）中的隔离性就是说事务的执行是一个一个排队执行的。我们想想用户少的时候，所请求的事务也是比较傲少的，可是对于很大的请求来说，那么串行化执行事务，那就是效率很低了，估计有的事务等个几个小时后才有机会被执行，所以为了避免这种情况的发生，我们采用**并发**去执行那些事务？[**并发**](https://blog.csdn.net/sinat_34082752/article/details/80680348)（点击这个链接去了解一下并发）。简单的介绍一下并发：不同的线程同一时间去获得系统资源去执行。逐字逐句的理解：**一起去执行**。
有了并发，我们不能忘了事务的隔离特征，所以我们既要保证隔离又要保证并发。并发和保证事务的一致性和完整性的关系是：一个高了另一个就低了。并发程度高了，就不太能保证事务的一致性以及完整性了。

##### **并发执行事务下又有不同的隔离级别应对不同的需求。**
   - [ ] read uncommitted：未提交就可以读，并发程度很高，但是会出现很多问题，脏写，脏读，不可重复读，幻读。
**为了演示事务的隔离级别，我们创建一个test表**

```
create table test(id tinyint(1)auto_increment ,
                  name varchar(10) not null ,
                  primary key (id)
)engine=innodb,charset =utf8mb4;
```


   首先开启**两个session**，在**session1**中开启一个事务1，在**session2**中不开启事务。
在**session1**中执行如下语句：
**设置和修改事务的隔离级别**

```
set session transaction isolation level  read uncommitted ;#设置read committed的隔离级别
select @@transaction_isolation;  #查看隔离级别
```
```
begin;
insert into test values (1,'robin');
```
在**session2中查看**

```
select * from test;
```
结果为：

```
+----+-------+
| id | name  |
+----+-------+
|  1 | robin |
+----+-------+
```
但是**session1**由于某种情况，不得不回滚刚才的操作

```
rollback;
```
所以**session2**读取到了不存在的数据。这就是**脏读**。
> 如果**session1**update 数据，然后提交（隐式或显示），那么**session2**也能读到最近更新的数据，而不是以前的那条数据，所以不能重复读取同一数据。这就是**不可重复读**
> 如果**session1**insert或者delete数据，然后提交（隐式或显示），那么**session2**将读取到的数据行数和刚才刚才读取到的不一样。这就是**幻读**

***
   - [ ] read committed 提交读
   - [ ] repeatable read 可重复读
   - [ ] serializable 串行化
上面三个隔离级别亦可以意义测试。

```
脏读：读取到了不存在的数据（修改回滚）
不可重复读：读取到的数据不一样（修改提交）
幻读：读取到的行数发生了变化（增加/删除，提交）
隔离级别是为了解决上面三个问题提出的。不同的隔离级别能解决一定的问题。
```

|隔离级别     |     脏读 |   不可重复读   | 幻读  |
| :-------- | --------:| :------: | :------: |
| read uncommitted    |   NO |  NO| NO|
| read committed     |     YES|   NO| NO|
| repeatable read   |   YES |  YES| NO|
| seializable  |   YES|  YES| YES|
> repeatable read隔离级别是可以避免幻读的，通过MVCC(多版本并发控制)解决的。

- **how**:如何用？**按需**使用不同的隔离级别

脏读：读取到了不存在的数据（修改回滚）
不可重复读：读取到的数据不一样（修改提交）
幻读：读取到的行数发生了变化（增加/删除，提交）
隔离级别是为了解决上面三个问题提出的。不同的隔离级别能解决一定的问题。
***
###  MVCCA多版本并发控制
对于一条数据，进行多次update，会将以前的数据加入undo log ，而每次的更新操作，有个隐藏列会将指针指向undo log ，这样就形成了一个链表，这个东西叫版本链。
- read uncommitted ：会访问最新的版本，也就是头结点。
- serializable：会通过加锁的方式来访问新的版本。
- read committed：会访问最新提交的版本
- repeatable:会访问第一次select语句的第一条数据。

> read committed 和repeatable read 访问版本需要判断是否版本链中的版本是否可见，为此引入了readview。来解决要访问那个版本来读取数据。

readview的组成：
- **m_ids**:表示在生成readview时点钱系统中活跃的读写事务的id列表
- **min_trx_id**:表示在省城readview时当前系统中活跃的读写事务中最小的事务id，也就是m_ids的最小值。
- **max_trx_id**：表示生成readview时双系统中该分配给下一个事务的id的值
- **creator_trx_id**:表示生成readview的事务的事务的id
**那怎么判断版本是否可见呢？**
- [ ]如果被访问版本的trx_id属性值与readview中的create_trx_id相等，那么意味着当前事务正在访问修改过的数据，所以该版本可以被当前事务访问。
- [ ]如果被访问版本的trx_id小于readview中的min_trx_id，表明生成该版本的事务在当前事务生成readview前已经提交，所以可以。
- [ ]如果被访问版本的trx_id属性值大于ReadView中的max_trx_id值，表明生成该版本的事务在当前事务生成ReadView后才开启，所以该版本不可以被当前事务访问。
- [ ]如果被访问版本的trx_id属性值在ReadView的min_trx_id和max_trx_id之间，那就需要判断一下trx_id属性值是不是在m_ids列表中，如果在，说明创建ReadView时生成该版本的事务还是活跃的，该版本不可以被访问；如果不在，说明创建ReadView时生成该版本的事务已经被提交，该版本可以被访问。
> 如果某个版本的数据对当前事务不可见的话，那就顺着版本链找到下一个版本的数据，继续按照上边的步骤判断可见性，依此类推，直到版本链中的最后一个版本。如果最后一个版本也不可见的话，那么就意味着该条记录对该事务完全不可见，查询结果就不包含该记录。


在MySQL中，READ COMMITTED和REPEATABLE READ隔离级别的的一个非常大的区别就是它们生成ReadView的时机不同。
- READ COMMITTED —— 每次读取数据前都生成一个ReadView
- REPEATABLE READ —— 在第一次读取数据时生成一个ReadView
> 生成ReadView的时机不同，READ COMMITTD在每一次进行普通SELECT操作前都会生成一个ReadView，而REPEATABLE READ只在第一次进行普通SELECT操作前生成一个ReadView，之后的查询操作都重复使用这个ReadView就好了。

read committed 提交过的数据都能被访问到。
repeatable read 只会访问到第一次查询的第一条结果。
