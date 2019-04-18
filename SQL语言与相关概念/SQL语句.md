# SQL语言

目标：通过SQL语句进行数据库中的数据管理

数据的读取：

1. 单表查询
2. 多表查询
3. 子查询

数据的存储

- 增加：增加一条数据；增加一张表
- 删除：删除数据；删除表
- 修改：修改数据；修改表

通过SQL语句管理用户

- 创建用户
- 修改用户
- 删除用户

通过SQL语句管理事务



知识点：

- 完成单表的增删改查
- 完成多表的查询
- 完成子查询
- 完成用户的管理
- 完成表的管理
- 序列、视图、索引
- 数据库的分页查询
- 数据库的备份



## SQL语句单表操作

### 1. 基础操作

- 基础查询

``` sql
  --查询表的所有数据  select * from 表名;*代表所有
    select * from emp;
  --查询表中指定字段的值 select 字段名1，字段名2，...from表名
    select empno from emp;
    select empno,ename from emp;
```

- 字段别名

``` sql
  --给查询结果中的字段使用别名
    --在字段名后使用关键字 字段名 as "别名"
    --作用：方便查看查询结果
    --注意：as关键字可以省略不写，别名中没有特殊字符双引号也可以省略不写。           
    select empno 员工编号,ename"员工姓名",job as 工作，mgr as "领导编号" from emp;
```

- 连接符

``` sql
  --连接符：select  字段名||'字符'||字段名||..... from 表名
     --||为sql语句的字符链接符，使用在select和from之间
     --字符链接格式为 字段名||'字符'||字段名
     --注意：一个拼接好的连接在结果集中是作为一个新的字段显示，可以使用别名优化字段显示。
    select   empno||'的姓名是'||ename as"信息"，job||'哈哈'||mgr from emp;
```

``` mysql
  ##mysql是使用concat来进行连接
```



- 去除重复

``` sql
  --去除重复 select distinct 字段名,字段名,...fromn 表名
     ---注意：去除重复的规则是按照行进行去除的，多行数据完全相同取其一
      select distinct  job ,mgr from emp;
```

- 排序

``` sql
  --排序  
      --单字段排序
             --select  * from 表名 order by 字段名 asc 升序排序 asc可以省略不写
             --select  * from 表名 order by 字段名 desc 降序序排序 
      --多字段排序
             --select * from emp order by 字段名1,字段名2...
             --先按照字段1排序，如果字段1的值相同，则按照字段2排序，....
      select * from emp order by empno desc--单字段排序 降序 
      select empno,ename,job from emp order by ename asc--单字段排序 升序
      select * from emp order by empno,ename--多字段排序
```

- 字段的逻辑运算

``` sql
   --字段的逻辑运算
       --select关键字和from关键字之间的字段可以直接进行四则运算
       --字段与字段之间也可以直接进行运算
       --注意：字段值为数值类型
    select * from emp
	select empno,ename,job,sal*2+1000，sal+comm  from emp
```

### 2. where子句

使用where子句查询筛选

 select 字段名，字段名，...from表名 where 筛选条件



- 单条件筛选

``` sql
 --select 字段名，字段名，...from表名 where 筛选条件
 --单筛选条件
        --使用运算符进行筛选 =,>,>=,<,<=,<>  单个条件中
        --注意：如果条件中的值为字符，必须使用单引号括起来
         --查询所有的员工的工资信息
         select empno,ename,sal+comm as 薪资 from emp
         --查询SMITH的个人信息
         select * from emp where ename='SMITH'
         --查询SMITH的薪资信息,逻辑运算符=
         select empno,ename,sal,sal+comm from emp where ename='SMITH'
         --查询工资大于1000的员工信息,逻辑符>
         select * from emp where sal>'2000'
         --查询工资不等于3000的员工信息
         select * from emp where sal<>3000 order by sal
```

``` sql
   --练习：
         --查看工资等于1250的员工信息
         select *from emp where sal='1250'
         --查看工作等于CLERK的员工信息
          select * from emp where job='CLERK'
         --查看工资大于1250的员工姓名和工作
         select ename,job from emp where sal>1250
         --查看工资大于等于2000的员工信息
         select * from emp where sal>=2000;
         --查看工资小于等于2000的员工信息；
         select * from emp where sal<=2000;
         --查看工资不等于1500的员工信息
         select * from emp where sal<>1500;
         --查看入职日期在81年后的员工信息
               --注意：oracle默认的日期格式为 日-月-年，示例'03-1月-1981'
         select * from emp order by hiredate
         select * from emp where hiredate>='01-1月-1981' order by hiredate
```

- 多条件筛选

``` sql
   --多条件筛选（where子句关键字：and,or,like,is null,is not null, in ,between and）
         --查询工资在2000-3000之间的员工信息
           --使用and关键字，多条件同时成立的筛选使用and关键字进行条件连接
           select * from emp where sal>=2000 and sal<3000
           --使用between  and 关键字进行条件连接，包含两头的数据
           select * from emp where sal between 2000 and 3000
         --查询工作为SALESMAN,ANALYST,MANAGER的员工信息
           --使用or关键字，进行或条件的筛选。
          select * from emp where job='SALESMAN' or job='ANALYST' or job='MANAGER' order by job
           --使用in关键字，也可以进行或筛选，但是in中的内容只能为一个字段的值。
          select * from emp where job in('SALESMAN','ANALYST','MANAGER')
          
          --查询工作为SALESMAN，MANAGER并且工资大于2500的员工信息
  			--and关键字的执行级别高于or
  			--可以使用小括号提升条件的执行级别，使用了小括号的级别是最高的
		  select * from emp where (job='SALESMAN' or job='MANAGER') and sal>2500

```

