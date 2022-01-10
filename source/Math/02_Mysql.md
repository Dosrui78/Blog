#### 2.2 mysql不完全指北

##### 1. mysql安装

* 安装：去官网下载好后解压在根目录下

  然后cmd以管理员身份打开

  cd至数据库解压目录，注：不可进入bin目录下

  然后在命令提示符下输入命令 bin\mysqld --initialize-insecure（实现MYSQL的初始化）注：如果这一步报错无法启动就要安装VC++

  再运行命令 bin\mysqld --install 安装MySQL服务到系统服务中

  最后在计算机管理中的服务中找到MySQL并启动就行了。

- 卸载：\bin\mysqld -- remove，然后删掉解压目录。

-------

##### 2. mysql简单配置

此时执行mysql -u root -p还无法执行，因为还没有配置环境变量。

复制bin目录的路径E:\mysql-8.0.22-winx64\bin

MYSQL服务也可以用命令启动或停止（**须以管理身份进行**）

```mysql
net start mysql
net stop mysql
```

-----------

##### 3. 操作命令

```mysql
数据库操作：
mysql>show databases;                                    --查看当前用户下的所有数据
mysql>create database  (if not exists) xxx;              --创建数据库
mysql>use test;                                          --选择进入test数据库
mysql>show create database xxx\G                         --查看数据库创建语句
mysql>select database();                                 --查看当前所在的数据库位置
mysql>drop database (if exists) xxx;                     --删除一个数据库

数据表操作：
mysql>show tables;                                       --查看当前数据库下所有表格mysql>desc tb1;                                          --查看表tb1的表结构
mysql>show create table xxx\G                            --查看表的建表语句
mysql>create table demo(
	->name varchar(16) not null,
	->age int,
	->sex enum('w','m') not null default 'm');           --创建demo表格  
mysql>show columns from demo;                            --查看表结构
mysql>desc demo;                                         --查看表结构
mysql>drop table (if exists) xxx;                        --尝试删除表格

数据操作：
mysql>insert into demo(name,age,sex) values('zhangsan', 20, 'w'); 
													     --添加数据
mysql>select * from demo                                 --查询数据
mysql>update demo set age=24 where name='aaa';           --修改数据
mysql>delete from demo where name='bbb';        	     --删除

```

```mysql
表的字段约束：
unsigned无符号（正数）
zerofill前导零填充
auto_increment自增
default默认值
not null非空
primary key主键（非null并不重复）
unique唯一性（可以为null但并不重复）
index常规索引
```

```mysql
MySQL的建表语句格式：
create table 表名(
	字段名 类型 [字段约束],
	字段名 类型 [字段约束],
	字段名 类型 [字段约束],
	…
	)
```

```mysql
布尔型：
select 10 in （1,2,3,4)  								    -> False
select 10 > 100             							    -> False
```

```mysql
修改表结构格式：alter table 表名 action(更改选项)
修改字段名：alter table 表名 rename 字段名信息 as xxx
添加字段：alter table 表名 add 字段名信息
删除字段：alter table 表名 drop 被删除的字段名
修改字段：alter table 表名 change[modify] 被修改后的字段名
修改数据库引擎类型： alter table 表名 engine innodb[myisam]前者稳定安全，后者速度快
```

```mysql
--修改数据                         
update 表名 set age=21,sex='w' where id in (6,8);
update 表名 set age=29,sex='w' where id =10;        (就算id=100不在表中也不会报错)

```

```mysql
--删除数据
delete from 表名 where id in (100,200);
delete from 表名 where id > 100;
delete from stu where id between 100 and 200;
```

```mysql
--数据查询
select [字段列表]|* from 表名 
		[where 搜索条件]
		[group by 分组字段 [having子条件]]
		[order by 排序 asc|desc]
		[limit分页参数]

select id,name from stu;
select id,name,age,age+5 from stu;
select * ,"beijing" as city from stu;
```

```mysql
--添加字段
alter table stu add column hobby varchar(255) default null comment '爱好' after age;
```

```mysql
--添加搜索条件
select * from stu where id<5;
select * from stu where id in (1,3,5,7);
select  * from stu where age between 20 and 25;
select * from stu where age<20 or age>25;
select * from mydb.tt2 where addtime is null; (addtime为字段名）

```

```mysql
--模糊查询
select * from stu where name like "%ang%"; （包含式查询，中间含ang的）
select * from stu where name like "____"; （含四位字母的）
select * from stu where name like "%02";（开头为02的）
```

```mysql
MySQL中的运算符：
算术运算符：+-*/%
比较运算符：= > < >= <= <> !=
数据库特有的比较： in, not in , is null, is not null, like, bwtween and
逻辑运算符：and or not
```

```mysql
select count(id) from stu;             --读取数据条数
select count(*),max(age),min(age),sum(age),avg(age) from stu;    
```

