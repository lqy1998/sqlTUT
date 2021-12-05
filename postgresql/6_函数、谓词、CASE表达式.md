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
INSERT INTO SampleStr (str1, str2, str3) VALUES ('opx',	        'rt'	,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abc'	,	'def'	,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('山田'	,	'太郎'  ,	'是我');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('aaa'	,	NULL    ,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES (NULL	,	'xyz',	        NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('@!#$%',	NULL	,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('ABC'	,	NULL	,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('aBC'	,	NULL	,	NULL);
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abc太郎',	'abc'	,	'ABC');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('abcdefabc',   'abc'	,	'ABC');
INSERT INTO SampleStr (str1, str2, str3) VALUES ('micmic',	      'i',        'I');;
COMMIT;

--确认表中的内容
SELECT * FROM SampleStr;
```

## 日期函数

## 转换函数
























