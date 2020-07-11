#### 一、MySql数据库地址：

> ip:106.52.178.155 
>
> 用户名:root 密码:rootroot
>
> use MyDatabase

#### 二、MySQL经典练习题及答案

> 表名和字段
> –1.学生表 
> Student(s_id,s_name,s_birth,s_sex) –学生编号,学生姓名, 出生年月,学生性别 
> –2.课程表 
> Course(c_id,c_name,t_id) – –课程编号, 课程名称, 教师编号 
> –3.教师表 
> Teacher(t_id,t_name) –教师编号,教师姓名 
> –4.成绩表 
> Score(s_id,c_id,s_score) –学生编号,课程编号,分数

-- 1.查询"01"课程比"02"课程成绩高的学生的信息及课程分数
分析：需要查询字段是该学生的所有信息及课程分数，条件是该学生01的课程分数要高于02的课程分数。
 (即Student、Score 2张表内查询)
第一步：先查出01课程的成绩表 

```mysql
SELECT * from Score where c_id='01'; 
```

第二步：先查出02课程的成绩表

```mysql
SELECT * from Score where c_id='02' ;
```


第三步：用where 条件过滤 1.满足同一个人  2.01分数>02分数 

```mysql
SELECT * from 
 (SELECT * from Score where c_id='01')  as s1, 
(SELECT * from Score where c_id='02') as s2 
WHERE s1.s_id=s2.s_id AND s1.s_score>s2.s_score;
```

第四步：生成新的一张表,并给给表字段创建别名

```mysql
SELECT s1.s_id,s1.s_score sc01,s2.s_score sc02  from 
 (SELECT * from Score where c_id='01')  as s1, 
(SELECT * from Score where c_id='02') as s2 
WHERE s1.s_id=s2.s_id AND s1.s_score>s2.s_score;
```

第5步：关联学生表获取学生信息即可

```mysql
select st.s_id,st.s_name,st.s_birth,st.s_sex,sc.sc01,sc.sc02 from Student st join (
SELECT s1.s_id,s1.c_id,s1.s_score sc01,s2.s_score sc02  from 
 (SELECT * from Score where c_id='01')  as s1, 
(SELECT * from Score where c_id='02') as s2 
WHERE s1.s_id=s2.s_id AND s1.s_score>s2.s_score
)sc on st.s_id=sc.s_id;
```

-- 2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数

```mysql
SELECT * from Student  st join (
select  s1.s_id,s1.s_score s01,s2.s_score s02  from 
(select * from Score WHERE c_id='01') as s1,
(select * from Score where c_id='02') as s2
where s1.s_id=s2.s_id and s1.s_score<s2.s_score 
) sc on sc.s_id=st.s_id;
```

-- 3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```mysql
SELECT st.s_id,st.s_name,sc.sc_v from Student st JOIN (
SELECT  s_id,avg(s_score) as sc_v from Score GROUP BY s_id 
) sc  on sc.s_id=st.s_id  and sc_v>60;
```

-- 4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩

```mysql
  SELECT st.s_id,st.s_name,sc.sc_v from Student st JOIN (
SELECT  s_id,avg(s_score) as sc_v from Score GROUP BY s_id 
) sc  on sc.s_id=st.s_id  and sc_v<60;    
```