- 模糊查询

``` sql
--查询姓名中包含s的，以s开头的，以s结尾的，第二个字符为A的。(模糊查询)
	--%号表任意多个的任意字符
		--select * from 表名 where 字段名 like '%字符%' 查询包含指定字符的数据
		select * from emp where ename like '%S%'  --包含s的
		--select * from 表名 where 字段名 like '字符%' 查询以指定字符开头的数据
		select * from emp where ename like 'S%'--以S开头
		--select * from 表名 where 字段名 like '%字符' 查询以指定字符结尾的数据
		select * from emp where ename like '%S'--以S结尾的
	--_表示一个任意字符	
		--select * from 表名 where 字段名 like '_字符%' 查询指定位置为指定字符的数据
		select * from emp where ename like '_A%'--第二个字符为A的
		--select * from 表名 where 字段名 like '%字符2字符1%' escape'字符2' 
	--escape将指定的字符变为转义字符
		--转义字符可以将特殊字符转为普通字符，用户涵盖特殊转义字符的查询
		select * from emp where ename like '%/_%' escape '/'
```

### 3. 函数学习

- 单行函数

``` sql
--单行函数学习(字符函数，数值函数，日期函数)
   --特点1：不改变真实数据，只是对数据做了进一步修饰或者处理显示。
   --特点2：可以和字段混合使用
   --使用:select 字段名， 函数名(字段名)，字段名....from 表名
      --字符函数：
      --查询所有的员工信息，员工姓名小写显示。
      	select empno,ename,lower(ename),job,mgr,sal from emp
      --查询所有的员工信息，员工姓名首字母大写。
      	select empno,INITCAP(ename) 首字母大写的姓名,lower(ename),job from emp
      --数值函数：对数值类型的数据进行运算
        --伪表：真实存在的表，是为了方便进行数据的验证而临时存在的表。表名为：dual
        select abs(-1),ceil(2.2),floor(3.3),power(2,3),mod(5,2),round(4.55),trunc(10/3,2) from dual
      --日期函数：
          select months_between('01-1月-2018','24-6月-2017') from dual --返回两个日期间的月份数  
          select add_months('01-4月-2018'，-4) from dual --返回指定月数后的日期
          select next_day('16-4月-2018','星期二') from dual--查询最近的星期的日期
          select last_day('16-4月-2018') from dual--返回当月的最后一天的日期
          select round(to_date('19-4月-2018'),'DAY') from dual--按照星期进行四舍五入
```

- 多行函数

``` sql
   --多行函数(max,min,avg,sum,count)很重要
      --作用：对查询的数据进行统计
      --使用：select 多行函数名(字段名),多行函数名(字段名)..from 表名
          --注意：多行函数不能和普通字段以及单行函数混用，除非分组
      --max(字段名) 返回该字段的最大值
      --min(字段名) 返回该字段的最小值
      --sum(字段名) 返回该字段的和
      --avg(字段名) 返回该字段的平均值
      --count
              --count(*) 返回表的记录数
              --count(字段名) 返回非空值的数量
              --count(distinct 字段名) 去除重复后的字段值的数量
      --查看员工的最高工资
      select max(sal) from emp--多行函数不能和字段直接混用，除非分组。
      select lower(ename)，max(sal) from emp--多行函数不能和单行函数混用，除非分组
      --查看员工的最低工资
      select min(sal) from emp
      --查看员工的平均工资
      select avg(sal) from emp
      --查看所有的员工工资之和
      select sum(sal) from emp
      --查询公司有多少员工
      select count(*) from emp--查询表的记录数
      --查询有津贴的员工人数
      select count(comm) from emp--查询字段的值的数量，null会自动过滤
      --查询公司有多少工作种类     
      select distinct job from emp
      select count(distinct job) from emp
      select count(*),sum(sal),avg(sal),max(sal),min(sal) from emp 
      select * from emp
```

- 转换函数

