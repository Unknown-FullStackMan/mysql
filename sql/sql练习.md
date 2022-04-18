

## 高级sql语句练习



### case when

```sql
（1）case when xx + 条件  then 条件 else end

（2）case  xx when 条件  then 条件 else end
```



1. 一表user存储如下数据

![image-20220415112908926](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415112908926.png)

​		实现效果：

![image-20220415113007069](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415113007069.png)

​		sql语句：

```sql
SELECT name as 姓名,
 sum(CASE degree WHEN 'A' THEN 1  WHEN 'a' THEN 1 ELSE 0 END) as A,
 sum(CASE degree WHEN 'B' THEN 1  WHEN 'b' THEN 1 ELSE 0 END) as B,
 sum(CASE degree WHEN 'C' THEN 1  WHEN 'c' THEN 1 ELSE 0 END) as C
 FROM score GROUP BY `name`
```





### left join

LEFT JOIN 关键字会从左表 (table_name1) 那里返回所有的行，即使在右表 (table_name2) 中没有匹配的行



1. t1表数据：

![image-20220415142836739](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415142836739.png)

​		t2表数据：

​																![image-20220415142922444](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415142922444.png)

​		实现效果：

![image-20220415143008076](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415143008076.png)

​		sql语句：

      ``` sql
SELECT
 t1.id,
 t1.`name`,
 (
  SELECT
   t3phonenum01.phonenum
  WHERE
   t1.phonenum01 = t3phonenum01.id
 ) AS phonenum01,
 (
  SELECT
   t3phonenum02.phonenum
  WHERE
   t1.phonenum02 = t3phonenum02.id
 ) AS phonenum02,
 (
  SELECT
   t3phonenum03.phonenum
  WHERE
   t1.phonenum03 = t3phonenum03.id
 ) AS phonenum03
 FROM t1
left JOIN t3 as t3phonenum01 ON t1.phonenum01 = t3phonenum01.id
left JOIN t3 as t3phonenum02 ON t1.phonenum02 = t3phonenum02.id
left JOIN t3 as t3phonenum03 ON t1.phonenum03 = t3phonenum03.id
      ```



### 综合练习

题目描述：

```sql
--1.学生表
Student(SID,Sname,Sage,Ssex) --SID 学生编号,Sname 学生姓名,Sage 出生年月,Ssex 学生性别
--2.课程表
Course(CID,Cname,TID) --CID --课程编号,Cname 课程名称,TID 教师编号
--3.教师表
Teacher(TID,Tname) --TID 教师编号,Tname 教师姓名
--4.成绩表
SC(SID,CID,score) --SID 学生编号,CID 课程编号,score 分数
*/
--创建测试数据
create table Student(SID varchar(10),Sname nvarchar(10),Sage datetime,Ssex nvarchar(10));
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
create table Course(CID varchar(10),Cname nvarchar(10),TID varchar(10));
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
create table Teacher(TID varchar(10),Tname nvarchar(10));
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
create table SC(SID varchar(10),CID varchar(10),score decimal(18,1));
insert into SC values('01' , '01' , 80);
insert into SC values('01' , '02' , 90);
insert into SC values('01' , '03' , 99);
insert into SC values('02' , '01' , 70);
insert into SC values('02' , '02' , 60);
insert into SC values('02' , '03' , 80);
insert into SC values('03' , '01' , 80);
insert into SC values('03' , '02' , 80);
insert into SC values('03' , '03' , 80);
insert into SC values('04' , '01' , 50);
insert into SC values('04' , '02' , 30);
insert into SC values('04' , '03' , 20);
insert into SC values('05' , '01' , 76);
insert into SC values('05' , '02' , 87);
insert into SC values('06' , '01' , 31);
insert into SC values('06' , '03' , 34);
insert into SC values('07' , '02' , 89);
insert into SC values('07' , '03' , 98);
```

​	

![image-20220415151840133](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415151840133.png)

![image-20220415151854829](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415151854829.png)

![image-20220415151905714](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415151905714.png)



1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数?

第一种情况：学生上课的课程同时都有"01"课程和"02"课程的情况

```sql
select a.* , b.score 课程01的分数,c.score 课程02的分数 from Student a , SC b , SC c
where a.SID = b.SID and a.SID = c.SID and b.CID = '01' and c.CID = '02' and b.score > c.score
```

第二种情况：学生上课的课程同时都有"01"课程和"02"课程的情况和学生上课的课程只有"01"课程没有"02"课程的情况

#### 嵌套查询（）

