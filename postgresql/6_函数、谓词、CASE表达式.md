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

> `EXTRACT`：截取日期元素
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
**`谓词`：返回值为真值的函数。**
* 相当于这个函数返回一个真值列表，根据TRUE和FALSE选择输出哪些记录

## `LIKE`：字符串的部分一致查询
* 前方一致：选出"**ddd**abc"
* 中间一致：选出"ab**ddd**c"或"abc**ddd**"或"**ddd**abc"
* 后方一致：选出"abc**ddd**"
	* 最宽松的查询条件是中间一致，同时包含了前方一致和后方一致的查询结果
	* **以`字符串中是否包含该条件`的规则为基础的查询称为`模式匹配`**
	
> 创建需要的SampleLike表
```sql
--DDL：创建表
CREATE TABLE SampleLike
( strcol VARCHAR(6) NOT NULL,
  PRIMARY KEY (strcol));

--DML：插入数据
BEGIN TRANSACTION;
INSERT INTO SampleLike (strcol) VALUES ('abcddd');
INSERT INTO SampleLike (strcol) VALUES ('dddabc');
INSERT INTO SampleLike (strcol) VALUES ('abdddc');
INSERT INTO SampleLike (strcol) VALUES ('abcdd');
INSERT INTO SampleLike (strcol) VALUES ('ddabc');
INSERT INTO SampleLike (strcol) VALUES ('abddc');
COMMIT;
```

> `LIKE`
```sql
-- 前方一致查询
SELECT *
  FROM SampleLike
  WHERE strcol LIKE 'ddd%';

-- 中间一致查询
SELECT *
  FROM SampleLike
  WHERE strcol LIKE '%ddd%';

-- 后方一致查询
SELECT *
  FROM SampleLike
  WHERE strcol LIKE '%ddd';
  
-- 查询“abc + 任意2个字符”  
SELECT *
  FROM SampleLike
  WHERE strcol LIKE 'abc__';

-- 查询“abc + 任意3个字符”
SELECT *
  FROM SampleLike
 WHERE strcol LIKE 'abc___';
```
* `%` 代表 “0字符以上的任意字符串”
* `_` 代表 “任意1个字符”

## `BETWEEN`：范围查询
>`BETWEEN`
* 包含临界值，不包含需要用`< 和 >`
```sql
SELECT product_name, sale_price
  FROM Product
  WHERE sale_price BETWEEN 100 AND 1000;
```

## `IS NULL, IS NOT NULL`：判断是否为NULL，详见比较运算符

## `IN, NOT IN`：OR的简便用法
>`IN, NOT IN`
```sql
-- 使用OR指定多个进货单价进行查询
SELECT product_name, purchase_price
  FROM Product
  WHERE purchase_price =  320
    OR purchase_price =  500
    OR purchase_price = 5000;

-- 使用IN指定多个进货单价进行查询 
SELECT product_name, purchase_price
  FROM Product
  WHERE purchase_price IN (320, 500, 5000);

-- 使用NOT IN排除多个进货单价进行查询 
SELECT product_name, purchase_price
  FROM Product
  WHERE purchase_price NOT IN (320, 500, 5000);
```
* `IN, NOT IN`无法选出NULL数据

### 使用子查询作为`IN`谓词的参数
* 子查询就是SQL内部生成的表，可以将表、视图作为IN的参数

> 创建需要的ShopProduct表
```sql
--DDL：创建表
CREATE TABLE ShopProduct
(shop_id    CHAR(4)       NOT NULL,
 shop_name  VARCHAR(200)  NOT NULL,
 product_id CHAR(4)       NOT NULL,
 quantity   INTEGER       NOT NULL,
 PRIMARY KEY (shop_id, product_id));         -- 指定了两列作为主键
 
--DML：插入数据
BEGIN TRANSACTION;
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000A',	'东京',	'0001',	30);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000A',	'东京',	'0002',	50);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000A',	'东京',	'0003',	15);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋', '0002', 30);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0003', 120);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0004', 20);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0006', 10);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0007', 40);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',	'0003',	20);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',	'0004',	50);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',	'0006',	90);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',	'0007',	70);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity) VALUES ('000D',	'福冈',	'0001',	100);
COMMIT;
```