``` sql
--转换函数：
    --to_number(数值类型的字符):将字符转换为数值
    --to_char(数值或者是日期):将数值或者日期转换为字符
    --to_date(日期格式的字符)：将字符转换为日期
----------------数值和字符的互转-----------------------
--字符转换为数字char---->number
    select to_number('123')+2  from dual
--数字转换字符number--->char
    --指定显示格式:
           --9表示位置占位，例如999,999,999会将数字按照三个一组使用逗号隔开。
           --L表示人民币符号，$表示美元符号
           --0可以进行占位分组，但是如果真实数据位数不足，会使用0进行补位。
            select to_char(12345,'$999,999,999') from dual
            select to_char(12345,'L999,999,999') from dual
            select to_char(12345678,'000,000,000,000.000') from dual
    --查询工资大于2000的员工信息
            --数值和字符之间的转换可以隐式转换。to_number可以省略不写.
            select * from emp where sal>'2000';
            select * from emp where sal>to_number('2000');
---------------日期和字符的互转---------------------------
--一般使用方式：新增数据使用to_date(),查询数据使用to_char()
--字符转换为日期 char--->date
    --使用to_date('要转换的字符',日期格式)函数将字符转换为日期
         --注意1：字符必须符合日期格式
         --注意2:oralce默认的转换格式为日月年，例如'01-1月-2018' oracle认为是一个日期
         --常用日期格式：
               --    yyyy-mm-dd
               --    yyyy/mm/dd
    --查询员工入职日期在82年后的信息
    select * from emp where hiredate >to_date('1982-01-01','yyyy-mm-dd')
    select * from emp where hiredate >to_date('1982/01/01','yyyy/mm/dd')      
    select * from emp where to_char(hiredate,'yyyy-mm-dd') >'1982-01-01'
--日期转换为字符  date--->char
   --使用to_char('要转换的日期',转换格式)
   --注意1：如果不指名转换格式，则使用默认格式，日月年例如:'01-1月-81'
   --常用转换格式：
         -- yyyy-mm-dd
         -- yyyy/mm/dd
         --'yyyy"年"mm"月"dd"日"'
   select to_char(hiredate) from emp--使用默认格式将日期转换为字符
   select to_char(hiredate,'yyyy-mm-dd') from emp--使用指定格式  yyyy-mm-dd
   select to_char(hiredate,'yyyy/mm/dd') from emp--使用指定格式 yyyy/mmm/dd
   select to_char(hiredate,'yyyy"年"mm"月"dd"日"') from emp--使用指定格式 'yyyy"年"mm"月"dd"日"'
```

- 其他函数

``` sql
--其他函数：
   --nvl()：nvl(字段名，新的值)
          --如果字段值不为null，则返回该字段的值。如果为null则返回新的值
   --nvl2()：nvl2(字段名，处理1，处理2)
          --如果字段值不为null，则执行处理1，为null执行处理2
   --decode()：decode(字段名,值1，处理1，值2，处理2，值3，处理3,...,公共处理)
          --如果字段的值和decode中的条件值相同则执行对象的处理。如果都没有则执行公共处理
   ---查询员工的工资信息
   select ename,job,sal from emp
   --查询员工的薪水信息
   select ename,job,sal+nvl(comm,0),sal+comm,sal from emp
   select ename,job,nvl2(comm,sal+comm,sal) from emp
   --显示员工的职称
   select ename,job,decode(job,'MANAGER','经理','PRESIDENT','董事长','SALESMAN','销售','普通员工') from emp
```

### 4. 分组查询&筛选查询

关键知识点：where语句与having的区别——执行顺序

``` sql
----分组查询&筛选学习：
     --关键字：group by 分组字段名,分组字段名....
         --注意1：使用了分组后，在select语句中只允许出现分组字段和多行函数。
         --注意2：如果是多字段分组，则先按照第一字段分组，然后每个小组继续按照第二个字段继续分组，以此类推。
         --注意3：在where子句中不允许出现多行函数。
     --分组筛选
         --关键字：having
              --作用：针对分组进行分组后的数据筛选，允许使用多行函数。
              --注意：having关键必须和分组结合使用。不允许单独使用。   
              --where和having的比较：
                 --where子句不允许出现多行函数，having允许出现多行函数
                 --where子句和having都可以使用普通字段直接进行筛选，但是where的效率高于having
                    --where执行顺序: from--->where--->group by-->select-->order by
                    --having执行顺序:from--->group by-->select--->having--->order by
               --结论：在分组语句中，使用where进行字段级别的筛选，使用having进行多行函数的筛选。    
      --查询最高工资和员工数
      select max(sal),count(*) from emp
      --查询不同部门的最高工资
      select deptno,max(sal) from emp group by deptno
      select * from emp
      --查询不同工作岗位的员工数
      select job, count(*) from emp group by job
      --查询不同部门的不同工作岗位的人数
      select deptno ,lower(job),count(*) from emp group by deptno,job order by deptno
      --查询不同部门的不同工作岗位的并且人数大于1的信息
      select deptno ,lower(job),count(*) from emp  group by deptno,job having count(*)>1 order by deptno
      --查询部门号大于10的不同部门的不同工作岗位的人数
          --使用having关键字
       select deptno ,lower(job),count(*) from emp group by deptno,job  having deptno>10  order by deptno
          --使用where关键字
       select deptno,job,count(*) from emp where deptno>10 group by deptno,job  order by deptno
```

- 执行结构

``` sql
---SQL查询语句的结构
        --select 子句                  要查询的数据(oracle函数，别名，连接符，去除重复，逻辑运算)
        --from语句                     决定要查询的表(表名)             
        --where子句                    筛选数据（筛选条件，关键字）
        --group by子句                 分组     （分组字段）
        --having子句                   分组筛选   (多行函数筛选条件)
        --order by子句                 排序       （排序）
        --from-->where--->group by-->select--->having--->order by
```

### 5. 增删改&数据备份

