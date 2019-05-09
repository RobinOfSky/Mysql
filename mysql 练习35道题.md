#### mysql 练习35道题
 
-- 部门表

```
create database test_mysql1;
use test_mysql1;
```

```
create table dept(
  deptno int auto_increment primary key comment '部门编号',
  deptname varchar(10) not null comment  '部门名称',
  loc varchar(5) not null comment '部门地址'
)engine=innodb,character set =utf8mb4;
```

```
create table emp(
  empno int auto_increment comment '员工号',
  empname varchar(10) not null comment '员工姓名',
  job varchar(10) not null comment '岗位名称',
  mgr int(4) not null comment '直属领导编号',
  hiredate date comment '雇用日期',
  sal float comment '薪水',
  comm int comment '提成',
  deptno  int not null comment '部门编号',
  primary key (empno),
  foreign key (deptno) references dept(deptno)
)engine=innodb,charset =utf8mb4;
```

```
insert into dept values(10,'财务部','北京');
insert into dept values(20,'研发部','上海');
insert into dept values(30,'销售部','广州');
insert into dept values(40,'行政部','深圳');
```

```
insert into emp values(7369,'刘一','职员',7902,'1980-12-17',800,null,20);
insert into emp values(7499,'陈二','推销员',7698,'1981-02-20',1600,300,30);
insert into emp values(7521,'张三','推销员',7698,'1981-02-22',1250,500,30);
insert into emp values(7566,'李四','经理',7839,'1981-04-02',2975,null,20);
insert into emp values(7654,'王五','推销员',7698,'1981-09-28',1250,1400,30);
insert into emp values(7698,'赵六','经理',7839,'1981-05-01',2850,null,30);
insert into emp values(7782,'孙七','经理',7839,'1981-06-09',2450,null,10);
insert into emp values(7788,'周八','分析师',7566,'1987-06-13',3000,null,20);
insert into emp values(7839,'吴九','总裁',7588,'1981-11-17',5000,null,10);
insert into emp values(7844,'郑十','推销员',7698,'1981-09-08',1500,0,30);
insert into emp values(7876,'郭十一','职员',7788,'1987-06-13',1100,null,20);
insert into emp values(7900,'钱多多','职员',7698,'1981-12-03',950,null,30);
insert into emp values(7902,'大锦鲤','分析师',7566,'1981-12-03',3000,null,20);
insert into emp values(7934,'木有钱','职员',7782,'1983-01-23',1300,null,10);
```

