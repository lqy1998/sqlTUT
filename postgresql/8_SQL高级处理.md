# 窗口函数（MySQL不支持？？）
**`窗口函数（OLAP online analysis processing）`：将表以窗口为单位进行分割，并在其中进行排序的函数**

* 能作为窗口函数使用的函数：
	* 能够作为窗口函数的聚合函数，SUM等
	* 专用窗口函数，RANK/DENSE_RANK/ROW_NUMBER等
* 聚合函数根据使用语法的不同，在聚合函数和窗口函数之间切换
* 窗口函数有位置限制，原则上只能在SELECT子句使用 
	* 在得到排序结果后，如果又用WHERE除去了某些记录，或者用GROUP BY进行了汇总处理，那排序结果就没有意义了
	* 其实也能在ORDER BY和UPDATE中的SET子句使用，但是几乎没有例子

## 专用窗口函数
> `RANK`
```sql
SELECT product_name, product_type, sale_price,
       RANK () OVER (PARTITION BY product_type
                     ORDER BY sale_price) 
	   AS ranking
  FROM Product;
```
* `PARTITION BY`设定排序的对象，即组别
* **`PARTITION BY`分组后的记录集合称为`窗口`，`窗口`代表范围**
* `ORDER BY`指定在组内具体按照哪一列、何种顺序排序

> `PARTITION BY`不是必须的
```sql
SELECT product_name, product_type, sale_price, 
       RANK () OVER (ORDER BY sale_price) AS ranking
  FROM Product;
```
* 将整个表作为一个大窗口使用

> 比较RANK/DENSE_RANK/ROW_NUMBER
```sql
SELECT product_name, product_type, sale_price, 
       RANK () OVER (ORDER BY sale_price) AS ranking,
       DENSE_RANK () OVER (ORDER BY sale_price) AS dense_ranking,
       ROW_NUMBER () OVER (ORDER BY sale_price) AS row_num
 FROM Product;
```
* `RANK`：1 1 1 4
* `DENSE_RANK`：1 1 1 2
* `ROW_NUMBER`：1 2 3 4
* 专用窗口函数无需参数，括号中是空的

## 作为窗口函数使用的聚合函数
> 作为窗口函数使用的聚合函数
```sql
-- SUM
SELECT product_id, product_name, sale_price,
       SUM (sale_price) OVER (ORDER BY product_id) AS current_sum
  FROM Product;
  
-- AVE
SELECT product_id, product_name, sale_price,
       AVG (sale_price) OVER (ORDER BY product_id) AS current_avg
  FROM Product;
```
* 需要在括号内指定作为汇总对象的列
* 按照ORDER BY指定的顺序进行排列，计算出id小于自身的price汇总值
* 在按照时间序列排序时，计算各个时间的销售总额时，通常会使用这种统计方法，即`累计`
* 将聚合函数作为窗口函数使用的最大特征：以“自身记录（当前记录）”作为基准进行统计

**`框架`：在窗口中指定的汇总范围**
> 移动平均
```sql
-- 截止到之前2行，包括自身共3行
SELECT product_id, product_name, sale_price,
       AVG (sale_price) OVER (ORDER BY product_id
                              ROWS 2 PRECEDING) AS moving_avg
  FROM Product;
  
-- 截止到之后2行，包括自身共3行
SELECT product_id, product_name, sale_price,
       AVG (sale_price) OVER (ORDER BY product_id
                              ROWS 2 FOLLOWING) AS moving_avg
  FROM Product;
  
-- 前后各一行，包括自身共3行
SELECT product_id, product_name, sale_price,
       AVG (sale_price) OVER (ORDER BY product_id
                              ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) AS moving_avg
  FROM Product;
```

> 两个ORDER BY
* `OVER`子句中的`ORDER BY`只用来决定窗口函数按照什么样的顺序进行计算，并不参与排列顺序，只有个例DBMS可以
```sql
-- 无法保证结果的排列顺序
SELECT product_name, product_type, sale_price, 
       RANK () OVER (ORDER BY sale_price) AS ranking
  FROM Product;
  
-- 在末尾使用ORDER BY子句对结果进行排列
SELECT product_name, product_type, sale_price, 
       RANK () OVER (ORDER BY sale_price) AS ranking
  FROM Product
  ORDER BY ranking;
```

# GROUPING运算符

> 同时得到小计和合计
```sql
SELECT '合计' AS product_type, SUM(sale_price)
  FROM Product
UNION ALL
SELECT product_type, SUM(sale_price)
  FROM Product
  GROUP BY product_type;
```

这样不行，应该这样:point_down:

> `ROLLUP`同时得到小计和合计
```sql
-- 单个聚合键
SELECT product_type, SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY ROLLUP(product_type);

-- 多个聚合键
SELECT product_type, regist_date, SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY ROLLUP(product_type, regist_date);
 
-- MySQL用法
SELECT product_type, SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY product_type WITH ROLLUP;
  
SELECT product_type, regist_date, SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY product_type, regist_date WITH ROLLUP;
```
* ROLLUP运算符的作用：从空集开始，依次叠加组合键，得到n+1个组合
	* ROLLUP()：使用NULL作为聚合键，得到全部数据的合计行的记录，即超级分组记录
	* ROLLUP(product_type)
	* ROLLUP(product_type, regist_date)

> `GROUPING`函数：判别NULL的来源
* 因为不知道是汇总导致的NULL还是缺失数据导致的NULL
* 小计和合计导致的NULL返回1，其他情况返回0
```sql
SELECT GROUPING(product_type) AS product_type, 
       GROUPING(regist_date) AS regist_date, 
	   SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY ROLLUP(product_type, regist_date);

-- 在超级分组记录的键值中插入字符串
SELECT CASE WHEN GROUPING(product_type) = 1 
            THEN '商品种类 合计' 
            ELSE product_type END AS product_type,
       CASE WHEN GROUPING(regist_date) = 1 
            THEN '登记日期 合计' 
            ELSE CAST(regist_date AS VARCHAR(16)) END AS regist_date,     -- CASE表达式所有分支的返回值一致
       SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY ROLLUP(product_type, regist_date);
```

> `CUBE`：获取2^n个聚合键组合结果
```sql
SELECT CASE WHEN GROUPING(product_type) = 1 
            THEN '商品种类 合计'
            ELSE product_type END AS product_type,
       CASE WHEN GROUPING(regist_date) = 1 
            THEN '登记日期 合计'
            ELSE CAST(regist_date AS VARCHAR(16)) END AS regist_date,
       SUM(sale_price) AS sum_price
  FROM Product
  GROUP BY CUBE(product_type, regist_date);
```
* 2^n个聚合键组合结果
	* ROLLUP()
	* ROLLUP(product_type)
	* ROLLUP(regist_date)
	* ROLLUP(product_type, regist_date)
* 也可以指定4个及以上的轴

> `GROUPING SETS`：获取n个聚合键各自的小计
```sql
SELECT CASE WHEN GROUPING(product_type) = 1 
            THEN '商品种类 合计'
            ELSE product_type END AS product_type,
       CASE WHEN GROUPING(regist_date) = 1 
            THEN '登记日期 合计'
            ELSE CAST(regist_date AS VARCHAR(16)) END AS regist_date,
       SUM(sale_price) AS sum_tanka
  FROM Product
  GROUP BY GROUPING SETS (product_type, regist_date);
```
:question: SETS和(需要空格吗