``` sql
  --数据库的增删改&数据备份
    --注意：增加删除修改的数据SQL语句执行完毕后，不会立马进行数据的写入。
           --还需要手动对数据进行提交，如果数据有问题还可以回滚
    --主键：非空唯一的字段可以设置为主键。
          --在一张表中，某个字段的值是非空唯一的，将此字段设置为主键。
          --主键的作用：唯一的标识一条数据。
  --增加数据
      --insert into 表名(字段名，字段名，...)values(值1，值2，值3....);
          --注意1：主键必须给值，允许为空的字段可以不给值。
          --注意2：插入语句表名后跟的字段名为要赋值的字段，值和字段数量和顺序必须是一一对应的。
          --注意3：如果是全字段插入，可以省略字段名部分 insert into 表名 values(值1，值2，.....)
      --在部门中新增一个新的部门信息，信息内容为 编号：50，名称：LOL学院,地址：北京
         insert into dept(deptno,dname,loc)values(50,'LOL学院','北京');
         insert into dept(deptno,dname,loc)values(60,'LOL学院','北京');
         insert into dept values(60,'LOL学院','北京');
         select * from dept
      --在部门中新增一条数据，只有部门编号和名称，没有地址。
         insert into dept(deptno,dname)values('吃鸡学院',70)
  --删除数据
       --delete from 表名 删除表中的所有记录
           --truncate table 表名  删除表中的所有记录，但是效率高于delete
       --delete from 表名 where 条件 删除指定的数据,只要符合条件就会删除
       delete from dept where deptno=50 --删除指定的数据
       delete from dept --清空表数据
       truncate table dept--清空表中数据
  --更新数据
       --update 表名 set 字段名=新的值,字段名=新的值...(会将字段的值全部改为新的值)
       --update 表名 set 字段名=新的值,字段名=新的值... where 条件(将符合条件的数据的字段改为新的值)
       update dept set dname='java学院',loc='上海'
       update dept set dname='java学院',loc='上海' where deptno=50
       select * from dept
  --数据的备份
       --注意：只会备份表结构和表的数据，约束不会备份。
       --表级别备份
           --全部备份:create table 新的表名 as select * from 备份表名
           --部分备份:create table 新的表名 as select 字段名，字段名，...from  备份表名
       --数据整体插入
           --insert into 插入表名 select * from 表名
           --注意:查询语句结果的字段数据必须和插入表名的字段数量一致，类型要一致。
       create table deptBak as select * from dept--全部备份
       create table deptBak2 as select deptno,dname from dept-- 部分备份
       select * from deptBak2
       insert into deptBak2 select deptno,dname from dept
```

## SQL多表联合操作

### 1. 多表联合查询SQL92

知识点：笛卡儿积；等值连接；不等值连接；自连接；外连接。

``` sql
--SQL92方式
    --笛卡尔积:将多个表的数据进行一一对应，所得到结果为多表的笛卡尔积。
          --结果的数量为所有表的数量的乘积，对两张表做全排列，两个表的数据全部一一对应方式。
    select * from emp,dept
    --等值连接筛选
         --概念：先做表的笛卡尔积，然后筛选，筛选条件为等值筛选。
         --注意：条件为字段的值相同来进行筛选，字段的名字可以不同
         --查询员工姓名，工作，薪资，部门名称
         select * from emp,dept where emp.deptno=dept.deptno
                --可以直接在select子句中使用字段直接获取数据，但是效率比较低。建议字段前加上表名
                --注意：如果是公共字段，则必须声明表名
     select ename,job,sal,dname from emp,dept where emp.deptno=dept.deptno--等值连接筛选
     select emp.ename,emp.job,emp.sal,dept.dname,emp.deptno from emp,dept where emp.deptno=dept.deptno
     select e.ename,e.job,e.sal,d.dname from emp e,dept d where e.deptno=d.deptno and sal>2000--给表使用别名
    --不等值连接
         --查询员工姓名，工作，工资，工资等级
         select * from emp e,salgrade s where e.sal>=s.losal and e.sal<=s.hisal
    --自连接:
         --查询员工姓名，工作，薪资，及上级领导姓名
         select e1.ename,e1.job,e1.sal,e2.ename from emp e1,emp e2 where e1.mgr=e2.empno
    --外连接
        --左外连接:加在右边，显示左边对应字段没有值的数据
                --查询员工姓名，工作，薪资，部门名称及没有部门的员工信息
                select * from emp e,dept d where e.deptno=d.deptno(+)
        --右外连接:加在左边，显示右边边对应字段没有值的数据
                --查询员工姓名，工作，薪资，部门名称及没有员工信息的部门
                select * from emp e,dept d where e.deptno(+)=d.deptno
```

### 2. 多表联合查询SQL99

注意连接的筛选条件为on