> 使用子查询作为`IN`的参数
```sql
--取得在大阪店销售的商品的销售单价
SELECT product_name, sale_price
  FROM Product
  WHERE product_id IN (SELECT product_id 
                         FROM ShopProduct
                         WHERE shop_id = '000C');
```
* 可以完美应对数据变更，这样的程序称为“易维护程序”，或者“免维护程序”

> 使用子查询作为`NOT IN`的参数
```sql
SELECT product_name, sale_price
  FROM Product
  WHERE product_id NOT IN (SELECT prouct_id 
                             FROM ShopProduct
                             WHERE shop_id = '000A');
```

## `EXISTS`：替换IN
> `EXISTS`只需在右侧书写一个参数，通常是一个关联子查询
```sql
--取得在大阪店销售的商品的销售单价
SELECT product_name, sale_price
  FROM Product AS P
  WHERE EXISTS (SELECT *
                  FROM ShopProduct AS SP
                  WHERE SP.shop_id = '000C'
                    AND SP.product_id = P.product_id);
```

> `EXISTS`只关心记录是否存在，返回哪些列都无所谓，不过通常是`*`
```sql
SELECT product_name, sale_price
  FROM Product AS P
  WHERE EXISTS (SELECT 1    -- 这里可以书写恰当的常数
                  FROM ShopProduct AS SP
                  WHERE SP.shop_id = '000C'
                    AND SP.product_id = P.product_id);
```

> `NOT EXISTS` 替换 `NOT IN`
```sql
SELECT product_name, sale_price
  FROM Product AS P
  WHERE NOT EXISTS (SELECT *
                      FROM ShopProduct AS SP
                      WHERE SP.shop_id = '000A'
                        AND SP.product_id = P.product_id);
```

# 6.3 CASE表达式：条件分支

## 搜索CASE表达式
> 语法
```sql
CASE WHEN <求值表达式> THEN <表达式>
	 WHEN <求值表达式> THEN <表达式>
	 WHEN <求值表达式> THEN <表达式>
	 ELSE <表达式>
END
```
* 由于表达式最终都会返回一个值，因此CASE表达式在SQL语句执行时，每个记录都会转化为一个值

> 将A~C的字符串加入到商品种类中
```sql
SELECT product_name,
       CASE WHEN product_type = '衣服'     THEN 'A：' || product_type
            WHEN product_type = '办公用品'  THEN 'B：' || product_type
            WHEN product_type = '厨房用具'  THEN 'C：' || product_type
            ELSE NULL
       END AS abc_product_type
  FROM Product;
```
* ELSE子句可以省略，默认为ELSE NULL，但是最好显式写出

> 实现列转换
```sql
-- 使用GROUP BY无法实现列转换
SELECT product_type, SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY product_type;
  
-- 使用CASE对按照商品种类得到的销售单价合计值进行行列转换
SELECT SUM(CASE WHEN product_type = '衣服'    THEN sale_price ELSE 0 END) AS sum_price_clothes,
       SUM(CASE WHEN product_type = '厨房用具' THEN sale_price ELSE 0 END) AS sum_price_kitchen,
       SUM(CASE WHEN product_type = '办公用品' THEN sale_price ELSE 0 END) AS sum_price_office
  FROM Product;
```

## 简单CASE表达式
> 语法
```sql
CASE <表达式>
     WHEN <表达式> THEN <表达式>
	 WHEN <表达式> THEN <表达式>
	 WHEN <表达式> THEN <表达式>
	 ELSE <表达式>
END
```

> 将A~C的字符串加入到商品种类中
```sql
-- 使用简单CASE表达式的情况
SELECT product_name,
       CASE product_type
            WHEN '衣服'    THEN 'A：' || product_type
            WHEN '办公用品' THEN 'B：' || product_type
            WHEN '厨房用具' THEN 'C：' || product_type
            ELSE NULL
       END AS abc_product_type
  FROM Product;
  
--使用搜索CASE表达式的情况
SELECT product_name,
       CASE WHEN product_type = '衣服'    THEN 'A：' || product_type
            WHEN product_type = '办公用品' THEN 'B：' || product_type
            WHEN product_type = '厨房用具' THEN 'C：' || product_type
            ELSE NULL
       END AS abc_product_type
  FROM Product;
```
* 将想要求值的表达式书写一次后，就无需在后续的WHEN子句中重复书写了，但是如果想在WHEN子句中指定不同列，简单CASE就无能为力了











