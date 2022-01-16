# SQL经典题练习

# 1 SQL技巧

## 1.1 SQL执行的顺序

### 1.1.1 普通子句：from > where > group by > select > order by

https://blog.csdn.net/qq_25186987/article/details/54311384

```sql
SELECT 
	emp_sex,
	COUNT(1) AS num
FROM 
	emp_baseinfo
WHERE
	emp_sex IS NOT NULL
GROUP BY
	emp_sex
ORDER BY
	emp_sex
```

先执行from关键字后面的语句，明确数据的来源，它是从哪张表取来的。

接着执行where关键字后面的语句，对数据进行筛选。

再接着执行group by后面的语句，对数据进行分组分类。

然后执行select后面的语句，也就是对处理好的数据，具体要取哪一部分。

最后执行order by后面的语句，对最终的结果进行排序。

### 1.1.2 结合Join语句

**sql执行顺序和join总结**链接：https://www.imooc.com/article/13273

**浅谈with cube与with rollup之区别** https://blog.csdn.net/iteye_4537/article/details/82342135

```
1.FROM
2.ON
3.JOIN
4.WHERE
5.GROUP BY
6.WITH CUBE or WITH ROLLUP 
7.HAVING
8.SELECT
9.DISTINCT
10.ORDER BY
11.TOP
```

on中不是最终过滤, 因为后面left join还可能添加回来, 而where才是最终过滤。只有当使用外连接(left, right)时, on 和 where 才有这个区别, 如果用inner join, 在哪里制定都一样, 因为on 之后就是where, 中间没有其它步骤

```sql
select * from table1 as a
inner join table2 as b on a.id=b.id and a.status=1
```

```sql
select * from table1 as a
inner join table2 as b on a.id=b.id
where a.status=1
```

如果是inner join, 放on和放where产生的结果一样, 但没说哪个效率速度更高? 如果有outer join (left or right), 就有区别了, 因为on生效在先, 已经提前过滤了一部分数据, 而where生效在后。综合一下, 感觉还是放在on里更有效率, 因为它先于where执行.

## 1.2 SQL的大小写不敏感

SQL对大小写不敏感https://www.zhihu.com/question/332929803/answer/1002003067

SQL是在70~80年出现并形成标准的，在那个时代流行的不少高级编程语言都是大小写不敏感的，例如BASIC、Lisp、COBOL、Pascal，还有汇编语言也是大小写不敏感的。

70年代随着Unix出现，C语言开始流行起来。C是大小写敏感的。之后的C++、Java、C#、Javascript、Go等等，都受到了C语言风格的影响。所以很大程度上，只是我们现在习惯了C风格的大小写敏感。

大小写敏感成为主流的还有一个原因是有助于保证代码的可读性。

# 2 SQL经典题