``` sql
--SQL99多表查询
    --注意1：依然可以给表添加别名
    --注意2：如果使用on或者usering关键对结果进行筛选，必须使用inner join作用表与表的连接，其中inner可以省略
    --注意3: 外连接的 outer关键字可以省略不写
    --注意4：依然可以继续使用分组，having ，排序等
    --笛卡尔积:使用cross join 关键字
            ---select 内容 from 表名 cross join 
             select * from emp cross join dept
    --筛选
         --查询员工姓名，工作，薪资，部门名称
         --自然连接：使用关键字 natural join
            --使用：select 内容 from 表名 natural join 表名
            --特点1：底层先笛卡尔积，然后按照所有的同名同值字段自动进行等值筛选。
            --问题1：如果只想按照部分字段结果筛选怎么办？
            --问题2：如果想按照字段名不同，但是值相同进行等值筛选怎么办？两个表的字段不同就不能筛选成功
            select * from emp natural join dept
             --解决1：使用using关键字
                  --作用1：指明使用指定的字段对联合查询的结果进行等值筛选
                  --注意：指明的字段必须是两表的同名同值字段
                  --使用: select 内容 from 表名 inner join 表名 using(字段名，字段名,....)
                  select * from emp inner join dept using(deptno)
            --解决2：使用on关键字进行自定义连接条件筛选(等值筛选，不等值筛选)
                  --注意：普通筛选条件使用where进行筛选，不要使用on进行。好处：SQL语句的阅读性变强。
                  --使用：select 内容 from 表名 inner join 表名 on 连接条件 where 普通筛选条件
                  select * from emp inner join dept on emp.deptno=dept.deptno where sal>2000
        --外连接：
             --左外连接:select 内容 from 表名 left outer join 表名 on 连接条件 
                  --查询员工姓名，工作，薪资，部门名称及没有部门的员工信息
                  select * from emp e left outer  join dept d on e.deptno=d.deptno
             --右外连接:select 内容 from 表名 right outer join 表名 on 连接条件 
                  --查询员工姓名，工作，薪资，部门名称及没有员工的部门信息
                  select * from emp e right outer  join dept d on e.deptno=d.deptno
             --全外连接：select 内容 from 表名 full outer join 表名 on 连接条件 
                  select * from emp e full  outer join dept d on e.deptno=d.deptno
        --自连接:
             --查询员工及其上级领导姓名
                  select  e1.*,e2.ename from emp e1 inner join emp e2 on e1.mgr=e2.empno
```

### 3 .SQL92&SQL99实现三表联合查询

``` sql
--SQL92&SQL99实现三表联合查询
  --创建city表：使用图形操作即可
  --给city表添加测试数据
    insert into city values(1,'商丘','历史闻名古都');
    insert into city values(2,'邯郸','历史闻名古都');
    insert into city values(3,'洛阳','历史闻名古都');
    insert into city values(4,'开封','历史闻名古都');
  --将部门表中的loc字段设置为城市表的城市编号
    update dept set loc='1' where deptno=50;
    update dept set loc='2' where deptno=40;
    update dept set loc='3' where deptno=30;
    update dept set loc='4' where deptno=20;
    update dept set loc='4' where deptno=10;
  --完成三表联合查询
    --SQL92实现：查询员工信息及部门名称及所在城市名称并且员工的工资大于2000或者有奖金
        --特点：易于书写，难于阅读
        --缺点：92的SQL语句结构不清晰
        --用法：
             --select  内容 (别名，连接符，去除重复，oracle函数，逻辑运算)
             --from  表名1，表名2，表名3...
             --where  条件(连接条件，普通筛选条件，where子句关键字)
             --group by 分组字段
             --having 多行函数筛选
             --order by 排序字段
        select e.*,d.dname,c.cname 
        from emp e,dept d,city c
        where (e.deptno=d.deptno and d.loc=c.cid and sal>2000) or (e.deptno=d.deptno and d.loc=c.cid and comm is not null) 
        order by e.sal 
   --SQL99实现:查询员工信息及部门名称及所在城市名称并且员工的工资大于2000或者有奖金
        --特点：难于书写，易于阅读
        --使用：
              --select 内容 from 表名1
              --inner join 表名2
              --on 连接条件
              --inner join 表名3
              --on 连接条件
              --where  普通筛选条件
              --group by 分组
              --having 多行函数筛选
              --order by 排序
      select * from emp e 
      inner join dept d 
      on e.deptno = d.deptno 
      inner join city c 
      on d.loc =c.cid
      where e.sal>2000 or e.comm is not null
      order by e.sal
```

## 子查询

``` sql
--子查询学习：
  --使用时机：当查询的筛选条件不明确时，考虑使用子查询。
  --单行子查询
  --多行子查询
```

``` sql
--单行子查询:
  --使用时机：筛选条件不明确需要执行一次查询，并且查询结果一个字段并值只有一个
  --注意：where子句中允许出现查询语句，该查询语句称为子查询
  --使用：select 内容 from 表名 where 字段名 比较运算符 子查询语句
    --查询所有比雇员“CLARK”工资高的员工信息
      select * from emp where sal>(select sal from emp where ename ='CLARK')
    --查询工资高于平均工资的员工的名字和工资
    select ename,sal from emp where sal>(select avg(sal) from emp )
    --查询和soctt属于同一部门且工资比他低的员工资料
    select * from emp where deptno=(select deptno from emp where ename='SCOTT') and sal<(select sal from emp where ename='SCOTT')
    --查询工资最高的员工资料
    select * from emp where sal=(select max(sal) from emp)
    --查询职务和scott相同，雇佣时间早的员工信息
    select * from emp where job=(select job from emp where ename='SCOTT') and hiredate <(select hiredate from emp where ename='SCOTT')
    --查询工资比scott高或者雇佣时间早的员工编号和名字
     select empno,ename from emp where job=(select job from emp where ename='SCOTT') or hiredate <(select hiredate from emp where ename='SCOTT')
----------------------------------------------------------------------------
----多行子查询：
     --使用:子查询的结果只有一个字段但是字段有n个值，考虑使用多行子查询，其实就是使用关键字
       --关键字1:any 任意
            --select 内容 from 表名 where 字段名 比较运算符 any 子查询语句
       --关键字2：all 所有
            --select 内容 from 表名 where 字段名 比较运算符 all 子查询语句
       --关键字3：in 表示任意存在,相当于 = any  
            --select 内容 from 表名 where 字段名 in 子查询语句   
            --select 内容 from 表名 where 字段名 not in 子查询语句   
    --查询工资高于任意一个CLERK的所有员工信息
    select * from emp where sal> any (select sal from emp where job='CLERK')
    --查询工资高于所有SALESMAN的员工信息
    select * from emp where sal> all (select sal from emp where job='SALESMAN')
    --查询部门20中同部门10的雇员工作一样的雇员信息
    select job from emp where deptno=10
    select * from emp where (job='MANAGER' or job='PRESIDENT' or job='CLERK') and deptno=20
    select * from emp where job in (select job from emp where deptno=10) and deptno=20
    select * from emp where job = any (select job from emp where deptno=10) and deptno=20
```