```mysql
分组查询 group by
select classid,count(*) from stu group by classid;
select classid,count(*),avg(age) from stu group by classid;
select classid,count(*) as num from stu group by classid having age>23;
查询顺序
①where ②group by xxx having xxx③order by ④limit 
--一般having都在group by之后，顺序不能颠倒；
```

```mysql
聚合函数：count()、max()、min()、sum()、avg()、sum()
```

```mysql
Q: 统计每个班男生、女生各有多少人
A: select classid,sex,count(*) from stu group by classid,sex; 

升降序排序：
select * form stu order by age desc;                 对年龄降序排序
select * from stu order by classid asc,age desc;   对年龄降序，对序号升序排序

有时候数据是成千上万的，不能完全显示，因此用limit来显示前xx条便于观看
limit查询：
select * from stu order by age desc limit 3;           --按年龄降序排序只拿前三条
select * from stu limit 2,3;      （第一个排除，第二个取）          --跳过两条取三条
e.g.
select * from stu where classid='python03' order by age asc limit 2
```

```mysql
数据库授权，备份和恢复
授权：
	show databases;可以看到一个user表；
	desc user;查看user表字段；
	select host,user,password from mysql.user;
	select * from user\G
格式：grant 允许操作 on 库名.表名 to 账号@来源 identified by '密码';
示例：grant all on *.* to zhangsan@'%' identified by ＂123";
     msyql -h ip地址 -u zhagnsan -p 远程连接数据库
     show databases;
     drop user 'zhangsan' @'%';
```

```mysql
备份：
	mysqldump -u root -p 数据库名 [表名] > 文件名.sql
	use mydemo
	select * from stu;
	cd 要备份的路径
	导库：mysqldump -u root -p mydemo>mydemo.sql   将mydemo.sql导入到路径下
	导表：mysqldump -u root -p mydemo>stu.sql  
恢复：
	mysql -u root -p 数据库名 < 文件名.sql
	use mydemo
	show tables;
	drop table stu;
	mysql -u root -p mydemo<mydemo.sql 
mysql -u root -p mydemo>mydb_tt2.sql
```

```mysql
多表联查
表与表之间关系：1对1 1对多 多对多
多表联查方式：
1.嵌套查询
2.where管理查询
3.连接join查询

left join （左连接）：返回包括左表中的所有记录和右表中连接字段相等的记录。
right join （右连接）：返回包括右表中的所有记录和左表中连接字段相等的记录。
inner join （等值连接或者叫内连接）：只返回两个表中连接字段相等的行。
full join （全外连接）：返回左右表中所有的记录和左右表中连接字段相等的记录。

一个查询结果是另一个查询的查询条件：
select * from plan where num=(select max(num) from plan);
select * from plan where id in (select id from plan where name='延边大学');
```

---

##### 4. 左连接 ，右连接，内连接和全外连接

例子：

A表

| id   | name |
| ---- | ---- |
| 1    | 小王 |
| 2    | 小李 |
| 3    | 小刘 |



B表

| id   | A_id | job    |
| ---- | ---- | ------ |
| 1    | 2    | 老师   |
| 2    | 4    | 程序猿 |



**内连接：（只有2张表匹配的行才能显示）**

```mysql
select a.name,b.job from A a inner join B b on a.id=b.A_id
```

只能得到一条记录

 小李　　老师

**左连接：（左边的表不加限制）**

```mysql
select a.name,b.job from A a left join B b on a.id=b.A_id
```

三条记录

 小王　　null　　

 小李　　老师

 小刘　　null　　

**右连接：（右边的表不加限制）**

```
select a.name,b.job from A a right join B b on a.id=b.A_id
```

两条记录

 小李　　老师

 null         程序猿

**全外连接：(左右2张表都不加限制）**

```
select a.name,b.job from A a full join B b on a.id=b.A_id
```

四条数据

 小王　　null

 小李　　老师

 小刘　　null

 null　　 程序猿

*注：在sql中l外连接包括左连接（left join ）和右连接（right join），全外连接（full join），等值连接（inner join）又叫内连接。*

----------

##### 5.  踩坑日志——使用GROUP_CONCAT报错

贴一下查询语句

```mysql
create database zyr666 (select a.id, a.校名, group_concat(DISTINCT b.中文名) as cn_name from inst2 a left join `2020库论文翻译20211131汇总_去研究机构` b on a.校名=b.校名 and b.国家 = '中国' group by a.校名 order by a.id asc)
```

Q:报错信息

```mysql
> 1260 - Row 231 was cut by GROUP_CONCAT()
```

由以上信息初步判断是创表时语句过长导致；查询一番得到解决办法如下

A:您可以将`group_concat_max_len`变量设置为更大的值。或者可能用于`GROUP_CONCAT(DISTINCT ...)`缩短结果。

```mysql
SET global group_concat_max_len=15000;
或者
SET session group_concat_max_len=15000;
```