[B站视频讲解](https://www.bilibili.com/video/BV1pp4y1Q7Yv/)

[CSDN文字版](https://blog.csdn.net/flycat296/article/details/63681089)

[ @](https://www.cnblogs.com/dbskill/p/11212826.html)

[sql server 存入中文前加N](https://blog.csdn.net/wxc238351/article/details/62883373)

## 2.1 数据准备

由于数据蛙的是使用Sqlserver，我们这里使用Mysql，所以都没有#

> 1.学生表
> Student(Sid,Sname,Sage,Ssex) 
>
> Sid 学生编号,Sname 学生姓名,Sage 出生年月,Ssex 学生性别

> 2.课程表 
> Course(Cid,Cname,Tid) 
>
> Cid --课程编号,Cname 课程名称,Tid 教师编号

> 3.教师表 
> Teacher(Tid,Tname)
>
> Tid 教师编号,Tname 教师姓名

>  4.成绩表 
> SC(Sid,Cid,score)
>
> Sid 学生编号,Cid 课程编号,score 分数

需要保证创建的MySQL数据库是utf8mb4字符集，否则中文失效

学生表 Student

```sql
create table Student(Sid varchar(10),Sname varchar(10),Sage datetime,Ssex varchar(10));
insert into Student values('01', '赵雷', '1990-01-01', '男');
insert into Student values('02', '钱电', '1990-12-21', '男');
insert into Student values('03', '孙风', '1990-05-20', '男');
insert into Student values('04', '李云', '1990-08-06', '男');
insert into Student values('05', '周梅', '1991-12-01', '女');
insert into Student values('06', '吴兰', '1992-03-01', '女');
insert into Student values('07', '郑竹', '1989-07-01', '女');
insert into Student values('08', '王菊', '1990-01-20', '女');
```

科目表 Course

```sql
create table Course(Cid varchar(10),Cname varchar(10),Tid varchar(10));
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
```

教师表 Teacher

```sql
create table Teacher(Tid varchar(10),Tname varchar(10));
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
```

成绩表 SC

```sql
create table SC(Sid varchar(10),Cid varchar(10),score decimal(18,1));
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



## 2.2 题目一览

### 1 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数

**知识点：select 形成表A的时候要先加where，否则join的时候效率会慢很多**

**知识点：a join b 后形成新的表 c，执行sql语句时前面还要加select才能取到结果**

<font color=red>问题：为什么where的时候'01'和01都可以？建表的是varchar呀？</font>

```sql
select A.*,B.Cid,B.score from (select * from SC where Cid='01')A 
left join(select * from SC where Cid='02')B 
on A.Sid=B.Sid
where A.score>B.score;
```

```
+-----+-----+-------+-----+-------+
| Sid | Cid | score | Cid | score |
+-----+-----+-------+-----+-------+
| 02  | 01  | 70.0  | 02  | 60.0  |
| 04  | 01  | 50.0  | 02  | 30.0  |
+-----+-----+-------+-----+-------+
2 rows in set
```

### 2 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩

**知识点：select 表A, 表B 是笛卡尔积，最好把中间,写成inner join**

**知识点：在SQL中增加HAVING子句原因是，WHERE关键字无法与聚合函数一起使用，而Having可以[having函数](https://www.runoob.com/sql/sql-having.html)**

```sql
select s.sid, sname, avg(score) as avg_score
from Student as s inner join sc -- s, sc 等同于 s inner join sc
where s.sid = sc.sid 
group by s.sid
having avg_score > 60;
```

```
+-----+-------+-----------+
| sid | sname | avg_score |
+-----+-------+-----------+
| 01  | 赵雷  | 89.66667  |
| 02  | 钱电  | 70.00000  |
| 03  | 孙风  | 80.00000  |
| 05  | 周梅  | 81.50000  |
| 07  | 郑竹  | 93.50000  |
+-----+-------+-----------+
5 rows in set
```

### 3 查询在 SC 表存在成绩的学生信息

**知识点：对于是否存在的集合类型判断，可以从字表中先构造类似集合的查询结果**

```sql
select * from student where sid in (select sid from sc where score is not null);
```

```
+-----+-------+---------------------+------+
| Sid | Sname | Sage                | Ssex |
+-----+-------+---------------------+------+
| 01  | 赵雷  | 1990-01-01 00:00:00 | 男   |
| 02  | 钱电  | 1990-12-21 00:00:00 | 男   |
| 03  | 孙风  | 1990-05-20 00:00:00 | 男   |
| 04  | 李云  | 1990-08-06 00:00:00 | 男   |
| 05  | 周梅  | 1991-12-01 00:00:00 | 女   |
| 06  | 吴兰  | 1992-03-01 00:00:00 | 女   |
| 07  | 郑竹  | 1989-07-01 00:00:00 | 女   |
+-----+-------+---------------------+------+
7 rows in set
```

### 4 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )

**知识点：groupby后的聚合函数可以在join后再用！如果不用聚合函数而是直接取*那么会返回聚合后的第一条记录**

**知识点：要显示为null，所以要用left join**

```sql
-- 可读性更强的写法：
select S.sid, s.sname, count(cid) as 选课总数, sum(score) as 总成绩
from student as s left join sc
on s.sid = sc.sid
group by s.sid
```

```sql
-- 自己写的，先去计算了再去join
select S.Sid, Sname, t_course, t_score from Student as S
left join (select Sid, count(*) as t_course, sum(score) as t_score from SC group by Sid) SCC
on S.Sid = SCC.Sid;
```

```
+-----+-------+----------+---------+
| Sid | Sname | t_course | t_score |
+-----+-------+----------+---------+
| 01  | 赵雷  |        3 | 269.0   |
| 02  | 钱电  |        3 | 210.0   |
| 03  | 孙风  |        3 | 240.0   |
| 04  | 李云  |        3 | 100.0   |
| 05  | 周梅  |        2 | 163.0   |
| 06  | 吴兰  |        2 | 65.0    |
| 07  | 郑竹  |        2 | 187.0   |
| 08  | 王菊  | NULL     | NULL    |
+-----+-------+----------+---------+
8 rows in set
```

### 5 查询「李」姓老师的数量

```sql
select count(*) from Teacher where Tname like "李%";
```

```
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set
```

### 6 查询学过「张三」老师授课的同学的信息

**知识点：[连续join多个表](https://www.cnblogs.com/laopo/p/4146876.html)**

**知识点：[SQL JOIN 中 on 与 where 的区别](https://www.runoob.com/w3cnote/sql-join-the-different-of-on-and-where.html)**

```sql
select * from student where sid in ( 
    select sid from sc, course, teacher -- 这里的两个,相当于两个outer join
    on sc.cid = course.cid -- 这里用where和on都有结果
     and course.tid = teacher.tid
     and tname = '张三'
);
```

```
+-----+-------+---------------------+------+
| Sid | Sname | Sage                | Ssex |
+-----+-------+---------------------+------+
| 01  | 赵雷  | 1990-01-01 00:00:00 | 男   |
| 02  | 钱电  | 1990-12-21 00:00:00 | 男   |
| 03  | 孙风  | 1990-05-20 00:00:00 | 男   |
| 04  | 李云  | 1990-08-06 00:00:00 | 男   |
| 05  | 周梅  | 1991-12-01 00:00:00 | 女   |
| 07  | 郑竹  | 1989-07-01 00:00:00 | 女   |
+-----+-------+---------------------+------+
6 rows in set
```

**对于用where或join on的形式进行以下探究：**

形式一：outer join + where 正确

```sql
select sid from sc, course, teacher
where sc.cid = course.cid
and course.tid = teacher.tid
and tname = '张三';
```

形式二：inner join + on 正确

```sql
select sid from sc inner join course inner join teacher
on sc.cid = course.cid
and course.tid = teacher.tid
and tname = '张三';
```

形式三：inner join + where 正确

```sql
select sid from sc inner join course inner join teacher
where sc.cid = course.cid
and course.tid = teacher.tid
and tname = '张三';
```

**形式四：outer join  + on 报错！**TODO：后面再探究

```sql
select sid from sc outer join course outer join teacher
on sc.cid = course.cid
and course.tid = teacher.tid
and tname = '张三';
```

**形式五：inner join不写on，结果也是笛卡尔积等同于 outer join**

```
select sid from sc inner join course inner join teacher
```

### 7 查询没有学全所有课程的同学的信息

```sql
select * from student where sid 
in (select sid from sc group by sid having count(cid) < 3);
```

```
+-----+-------+---------------------+------+
| Sid | Sname | Sage                | Ssex |
+-----+-------+---------------------+------+
| 05  | 周梅  | 1991-12-01 00:00:00 | 女   |
| 06  | 吴兰  | 1992-03-01 00:00:00 | 女   |
| 07  | 郑竹  | 1989-07-01 00:00:00 | 女   |
+-----+-------+---------------------+------+
3 rows in set
```

### 8 查询和" 01 "号的同学学习的课程完全相同的其他同学的信息

**知识点：[mysql之group_concat函数详解](https://blog.csdn.net/u012620150/article/details/81945004)**

原解法存在问题：

```sql
select * from Student
where Sid in(
    select Sid from SC
    where Cid in (select Cid from SC where Sid = '01') and Sid <>'01'
    group by Sid
    having COUNT(Cid)>=3 -- 这里其实有问题，就是他已经知道这个同学是3门课了，而且总共就3门课
);
```

下面是更正确的解法：

```sql
SELECT * FROM student WHERE SId in
(
select sid from sc where sid<>'01' group by sid -- 取出不是01学生的cid 并排序 和 取出01学生的cid并排序 进行比较
having group_concat(cid ORDER BY cid) = (select group_concat(cid ORDER BY cid) from SC where sid = '01')
);
```

```
+-----+-------+---------------------+------+
| Sid | Sname | Sage                | Ssex |
+-----+-------+---------------------+------+
| 02  | 钱电  | 1990-12-21 00:00:00 | 男   |
| 03  | 孙风  | 1990-05-20 00:00:00 | 男   |
| 04  | 李云  | 1990-08-06 00:00:00 | 男   |
+-----+-------+---------------------+------+
3 rows in set
```

### 9 查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息

```sql
select * from Student where Sid in 
(select distinct(Sid) from SC where Cid in  -- 只要有一门课在就行，这里distinct一下
	(select Cid from sc where sid = '01') -- 取出01同学上过的所有的课，因为一门课只上一次不用distinct
);
```

```
+-----+-------+---------------------+------+
| Sid | Sname | Sage                | Ssex |
+-----+-------+---------------------+------+
| 01  | 赵雷  | 1990-01-01 00:00:00 | 男   |
| 02  | 钱电  | 1990-12-21 00:00:00 | 男   |
| 03  | 孙风  | 1990-05-20 00:00:00 | 男   |
| 04  | 李云  | 1990-08-06 00:00:00 | 男   |
| 05  | 周梅  | 1991-12-01 00:00:00 | 女   |
| 06  | 吴兰  | 1992-03-01 00:00:00 | 女   |
| 07  | 郑竹  | 1989-07-01 00:00:00 | 女   |
+-----+-------+---------------------+------+
7 rows in set
```

### 10 查询没学过"张三"老师讲授的任一门课程的学生姓名

**知识点：一般涉及到"任意"的都会用到not in这样的取反的结构**

```sql
select sname from student
where sname not in (
    select s.sname
    from student as s, course as c, teacher as t, sc
    where s.sid = sc.sid -- 感觉这种暴力笛卡尔积不好 
        and sc.cid = c.cid 
        and c.tid = t.tid
        and t.tname = '张三'
);
```

```
+-------+
| Sname |
+-------+
| 吴兰  |
| 王菊  |
+-------+
2 rows in set
```

**自己认为对此进行了改进尝试：**

改进1：可以少去join一张表看Sid就可以了

```sql
select Sname from Student where Sid not in (
	select distinct(Sid) from SC, Course, Teacher
	where Teacher.Tname = '张三'
    and SC.Cid = Course.Cid 
    and Course.Tid = Teacher.Tid
    and Teacher.Tname = '张三'
);
```

改进2：可以先不着急笛卡尔积，多查询几次性能会更好

```sql
select Sname from Student where Sid not in (
	select distinct(Sid) from SC where Cid in(
		select Cid from Course where Tid in(
			select Tid from Teacher where Tname = "张三"
		)
	)
);
```

### 11 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

**注意：这里题目表述有问题，应该是这些同学不及格的两门的平均成绩**

```sql
select s.sid, s.sname, avg(score)
from student as s, sc
where s.sid = sc.sid and score<60
group by s.sid
having count(score)>=2;
```

```
+-----+-------+------------+
| Sid | Sname | avg(score) |
+-----+-------+------------+
| 04  | 李云  | 33.33333   |
| 06  | 吴兰  | 32.50000   |
+-----+-------+------------+
2 rows in set
```

### 12 检索" 01 "课程分数小于 60，按分数降序排列的学生信息

看到降序，而降序的表又不是来自于主表，则需要join

```sql
select S.*, SC.score from 
Student as S inner join SC on S.Sid = SC.Sid
and SC.Cid = '01'
and SC.score < 60
order by SC.Score desc;
```

```
+-----+-------+---------------------+------+-------+
| Sid | Sname | Sage                | Ssex | score |
+-----+-------+---------------------+------+-------+
| 04  | 李云  | 1990-08-06 00:00:00 | 男   | 50.0  |
| 06  | 吴兰  | 1992-03-01 00:00:00 | 女   | 31.0  |
+-----+-------+---------------------+------+-------+
2 rows in set
```

### 13 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

**知识点：[case when](https://blog.csdn.net/rongtaoup/article/details/82183743) 配合聚合函数使用**

```sql
select sid,
    sum(case when cid=01 then score else null end) as score_01,
    sum(case when cid=02 then score else null end) as score_02,
    sum(case when cid=03 then score else null end) as score_03,
    avg(score)
from sc group by sid
order by avg(score) desc;
```

```
+-----+----------+----------+----------+------------+
| sid | score_01 | score_02 | score_03 | avg(score) |
+-----+----------+----------+----------+------------+
| 07  | NULL     | 89.0     | 98.0     | 93.50000   |
| 01  | 80.0     | 90.0     | 99.0     | 89.66667   |
| 05  | 76.0     | 87.0     | NULL     | 81.50000   |
| 03  | 80.0     | 80.0     | 80.0     | 80.00000   |
| 02  | 70.0     | 60.0     | 80.0     | 70.00000   |
| 04  | 50.0     | 30.0     | 20.0     | 33.33333   |
| 06  | 31.0     | NULL     | 34.0     | 32.50000   |
+-----+----------+----------+----------+------------+
7 rows in set
```

### 14 查询各科成绩最高分、最低分和平均分

要求以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率(及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90）。
 要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

```sql
select c.cid as 课程号, c.cname as 课程名称, count(*) as 选修人数,
    max(score) as 最高分, min(score) as 最低分, avg(score) as 平均分,
    sum(case when score >= 60 then 1 else 0 end)/count(*) as 及格率,
    sum(case when score >= 70 and score < 80 then 1 else 0 end)/count(*) as 中等率,
    sum(case when score >= 80 and score < 90 then 1 else 0 end)/count(*) as 优良率,
    sum(case when score >= 90 then 1 else 0 end)/count(*) as 优秀率
from sc, course c
where c.cid = sc.cid
group by c.cid
order by count(*) desc, c.cid asc;
```





15. 按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺

15.1 按各科成绩进行排序，并显示排名， Score 重复时合并名次

16.  查询学生的总成绩，并进行排名，总分重复时保留名次空缺

16.1 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺

17. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比
18. 查询各科成绩前三名的记录
19. 查询每门课程被选修的学生数 
20. 查询出只选修两门课程的学生学号和姓名 
21. 查询男生、女生人数
22. 查询名字中含有「风」字的学生信息
23. 查询同名同性学生名单，并统计同名人数
24. 查询 1990 年出生的学生名单
25. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列
26. 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩 
27. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 
28. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）
29. 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数
30. 查询不及格的课程
31. 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名
32. 求每门课程的学生人数 
33. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
34. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
35. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 
36. 查询每门功成绩最好的前两名
37. 统计每门课程的学生选修人数（超过 5 人的课程才统计）。
38. 检索至少选修两门课程的学生学号 
39. 查询选修了全部课程的学生信息
40. 查询各学生的年龄，只按年份来算 
41. 按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一
42. 查询本周过生日的学生
43. 查询下周过生日的学生
44. 查询本月过生日的学生
45. 查询下月过生日的学生

## 2.2 题解

查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数