## 分页查询

``` sql
--oracle的分页查询1：ROWNUM查询分页通式
/*

* firstIndex:起始索引

* pageSize:每页显示的数量

* sql:可以是简单的单表查询语句，也可以是复杂的多表联合查询语句

*/
select * from(select ROWNUM rn，a.* from(sql) a where ROWNUM<=(firstIndex+pageSize)) where rn>firstIndex
```

``` sql
--oracle的分页查询2：row_number()解析函数分页查询通式
 /*

 * firstIndex:起始索引

 * pageSize:每页显示的数量

 * orderColumn:排序的字段名

 * sql:可以是简单的单表查询语句，也可以是复杂的多表联合查询语句

 */
select * from(select * from(select t.*,row_number() over(order by orderColumn) as rownumber from(sql) t) p where p.rownumber>firstIndex) where rownum<=pageSize
```

``` mysql
--mysql的分页查询
/*

* sql:可以是单表的查询语句，也可以是多表的联合查询语句

* firstIndex:起始的索引

* pageSize:每页显示的记录数

*/

select o.* from (sql) o limit firstIndex,pageSize
```



## 二维表操作

``` sql
---Oralce的二维表操作
    --创建表并同时添加约束
      --主键约束
      --非空约束
      --检查约束
      --唯一约束
      --外键约束
    --简单的表创建和字段类型
    --简单的创建语句：
```

### 1. 创建表

``` sql
 --创建表:
    --使用：create table 表名(字段名 类型，字段名 类型，....);
    --数据类型：
        --number类型
              --数值类型
                   --整数类型 number(a)   总长度为a
                   --浮点数类型 number(a,b) 总长度为a，小数位长度为b，小数位可以不写。
        --varchar2类型
                --字符类型 varchar2(ln) ln表示字符的最大长度，实际存储内存长度是根据字符大小来分配，但是最大不能超过ln 
                --特点：动态分配存储空间，节省空间
        --char类型
                --字符类型 char(ln) 不管字符数据长度是多大，直接开辟ln大小的空间存储数据
                --特点：存储效率高于varchar2
        --date类型
    create table student(
      sno number(10),
      sname varchar2(100),
      sage number(3),
      ssex char(100),
      sfav varchar2(500),
      sbirth date   
    )
 --添加测试数据
    insert into student values(1,'柳岩',18,'女','拍电影'，'01-1月-1985');
    insert into student values(2,'古力娜扎',20,'女','拍电影'，to_date('1990-01-01','yyyy-mm-dd'));
select * from student
```

### 2. 二维表的约束学习

见附件

### 3. 二维表的修改

``` sql
--oracle
--二维表的维护
    --添加新的字段:
         --alter table 表名 add 字段名 类型
         alter table student add sphone number(11)--在学生表中添加新的字段
    --修改原有字段
         --修改字段类型
             --alter table 表名 modify 字段名 新的类型
             alter table  student modify sphone varchar2(11)
         --修改字段名
             --alter table 表名 rename column 字段名 to 新的字段名
             alter table student rename column sphone to phone 
         --删除字段
             --alter table 表名 drop column 字段名
             alter table student drop column phone
    --修改表名
         --rename 原有表名 to 新的表名
         rename student to student2
         rename student2 to student
    --删除表
        --drop table 表名
        drop table student 
```

``` mysql
	--mysql的不同在于修改表名的部分
		alter table 表名 rename as 新的表名
```









## 附

### 1. 用户管理

``` sql
--oracle的账户管理学习：
    --权限：具备某类事物的操作的能力，此能力称为权限。
    --角色：一系列权限的集合
    --oracle自带账户：
      -- system 管理账户  bjsxt
         --特点：具备大部分oracle的操作权限，主要用来管理普通账户及oralce的数据
         --使用人：oracle数据维护工作人员
      -- sys 超级管理员账户 bjsxt
         --特点：具备system的所有权限，同时又具备其他的权限
         --使用人：oracle攻城狮
    --创建账户
         --使用system账户，并使用dba身份，登录oracle管理系统
         --创建用户
             create user bjsxt identified by bjsxt;         
    --维护账户
            --赋予权限  grant 权限或者角色名 to 用户名
            
             grant connect to bjsxt;--给用户赋予登录权限
             grant resource to bjsxt;--给用户资源操作权限
             grant dba to bjsxt;--给用户赋予dba权限
             select * from scott.emp--查看其它用户的表 使用用户名.表名  
             
            --删除权限 revoke 权限或者角色名 from 用户名
             revoke dba from bjsxt;
    --删除账户 drop user 用户名
            drop user bjsxt;
```