```sql
SELECT * FROM (select a.* , b.score 课程01的分数,c.score 课程02的分数 from Student a 
left join SC b on a.SID = b.SID and b.CID = '01' 
left join SC c on a.SID = c.SID and c.CID = '02' ) as d WHERE  d.`课程01的分数` is NOT NULL  and (d.`课程01的分数` > d.`课程02的分数` or d.`课程02的分数` is NULL )
```

分析：

![image-20220415153331708](D:\IDEAWorkSpace\mysql\sql\sql练习.assets\image-20220415153331708.png)



2.查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩？

#### cast（）

把一种类型转换成另外一种类型

#### avg() 

求平均数函数

#### having

对结果进行过滤，select 语句执行顺序大致是：where（数据查询） -> group by（数据编组） -> having（结果过滤） -> order by（排序）

#### any_value（）

会选择被分到同一组的数据里第一条数据的指定列值作为返回数据

#### DECIMAL(18,2)

最大存放18位的数值，小数点左 + 小数点右的位数。并保留2位小数。

```sql
SELECT a.SID,ANY_VALUE( a.Sname) as Sname, cast(AVG(b.score) AS DECIMAL(18,2))  avg_score FROM student a, sc b WHERE a.SID = b.SID GROUP BY a.SID HAVING avg_score >= 60 ORDER BY a.SID 


或者：

SELECT a.SID,a.Sname, cast(AVG(b.score) AS DECIMAL(18,2))  avg_score FROM student a, sc b WHERE a.SID = b.SID GROUP BY a.SID,a.Sname HAVING avg_score >= 60 ORDER BY a.SID 
 
 GROUP BY 后面跟的参数与前面select的参数要对应，或者使用ANY_VALUE（）避免这种情况，否则报错：this is incompatible with sql_mode=only_full_group_by。
 具体原因：https://blog.csdn.net/u012660464/article/details/113977173
```



3. 查询在sc表中不存在成绩的学生信息的SQL语句?

#### distinct 

去重

```sql
select DISTINCT a.* from Student a , sc b WHERE a.SID not in (SELECT SID FROM sc)
```



	4. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩?

#### count() 

#### sum()

```sql
4.1 查询所有有成绩的SQL
SELECT a.SID 学生编号, a.Sname 学生姓名, COUNT(b.CID) 选课总数, SUM(score)  FROM student a, sc b WHERE a.SID = b.SID 
GROUP BY a.SID,a.Sname

4.2 查询所有(包括有成绩和无成绩)的SQL
SELECT a.SID 学生编号, a.Sname 学生姓名, COUNT(b.CID) 选课总数, SUM(score)  FROM student a  left join sc b
on a.SID = b.SID GROUP BY a.SID,a.Sname
```



	5. 查询"李"姓老师的数量

#### like 

模糊匹配

#### left()

从字符表达式最左边一个字符开始返回指定数目的字符.若 b 的值大于 a 的长度,则返回字符表达式的全部字符a.如果 b 为负值或 0,则返回空字符串.



```sql
select count(Tname)  李姓老师的数量  from teacher where Tname like '李%'

select count(Tname)  李姓老师的数量  from Teacher where left(Tname,1) = '李'
```



6. 查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

#### exists

存在某行记录

#### in

```sql
select Student.* from Student , SC where Student.SID = SC.SID and SC.CID = '01' and exists (Select 1 from SC SC_2 where SC_2.SID = SC.SID and SC_2.CID = '02')

或者

select Student.* from Student , SC where Student.SID = SC.SID and SC.CID = '01' and Student.SID  in (Select SC_2.SID from SC SC_2 where SC_2.SID = SC.SID and SC_2.CID = '02') order by Student.SID
```



7. 查询没有学全所有课程的同学的信息

```sql
select Student.*
from Student , SC
where Student.SID = SC.SID
group by Student.SID , Student.Sname , Student.Sage , Student.Ssex having count(CID) < (select count(CID) from Course)
```



8. 查询至少有一门课与学号为"01"的同学所学相同的同学的信息

```sql
select distinct Student.* from Student , SC where Student.SID = SC.SID and SC.CID in (select CID from SC where SID = '01') and Student.SID <> '01'
```



9. 查询和"01"号的同学学习的课程完全相同的其他同学的信息

```sql
select Student.* from Student where SID in
(select distinct SC.SID from SC where SID <> '01' and SC.CID in (select distinct CID from SC where SID = '01')
group by SC.SID having count(1) = (select count(1) from SC where SID='01'))
```

10. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```sql
select a.SID 学生编号 , a.Sname 学生姓名 ,
       max(case c.Cname when '语文' then b.score else null end)  语文 ,
       max(case c.Cname when '数学' then b.score else null end)  数学 ,
       max(case c.Cname when '英语' then b.score else null end)  英语 ,
       cast(avg(b.score) as decimal(18,2)) 平均分
from Student a
left join SC b on a.SID = b.SID
left join Course c on b.CID = c.CID
group by a.SID , a.Sname
order by 平均分 desc

max换成sum能有相同结果。 每个查询字段都是聚合查询结果
```



11. 统计各科成绩各分数段人数：课程编号,课程名称, 100-85 , 85-70 , 70-60 , 0-60 

```sql
--横向显示
select Course.CID  课程编号  , Cname as  课程名称  ,
	sum(case when score >= 85 then 1 else 0 end)  '85-100' ,
	sum(case when score >= 70 and score < 85 then 1 else 0 end) as '70-85' ,
	sum(case when score >= 60 and score < 70 then 1 else 0 end)  as '60-70' ,
	sum(case when score < 60 then 1 else 0 end) '0-60 '
from sc , Course 
where SC.CID = Course.CID 
group by Course.CID , Course.Cname
order by Course.CID

--纵向显示
select m.CID  课程编号  , m.Cname  课程名称  ,  (
  case when n.score >= 85 then '85-100'
       when n.score >= 70 and n.score < 85 then '70-85'
       when n.score >= 60 and n.score < 70 then '60-70'
       else '0-60'
  end) 分数段 , 
  count(1) 数量 
from Course m , sc n
where m.CID = n.CID 
group by m.CID , m.Cname , 分数段
order by m.CID , m.Cname , 分数段
```



12. 统计各科成绩各分数段人数：课程编号,课程名称, 100-85 , 85-70 , 70-60 , <60 及所占百分比 

```sql
select m.CID 课程编号, m.Cname 课程名称,
  CONCAT_WS("  , ",(select count(1) from SC where CID = m.CID and score < 60) , cast((select count(1) from SC where CID = m.CID and score < 60)*100.0 / (select count(1) from SC where CID = m.CID) as decimal(18,2))) "0-60 , 百分比" ,
  CONCAT_WS("  , ",(select count(1) from SC where CID = m.CID and score >= 60 and score < 70) ,
  cast((select count(1) from SC where CID = m.CID and score >= 60 and score < 70)*100.0 / (select count(1) from SC where CID = m.CID) as decimal(18,2)))  "60-70 , 百分比" ,
  CONCAT_WS("  , ",(select count(1) from SC where CID = m.CID and score >= 70 and score < 85)   ,
  cast((select count(1) from SC where CID = m.CID and score >= 70 and score < 85)*100.0 / (select count(1) from SC where CID = m.CID) as decimal(18,2))) "70-85 , 百分比"  ,
  (select count(1) from SC where CID = m.CID and score >= 85)  "85-100" ,
  cast((select count(1) from SC where CID = m.CID and score >= 85)*100.0 / (select count(1) from SC where CID = m.CID) as decimal(18,2))  百分比 
from Course m 
order by m.CID
```













### 疑难杂症

 #### GROUP BY问题

例如：4.1中的sql

```sql
SELECT a.SID 学生编号, a.Sname 学生姓名, COUNT(b.CID) 选课总数, SUM(score)  FROM student a, sc b WHERE a.SID = b.SID 
GROUP BY a.SID,a.Sname
```

+ 情况1：去掉group by会导致

  1140 - In aggregated query without GROUP BY, expression #1 of SELECT list contains nonaggregated column 'testjoin.a.SID'; this is incompatible with sql_mode=only_full_group_by

  原因：使用了聚合函数count(),sum()肯定要用到group by。

+ 情况2：（a.SID不是student主键）只加一个字段进行group by，如只加a.SID

  1055 - Expression #2 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'testjoin.a.Sname' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by

+ 情况3：（a.SID是student主键）只加一个字段进行group by，如加a.Sname 

  1055 - Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'testjoin.a.SID' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by

+ 情况4：（a.SID是student主键）只加一个字段进行group by，如加a.SID

  不报错，查询正确！

+ 情况5：只加一个字段进行group by，如只加a.SID，如果在select的其他字段使用any_value()函数返回。

  不报错，查询正确！

根因：`sql_mode`设置，在`MySQL5.7.5`后，默认开启了`ONLY_FULL_GROUP_BY`。

具体解释：https://blog.csdn.net/u012660464/article/details/113977173









