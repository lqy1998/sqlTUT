# 6.1 函数种类
1. 算术函数
2. 字符串函数
3. 日期函数
4. 转换函数
5. 聚合函数

## 算数函数
`+ - * / `···

> 创建算术函数需要的表，`NUMERIC(全体位数，小数位数)`
```sql
--DDL：创建表
CREATE TABLE SampleMath
(m  NUMERIC (10,3),
 n  INTEGER,
 p  INTEGER);

--DML：插入数据
BEGIN TRANSACTION;
INSERT INTO SampleMath(m, n, p) VALUES (500,  0,    NULL);
INSERT INTO SampleMath(m, n, p) VALUES (-180, 0,    NULL);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, NULL, NULL);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 7,    3);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 5,    2);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 4,    NULL);
INSERT INTO SampleMath(m, n, p) VALUES (8,    NULL, 3);
INSERT INTO SampleMath(m, n, p) VALUES (2.27, 1,    NULL);
INSERT INTO SampleMath(m, n, p) VALUES (5.555,2,    NULL);
INSERT INTO SampleMath(m, n, p) VALUES (NULL, 1,    NULL);
INSERT INTO SampleMath(m, n, p) VALUES (8.76, NULL, NULL);
COMMIT;

--确认表中的内容
SELECT * FROM SampleMath;
```

> `ABS(数值)`：绝对值
```sql
SELECT m, ABS(m) AS abs_col
  FROM SampleMath;
```
* 绝大多数函数对于NULL都返回NULL，转换函数COALESCE除外

> `MOD(被除数，除数)`：求余
```sql
-- 计算 n/p 的余数
SELECT n, p, MOD(n, p) AS mod_col
  FROM SampleMath;
```
* 只有SQL Server不支持`MOD`，使用 `n % p`

> `ROUND(对象数值，保留的小数位数)`：四舍五入
```sql
-- 对m列的数值进行n列位数的四舍五入
SELECT m, n, ROUND(m, n) AS round_col
  FROM SampleMath;
```

## 字符串函数

> 创建字符串函数需要的表
```sql
--DDL：创建表
CREATE TABLE SampleStr
(str1  VARCHAR(40),
 str2  VARCHAR(40),
 str3  VARCHAR(40));

--DML：插入数据
BEGIN TRANSACTION;
INSERT INTO SampleStr (str1, str2, str3) VALUES ('opx'  ,	 'rt'	 ,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abc'	 ,	'def'	 ,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('刘'	  ,	'青云'  ,	'是我');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('aaa'	 ,	NULL   ,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES (NULL 	 ,	'xyz'  ,	  NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('@!#$%',	NULL	  ,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('ABC'	 ,	NULL	  ,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('aBC'	 ,	NULL	  ,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abc青云'  ,	'abc'	,	'ABC');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abcdefabc', 'abc'	,	'ABC');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('micmic'   ,	'i'   , 'I');;
COMMIT;

--确认表中的内容
SELECT * FROM SampleStr;
```

> `||`：拼接
```sql
SELECT str1, str2, str1 || str2 AS str_concat
  FROM SampleStr;
  
-- 拼接多个字符串
SELECT str1, str2, str3, str1 || str2 || str3 AS str_concat
  FROM SampleStr
  WHERE str1 = '刘';
```
* SQL Server使用`+`，MySQL使用`CONCAT`函数
	* str1 + str2 + str3
	* CONCAT(str1, str2, str3)

> `LENGTH(字符串)`：
```sql
SELECT str1, LENGTH(str1) AS len_str
  FROM SampleStr;
```
* SQL Server使用`LEN`函数
* 多字节字符在不同DBNS中的执行结果不同，比如在MySQL中 `LENGTH(青云) = 4`  :question:不用加""吗
* MySQL中还有计算字符串长度的自有函数 `CHAR_LENGTH`

> `LOWER(字符串)`：小写转换
```sql
SELECT str1, LOWER(str1) AS low_str
  FROM SampleStr
  WHERE str1 IN ('ABC', 'aBC', 'abc', '刘');
```
* UPPER大写


> `REPLACE(对象字符串，替换前的部分，替换后的部分)`
```sql
SELECT str1, str2, str3, REPLACE(str1, str2, str3) AS rep_str
  FROM SampleStr;
```

> `SUBSTRING(对象字符串 FROM 截取的起始位置 FOR 截取的字符数)`：有多少截多少
```sql
SELECT str1, SUBSTRING(str1 FROM 3 FOR 2) AS sub_str
  FROM SampleStr;
```
* 虽然是标准SQL，但是只有PostgresSQL和MySQL支持

## 日期函数

> `CURRENT_DATE`：获取当前日期
```sql
SELECT CURRENT_DATE;
```
* PostgresSQL和MySQL支持

> `CURRENT_TIME`：获取当前时间
```sql
SELECT CURRENT_TIME;
```
* PostgresSQL和MySQL支持

> `CURRENT_TIMESTAMP`：获取当前日期和时间
```sql
SELECT CURRENT_TIMESTAMP;
```
* SQL Server，PostgresSQL和MySQL支持

> `CURRENT_TIMESTAMP`：获取当前日期和时间
```sql
SELECT CURRENT_TIMESTAMP,
       EXTRACT(YEAR   FROM CURRENT_TIMESTAMP) AS year,
       EXTRACT(MONTH  FROM CURRENT_TIMESTAMP) AS month,
       EXTRACT(DAY    FROM CURRENT_TIMESTAMP) AS day,
       EXTRACT(HOUR   FROM CURRENT_TIMESTAMP) AS hour,
       EXTRACT(MINUTE FROM CURRENT_TIMESTAMP) AS minute,
       EXTRACT(SECOND FROM CURRENT_TIMESTAMP) AS second;
```
* PostgresSQL和MySQL支持

## 转换函数
* 数据类型的转换
* 值的转换

> `CAST(转换前的值 AS 想要转换的数据类型)`：类型转换
```sql
-- 将字符串类型转换为数值类型
SELECT CAST('0001' AS INTEGER) AS int_col;
--MySQL
SELECT CAST('0001' AS SIGNED INTEGER) AS int_col; :question: why signed

-- 将字符串类型转换为日期类型
SELECT CAST('2009-12-14' AS DATE) AS date_col;
```

> `COALESCE(数据1，数据2，数据3···)`：返回可变参数中左侧开始第1个非NULL值
```sql
SELECT COALESCE(NULL, 1)                  AS col_1,
       COALESCE(NULL, 'test', NULL)       AS col_2,
       COALESCE(NULL, NULL, '2009-11-01') AS col_3;
       
SELECT COALESCE(str2, 'NULL')
  FROM SampleStr;       
```

# 6.2 谓词




