### 2. 二维表的约束学习

- oracle的约束学习

``` sql
      create table student(
           sno number(10) ,--primary key
           sname varchar2(100) ,--not null
           sage number(3), --check(sage<150 and sage>0)
           ssex char(4) ,--check(ssex='男' or ssex='女')
           sfav varchar2(500),
           sbirth date,
           sqq varchar2(30) --unique
           --constraints pk_student_sno primary key(sno)--添加主键约束
           --constraints ck_student_sname check(sname is not null)--非空约束
           --constraints ck_student_sage check(sage<150 and sage>0)--检查约束
           --constraints ck_student_ssex check(ssex='男' or ssex='女')--检查约束
           --constraints un_student_sqq unique(sqq)--唯一约束
      )   
      --添加主键约束
       alter table student add  constraints pk_student_sno primary key(sno); 
       alter table student drop  constraints pk_student_sno;
      --添加非空约束
       alter table student add  constraints ck_student_sname check(sname is not null);
       alter table student drop  constraints ck_student_sname; 
      --添加检查约束
       alter table student add constraints ck_student_sage check(sage<150 and sage>0)
       alter table student drop  constraints ck_student_sage; 
      --添加检查约束校验性别
       alter table student add constraints ck_student_ssex check(ssex='男' or ssex='女')
       alter table student drop  constraints ck_student_ssex; 
      --添加唯一约束
       alter table student add constraints un_student_sqq unique(sqq)
       select * from student
       drop table student
------------------------------------------------------------------------------------------------------------------
--二维表创建约束学习：(主键约束，非空约束，检查约束，唯一约束)
    --问题1：学号重复依然可以添加
  insert into student values(1,'关晓彤',18,'女','拍电影',to_date('2000-01-01','yyyy-mm-dd'),'267889900');
  insert into student values(1,'关晓彤001',18,'女','拍电影',to_date('2000-01-01','yyyy-mm-dd'),'267889900');
    --问题2：竟然可以没有名字
  insert into student values(2,'',18,'女','拍电影',to_date('2000-01-01','yyyy-mm-dd'),'267889900');
    --问题3：年龄竟然可以超过200岁
  insert into student values(3,'关晓彤002',300,'女','拍电影',to_date('2000-01-01','yyyy-mm-dd'),'267889900');
    --问题4：性别竟然可以为任意字符
  insert into student values(4,'关晓彤',18,'a','拍电影',to_date('2000-01-01','yyyy-mm-dd'),'267889900');
    --问题5：qq号竟然可以重复
  insert into student values(5,'关晓彤003',18,'女','拍电影',to_date('2000-01-01','yyyy-mm-dd'),'267889900');
  insert into student values(6,'关晓彤004',18,'女','拍电影',to_date('2000-01-01','yyyy-mm-dd'),'267889900');
---解决：
    --问题1：添加主键，主键特点：非空唯一
       --使用：
            --直接在创建表的字段后使用 primary key
            --在创建表的语句的最后面使用 constraints pk_表名_字段名 primary key(字段名)
            --在创建表后使用 alter table 表名 add  constraints pk_表名_字段名 primary key(字段名);
            --删除主键     alter table student drop  constraints 主键的约束名;
    --问题2：使用非空约束
       --使用：
            --直接在创建表的字段后使用 not null 关键字 
            --在创建表的语句的最后面使用 constraints ck_表名_字段名 check(字段名 is not null)
            --在创建表后使用 alter table 表名 add  constraints ck_表名_字段名 check(字段名 is not null);
            --删除非空约束 alter table student drop  constraints 非空约束名;
    --问题3：使用检查约束
            --直接在创建表的字段后使用 check(条件) 例如      sage number(3) check(sage<150 and sage>0)，
            --在创建表的语句的最后面使用 constraints ck_表名_字段名 check(条件)
            --在创建表后使用 alter table 表名 add  constraints ck_表名_字段名 check(条件);
            --删除检查约束 alter table student drop  constraints 检查约束名;
    --问题4：使用检查约束
            --直接在创建表的字段后使用 check(条件)
            --在创建表的语句的最后面使用 constraints ck_表名_字段名 check(条件)
            --在创建表之后使用alter table 表名 add  constraints ck_表名_字段名 check(条件);
            --删除检查约束 alter table 表名 drop  constraints 检查约束名;
    --问题5：使用唯一约束
            --直接在创建表的字段后使用 unique
            --在创建表的语句后面使用 constraints un_表名_字段名 unique(字段名);
            --在创建表后使用 alter table 表名 add  constraints un_表名_字段名 unique(字段名);
            --删除约束：alter table 表名 drop  constraints 唯一约束名;
--------------------------------------------------------------------------------------------------------
--二维表创建 外键约束学习：
   --创建学生表
   create table student(
         sno number(10) primary key,
         sname varchar2(100) not null,
         sage number(3) check(sage>0 and sage<150),
         ssex char(4) check(ssex='男' or ssex='女'),
         sfav varchar2(500),
         sqq varchar2(30) unique,
         cid number(10) --references clazz(cno)
         --constraints fk_student_cid foreign key(cid) references clazz(cno)--外键
      )
      --添加外键
      alter  table student add constraints fk_student_cid foreign key(cid) references clazz(cno) on delete set null
      alter  table student drop constraints fk_student_cid 
      drop table student
      --添加测试数据
      insert into student values(1,'张三001',18,'男','唱歌','657889900',1);
      insert into student values(2,'张三002',18,'男','唱歌','657889901',1);
      insert into student values(3,'李四001',18,'男','唱歌','657889903',2);
      insert into student values(4,'李四002',18,'男','唱歌','657889904',2);
   --创建班级表
      create table clazz(
       cno number(10) primary key,
       cname varchar2(100) not null,
       cdesc varchar2(300)
      
      )
      --添加测试数据
       insert into clazz values(1,'java高薪就业班','6666');
       insert into clazz values(2,'python高薪就业班','33333');
  --查询学生及其班级信息
      select * from student  s
      inner join clazz c
      on s.cno=c.cno
  --问题:竟然可以在学生表中插入一个不存在班级
      insert into student values(5,'李四003',18,'男','唱歌','657889905',3);
  --使用外键:
      --作用：当在子表中插入的数据在父表中不存在，则会自动报错。
      --概念：当一张表的某个字段的值需要依赖另外一张表的某个字段的值，则使用外键约束。
             --其中主动依赖的表称为子表，被依赖的表称为父表。外键加在子表中。
      --使用： 
             --在子表中的字段后直接使用   references 父表名(字段) 例如： cid number(10) references clazz(cno)
             --在创建表语句的最后面使用  constraints fk_子表名_字段名 foreign key(字段名) references 父表名(字段名)
             --在创建表后使用：alter table 表名 add constraints fk_子表名_字段名 foreign key(字段名) references 父表名(字段名)
            --删除外键：alter table 表名 drop constraints 外键约束名
      --外键选取：
            --一般选取父表的主键作为子表的外键。
      --外键的缺点：
            --无法直接删除父表数据，除非级联删除
            --级联删除：在添加外键约束时，使用关键字 on delete cascade
                     --使用：当删除父表数据时，自动删除子表相关所有数据。
                     --缺点：无法保留子表历史数据。
                     --使用关键字 on delete set null
                           --删除父表数据时，将子表中的依赖字段的值设置为null。
                           --注意：子表依赖字段不能添加非空约束。
           --删除班级1的信息
           select * from student
           delete from clazz where cno=1 
```

