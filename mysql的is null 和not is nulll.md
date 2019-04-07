#### mysql的**is null** 和**not is nulll**
> 在mysql中当select查询值为null的时候，不能直接列名=null 或者！=null,这个的结果都会为空，那为什么呢？

我们创建一个表：

    create  table  test_null(id tinyint(1) 
             auto_increment,
             name varchar(10) ,
             primary key (id));
插入几条数据：

    insert into test_null values(1,'dwd'),(2,'dwdw'),(3,null),(4,null),(5,'dwfef');
我们做几条查询看看

    select * from test_null where name=null;
    select * from test_null where name!=null;
    /* 这两个查询的结果都是什么都没有！*/
带有is null的查询

    select * from test_null where name is null;
结果为：

    +----+------+
    | id | name |
    +----+------+
    |  3 | NULL |
    |  4 | NULL |
    +----+------+
带 is not null 的查询

    select * from test_null where name  is not null;
结果为：

    enter code here
    +----+-------+
    | id | name  |
    +----+-------+
    |  1 | dwd   |
    |  2 | dwdw  |
    |  5 | dwfef |
    +----+-------+
 > 从上面几个查询可以看出，和null的比较，结果都为null，所以在查询null值得时候要使用**is null**，在查询不为null的时候要用**is not null**










    












 