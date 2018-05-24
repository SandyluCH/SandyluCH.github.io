###mysql
     MySQL 5.7支持的存储引擎有：InnoDB,MyISAM,Memory,Merge,Archive,Federated,CSV,BALCKHOLE等，
使用命令show engines \G;语句可查看系统所支持的引擎类型。
     数据库访问接口主要有：
	 1、ODBC 
       Open Database Connectivity
	 2、JDBC
	   Java Data Base Connectivity
	 3、ADO.NET
     4、PDO
	 (php data object)为PHP访问数据库定义了一个轻量级的、一致性的接口，它提供了一个数据访问抽象层。 
  	 
#### MySQL的优势
主要优势：
（1）速度：运行速度快
（2）价格：免费
（3）容易使用
（4）可移植性
（5）丰富的接口
（6）支持查询语言
（7）安全性和连接性

#### 登录MySQL
命令行登录：
````
> cd C:\Program Files\MySQL\MySQL Server 5.7\bin\
> mysql -h localhost -u root -p
回车输入正确密码进入
mysql>

````

#### 使用
1. 查看当前存在的所有数据库

```mysql> SHOW DATABASES; ```

2. 创建数据库

```mysql> CREATE DATABASE test_db; ```

3. 查看创建好的数据库test_db

```mysql> SHOW CREATE DATABASE test_db\G ```

4. 删除数据库

```mysql> DROP DATABASE test_db; ```

5. 创建表

创建表之前需要现选择数据库，语法```USER 数据库名 ```

````
mysql>   CREATE TABLE tb_emp1(
 id INT(11),
 name VARCHAR(25),
 deptId INT(11),
 salary FLOAT
 );

````

6. 显示某数据库中的所有存在的表

```mysql> SHOW TABLES; ```

7. 查看数据表结构

```mysql> DESCRIBE tb_emp1; ```

8. 修改表名

```mysql> ALTER TABLE tb_emp1 RENAME  tb_emplement1 ;```

9. 修改字段的数据类型

```mysql> ALTER TABLE tb_emplement1 MODIFY name VARCHAR(30); ```

10. 修改字段名

```mysql> ALTER TABLE tb_emplement1 CHANGE name tname VARCHAR(50);  ```

11. 添加字段名

```mysql> ALTER TABLE tb_emplement1 ADD newname VARCHAR(30); ```

12. 删除字段

```mysql> ALTER TABLE tb_emplement1 DROP newname; ```

13. 删除表

```mysql> DROP TABLE IF EXISTS tb_emplement1; ```

### MySQL数据类型