- mysql的约束学习

  <https://blog.csdn.net/w_linux/article/details/79655073>

  最主要的区别有以下几点：

  - 检查约束，check的使用，mysql需要使用到自身的存储过程

### 3. 序列&索引&视图

``` sql
--oracle的序列的学习
    --创建序列
      --使用 create sequence 序列名
      --特点1：默认开始是没有值的，也就是指针指在了没有值的位置。
      --特点2：序列名.nextval每次执行都会自增一次，默认步长为1
      --特点3：序列名.currval查看当前序列的值。开始是没有的。
      --作用：作为主键使用,动态的获取之间的值，这样新增数据的时候极大的避免了主键冲突
           --使用的是 序列名.nextval作为主键
      --注意：主键是非空唯一就可以，不需要主键的值是连续的值。
           --创建默认序列
             create sequence cc;--创建序列cc
             select cc.currval from dual--查看序列当前值
             select cc.nextval from dual--查看序列的自增后的值。
           --创建自定义序列
              create sequence aa--创建序列
              start with 5      --设置开始位置
              increment by 2    --设置步长
              select aa.currval from dual 
              select aa.nextval from dual
       --创建测试表
           create table teacher(
                tid number(10) primary key,
                tname varchar(100) not null
           )
           insert into teacher values(cc.nextval,'张三');
           insert into teacher values(cc.nextval,'张三');
            
           select * from teacher
    --删除序列
           --drop sequence 序列名
           drop sequence aa
 -------------------------------------------------------------------
 --索引学习：
     --作用：提升查询效率
     --使用索引：
         --创建
           create index 索引名 on 表名(字段名)
         --删除索引
           drop index 索引名
     --特点：
         --显示的创建，隐式的执行
     --注意：
         --oracle会自动给表的主键创建索引。
      
     create index index_teacher_tname on teacher(tname)--创建索引
     drop index index_teacher_tname--删除索引
     select * from teacher where tname='张三'
     select * from teacher where tid=8
--------------------------------------------------------------------
--视图学习:
      --使用视图：
          --创建视图
          create view 视图名 as select 对外提供的内容 from 真实表名
          --删除视图
          drop view 视图名
      --视图特点：
         --特点1：保护真实表，隐藏重要字段的数据。保护数据。
         --特点2：在视图中的操作会映射执行到真实表中
         --特点3：可以手动开启只读模式 使用关键字 with read only
      --注意：视图的创建必须拥有dba权限
      create view stu as select sno,sname,sage from  bjsxt.student
      create view stu2 as select sno,sname,sage from  student with read only 
      drop view stu
      select * from student
      select * from stu
      update stu2 set sname='wollo' where sno=1
      grant dba to bjsxt
```

