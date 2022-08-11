## SELECT

```sql
	SELECT col, col2, col3 FROM table
```

```sql
	SELECT * FROM table
```

```sql
		SELECT col, "new col" AS col2 FROM table
		-- key 为 col2 value 都为 new col
```

## WHERE

### =, !=, <, <=, >, >=

```sql
WHERE col != 4
```

### BETWEEN xxx AND xxx

```sql
WHERE col BETWEEN 1.5 AND 10.5
```

### NOT BETWEEN xxx AND xxx

```sql
WHERE col NOT BETWEEN 1.5 AND 10.5
```

### IN xxx

```sql
WHERE col IN (3, 4, 5)
```

```sql
		WHERE col IN ("A", "B", "C")
```

### NOT IN xxx

```sql
WHERE col NOT IN (3, 4, 5)
```

```sql
		WHERE col NOT IN ("D", "E", "F")
```

### =

```sql
WHERE col = "abc"
```

### != or <>

```sql
WHERE col != "abcd"
```

### LIKE

```sql
WHERE col LIKE "abc"
```

### NOT LIKE

```sql
WHERE col LIKE "abcd"
```

### %

```sql
WHERE col LIKE "%AT%"
-- matches "AT" "ATTIC" "CAT" "BATS"
-- %AT% 表示 AT 前后可以有任意字符
```

### \_

```sql
WHERE col LIKE "AN_"
-- matches "AND", but not "AN"
```

```sql
SELECT * FROM table WHERE col >10 and col2 < 100
```

```sql
SELECT * FROM table WHERE col >10 or col2 < 100
```

## DISTINCT

DISTINCT 会直接删除重复行

```sql
SELECT DISTINCT col, col2, col3 FROM table
```

## ORDER BY

排序，对于数字来说是 1，2，3  
对于文本来说是按文本的字母序  
ORDER BY col ASC/DESC

```sql
SELECT * FROM table ORDER BY col ASC
```

## LIMIT OFFSET

通常和 ORDER BY 一起使用  
LIMIT 指定返回多少结果，OFFECT 指定从哪一行返回（翻页）

```sql
SELECT * FROM table WHERE col = 10 ORDER BY col2 ASC LIMIT 100 OFFSET 10
```

```sql
SELECT col, col2
FROM table
WHERE condition AND/OR condition
ORDER BY col ASC/DESC, col2 ASC/DESC
LIMIT num_limit OFFSET num_offset
```

## JOIN

### INNER JOIN

保留两个表都存在的数据，比如 table 有 1 2 3，table2 有一个 table_id 和 table 对应，但是，table2 的数据里面有 1 2 3 4 5 数据，这时候，如果 JOIN 的话就只会合并 table 的 1 2 3 和 table2 的 1 2 3，table2 的 4 5 会被舍弃

```sql
INNER JOIN table2 ON table.id = table2.id
```

### LEFT/RIGHT/FULL JOIN

LEFT/ROGHT/FULL OUTER JOIN 等价

LEFT 保留 table 的所有行，不管有没有匹配
RIGHT 保留 table2 的所有行，不管有没有匹配
保留所有行，不管有没有匹配
如果没有存在，会用 null 来填充结果数据

## NULL

当计算平均值的时候，如果是 0 则会参与平均值计算，如果是 null 则不参与平均值计算

```sql
WHERE col IS NULL
```

```sql
WHERE col IS NOT NULL
```

查询中使用表达式

```sql
SELECT col / 2 as half_particle_speed
-- 对结果做了一个除以 2 的计算
```

```sql
FROM table
WHERE ABS(particle_positions) * 10 > 500
-- 要求属性绝对值乘以 10 大于 500
```

### 统计函数

- COUNT(\*)
  - 统计数据行数
- COUNT(col)
  - 统计 col 非 null 的行数
- MIN(col)
  - 找到 col 中最小的一行
- MAX(col)
  - 找到 col 中最大的一行
- AVG(col)
  - 对 col 的所有行取平均值
- SUM(col)
  - 对 col 所有行求和

## GROUP BY

分母有的时候可以用 SELECT COUNT(\*) FROM table

## HAVING

在 GROUP BY 之后，还想继续筛选，同 WHERE

```sql
SELECT CASE WHEN col IS NULL THEN 0 ELSE 1 END
```

```sql
SELECT
  role,
  CASE
    WHEN years_employed < 3 THEN '0-3'
    WHEN years_employed < 6 THEN '3-6'
    ELSE '6-9'
  END AS employ_year,
  COUNT(*) as count
FROM
  employees
GROUP BY
  ROLE,
  employ_year
```

## 运行顺序

```sql
SELECT DISTINCT col, AGG_FUNC(col_or_expression), ...
FROM table
	INNTER JOIN table2
	ON table.col = table2.col
	WHERE constraint_expression
	GROUP BY col
	HAVING constraint_expreession
	ORDER BY col ASC/DESC
	LIMIT count OFFSET count
```

### FROM 和 JOINs

第一个执行，确定一个整体的数据范围，如果要 JOIN 不同表，可能会生成一个临时 table 来用于下面的过程，第一步可以理解为切顶一个数据源表

### WHERE

确定数据来源之后，WHERE 在这个数据源中按要求进行数据筛选并丢弃不符合要求的数据行，所有的筛选用 col 都来自 FROM 圈定的表，AS 别名还不能使用，因为别名可能是一个还没执行的表达式

### GROUP BY

对之前的数据进行分组，统计等

### HAVING

在 GROUP BY 之后对数据继续筛选，AS 别名也不能在这个阶段使用

### SELECT

确定结果之后，SELECT 用来对结果 col 简单筛选或计算，决定输出什么数据

### DISTINCT

如果数据行有重复，DISTINCT 将进行去重

### ORDER BY

在结果集确定的情况下，ORDER BY 对结果排序，因为 SELECT 中的表达式已经执行完了，此时可以用 AS 别名

### LIMIT / OFFECT

最后 LIMIT 和 OFFSET 从排序的结果中截取部分数据