```
#1．列出至少有一个员工的所有部门。
select  dept.deptno, count(*) from emp  group by deptno;
select dept.*,count(empno)from dept left join  emp on dept.deptno=emp.deptno group by dept.deptno having count(empno)>=1;
#2．列出薪金比"刘一"多的所有员工。
select emp.* from emp where sal >(select emp.sal from emp where emp.empname='刘一');
explain select *from emp where sal >(select emp.sal from emp where emp.empname='刘一');# 执行时间是31ms
#3. 列出所有员工的姓名及其直接上级的姓名。(自关联)
select e1.empname ,e2.empname from emp e1 join emp e2 where e1.empno=e2.mgr  ;
#4．列出受雇日期早于其直接上级的所有员工。
select e1.empname,e2.empname from  emp e1 , emp e2 where e1.empno=e2.mgr and e1.hiredate<e2.hiredate ;
# 5．列出部门名称和这些部门的员工信息，同时列出那些没有员工的部门。
select dept.deptname, e.* from dept left join emp e on dept.deptno = e.deptno;
# 6．列出所有job为“职员”的姓名及其部门名称。
select d.deptname,e.empname from  dept d inner join emp e on d.deptno = e.deptno where job ='职员';
# 7．列出最低薪金大于1500的各种工作。
select  distinct emp.job from  emp where (sal+comm) >1500;
# 8．列出在部门 "销售部" 工作的员工的姓名，假定不知道销售部的部门编号。
select emp.empname from emp where deptno = (select dept.deptno from dept  where deptname='销售部');
select dept.deptno from dept  where deptname='销售部';
#9．列出薪金高于公司平均薪金的所有员工。
select empname,sal from emp where sal>(select avg(sal) from emp);
#10．列出与"周八"从事相同工作的所有员工
select empname,job from emp where job in (select job from emp where empname='周八');
#11．列出薪金等于部门30中员工的薪金的所有员工的姓名和薪金。
select * from emp where sal in (select sal from emp where deptno=30);
#13．列出在每个部门工作的员工数量、平均工资。
select deptno, count(empno),avg(sal) from emp group by deptno;
-- 14．列出所有员工的姓名、部门名称和工资。
select e.empname,d.deptname,e.sal from  dept d inner join emp e on d.deptno = e.deptno;
-- 15．列出所有部门的详细信息和部门人数。
select d.* ,count(empno) from dept d left join emp e on d.deptno = e.deptno group by deptno;
-- 16．列出各种工作的最低工资。
select job ,sal from emp where sal in(select min(sal) from emp group by job);
-- 17．列出各个部门的 经理 的最低薪金。
select job,min(sal) from emp  where    job='经理' group by deptno;
-- 18．列出所有员工的年工资,按年薪从低到高排序。
select empname,  (sal*12)year_sal from emp order by year_sal;
-- 19.查出emp表中薪水在3000以上（包括3000）的所有员工的员工号、姓名、薪水。
select  empno,empname,sal from emp where sal>=3000;
-- 20.查询出所有薪水在'陈二'之上的所有人员信息。
select empname,sal from emp where sal>(select sal from emp where empname='陈二');
-- 21.查询出emp表中部门编号为20，薪水在2000以上（不包括2000）的所有员工，显示他们的员工号，姓名以及薪水，
select empno,empname,sal from emp where deptno=20 and sal>2000;
-- 22.查询出emp表中所有的工作种类（无重复）
select distinct job from  emp;
-- 23.查询出所有奖金（comm）字段不为空的人员的所有信息。
select * from  emp where comm is not null;
-- 24.查询出薪水在800到2500之间（闭区间）所有员工的信息。（注：使用两种方式实现and以及between and）
select * from emp where sal>=800 and sal<=2500;
select * from emp where sal between 800 and 2500;
-- 25.查询出员工号为7521，7900，7782的所有员工的信息。（注：使用两种方式实现，or以及in）
select * from emp where empno =7521 or empno=7900 or empno=7782;
select * from emp where empno in (7521,7900,7782);
-- 26.查询出名字中有“张”字符，并且薪水在1000以上（不包括1000）的所有员工信息。
select * from emp where empname like '%张%' and sal>1000;
-- 27.查询出名字第三个汉字是“多”的所有员工信息。
select * from emp where empname like '__多';
-- 28.将所有员工按薪水升序排序，薪水相同的按照入职时间降序排序。
select * from emp order by sal ,hiredate desc;
-- 29.将所有员工按照名字首字母升序排序，首字母相同的按照薪水降序排序。 order by convert(name using gbk) asc;
select * from emp order by convert(substring(empname,1,1) using gbk) asc ,sal desc;
-- 30.查询出最早工作的那个人的名字、入职时间和薪水。
select empname,hiredate,sal from emp where hiredate =(select min(hiredate) from emp );
-- 31.显示所有员工的名字、薪水、奖金，如果没有奖金，暂时显示100.
select empname,sal,ifnull(comm,100) from emp ;
-- 32.显示出薪水最高人的职位。
select job from emp where sal=(select max(sal) from emp);
-- 33.查出emp表中所有部门的最高薪水和最低薪水，部门编号为10的部门不显示。
select empname from emp where deptno <>10 group by deptno;
-- 34.删除10号部门薪水最高的员工。
delete from emp where empno=(select empno from (select empno from emp where  sal>=all(select sal from emp where deptno=10))as temp);
-- 36.查询员工姓名，工资和 工资级别(工资>=3000 为3级，工资>2000 为2级，工资<=2000 为1级)
-- 语法：case when ... then ... when ... then ... else ... end
select empname,sal,case
when sal>3000 then '三级'
when sal>2000 then '二级'
when sal<=2000 then '一级'
end as level
from  emp;
```