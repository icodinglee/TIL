####  mysql数据库基本存储单元 
database ->  tables -> column;

####  登录
mysql -u[username] -p[password]

#### 退出
mysql> exit;

#####  Database

![databases.png](http://upload-images.jianshu.io/upload_images/5545478-17cb27b12559cf83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
- 显示所有数据库内所有database表
mysql> show databases;  (sql语句后分号不能省略）

- 创建database表
mysql> create  database [database_name];

- 进入并使用某个database表
mysql> use [database_name];

- 显示当前database下所有tables;
mysql> show tables;

- 删除数据库
mysql> drop database [database_name];

##### Table

![test数据库下的table表](http://upload-images.jianshu.io/upload_images/5545478-5448c977a9fc7603.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 查看数据库下某个table表下的内容
mysql> use [table_name];

- 创建table表
mysql> create table [table_name] (
column_name column_type,
column_name column_type,
column_name column_type
);

#### Column
![column表](http://upload-images.jianshu.io/upload_images/5545478-30c171263f44c672.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面是对数据的一些操作

-    查看所有数据
mysql> select * from Customer;
(下面所有column的名称都暂定为Customer)

- 插入数据
mysql> insert into Customer values("hxn","hxn","beijing","china",null,null);
(不插入数据时设置为null)

-  删除数据
mysql>  delete from  Customer where First_Name="hxn";

- 更新数据
mysql>  update Customer set First_Name="hxn" where Last_Name="lhk";

- 添加列
mysql>alter table Customer add [column_name]  [data_type];

- 删除列
mysql>alter  table Customer drop [column_name] ;

- 指定方式查询
select  \*  from Customer where City like "h%";（以h开头）
select  \*  from Customer where City like "%h"; (以h结尾)
select  \*  from Customer where First_name="lhk" or Last_Name="hxn"; （or的使用）
select  \*  from Customer where (First_Name > 100 or First_Name < 30) and City = null;（or与and连用）

- 联表查询  join
select * from Customer2 join Customer where Customer.First_Name = Customer2.x;


