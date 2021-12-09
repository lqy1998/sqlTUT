# 数据库

**`数据库（DB）`：将大量数据保存起来，通过计算机加工而成的可以进行高效访问的数据集合。**  
**`数据库管理系统（DBMS）`：用来管理数据库的计算机系统。**
* 通过DBMS实现多个用户同时安全简单地操作大量数据
* DBMS通过数据地保存格式进行分类：
	* 层次数据库
	* 关系数据库:star:
	* 面向对象数据库
	* XML数据库
	* 键值存储系统

**`表`：关系数据库中用来管理数据地二维表。**     
* 根据SQL语句地内容返回地数据必须是二维表的形式
* 一个单元格只能输入一个数据
 
**`字段`：表的每一列。**      
* 同一个表的每一行记录都拥有相同的若干字段  

**`记录`：表的每一行。**      
* 关系数据库必须以行为单位进行数据读写

# SQL

**SQL语句种类：**
* `DDL(Data Definition Language)`：创建、删除、变更数据库和表等对象  
* `DML(Data Manipulation Language)`:star:：查询插入、更新、删除表中的记录  
* `DCL(Data Control Language)`：确认或取消对数据库中的数据的变更，赋予或取消用户的操作权限 

**SQL书写规则：**
* SQL语句以`;`结尾
* SQL语句不区分大小写，一般关键字大写，表名首字母大写，其余（列名等）小写
* 插入的数据区分大小写
* 字符串和日期使用`'`括起来，日期格式多种
* 单词使用半角空格或换行分隔

**命名规则：**
* 使用半角英文字母、数字、下划线作为数据库、表和列的名称
* 名称以半角英文字母开头
* 同一数据库中表的名称不能重复，同一表中列的名称不能重复

**数据类型：**  
所有列必须指定数据类型。
* INTEGER
* CHAR
	* 类似CHAR(10)，指定该列可以存储的字符串的最大长度
	* RDBMS不同，长度单位不同，为字符个数或者字节长度
	* 定长字符串，使用半角空格补足
* VARCHAR
	* 可变长字符串
* DATE
	* 年月日，Oracle中还包括时分秒

**`约束`：除数据类型外，对列中存储数据进行限制或者追加条件的功能。**
* `NOT NULL约束`，只能以列为单位进行设置
* `主键约束`
	* `键`：在指定特定数据时使用的列的组合
	* `主键`：可以特定一行数据的列

# 代码

**创建数据库**
```sql
	CREATE DATABASE <数据库名称>;
```
**创建表**
```sql
	CREATE TABLE <表名>
	(<列名1> <数据类型> <该列所需约束>,
	 <列名2> <数据类型> <该列所需约束>,
	 <列名3> <数据类型> <该列所需约束>,
	 ···
	 <该表的约束1>, <该表的约束2>, ···);
	-------------------------------------------
	CREATE TABLE Product
	(product_id     CHAR(4)       NOT NULL,
	 product_name   VARCHAR(100)  NOT NULL,
	 product_type   VARCHAR(32)   NOT NULL,
	 sale_price     INTEGER       ,
	 purchase_price INTEGER       ,
	 regist_date    DATE          ,
	 PRIMARY KEY (product_id));
```
**删除表**
```sql
	DROP TABLE <表名>;
```

**更新表：添加列，删除列**  
无法撤销
```sql
	ALTER TABLE <表名> ADD COLUMN <列的定义>;	
	ALTER TABLE <表名> DROP COLUMN <列名>;
	-------------------------------------------
	ALTER TABLE Product ADD COLUMN product_name_pinyin  VARCHAR(100);
	ALTER TABLE Product DROP COLUMN product_name_pinyin;
```	
**插入数据**
```sql
	BEGIN TRANSACTION;
	INSERT INTO Product VALUES ('0001', 'T恤' ,'衣服', 1000, 500, '2009-09-20');
	INSERT INTO Product VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
	INSERT INTO Product VALUES ('0003', '运动T恤', '衣服', 4000, 2800, NULL);
	INSERT INTO Product VALUES ('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20');
	INSERT INTO Product VALUES ('0005', '高压锅', '厨房用具', 6800, 5000, '2009-01-15');
	INSERT INTO Product VALUES ('0006', '叉子', '厨房用具', 500, NULL, '2009-09-20');
	INSERT INTO Product VALUES ('0007', '擦菜板', '厨房用具', 880, 790, '2008-04-28');
	INSERT INTO Product VALUES ('0008', '圆珠笔', '办公用品', 100, NULL, '2009-11-11');
	COMMIT;
```	
**变更表名**	
```sql	
	ALTER TABLE Poduct RENAME TO Product;
```
