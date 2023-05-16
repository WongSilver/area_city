### 代码示例:
```sql
-- 反解一个坐标对应的城市
SELECT
	* 
FROM
	area_city_geo 
WHERE
	deep = 2 
	AND ST_Intersects (
		polygon,
		ST_GeomFromText ( 'POINT(117.122632 31.822205)', 0 )
	);


-- 查询某城市的周边城市
SELECT
	* 
FROM
	area_city_geo 
WHERE
	deep = 1 
	AND ST_Intersects (
		polygon, 
		(SELECT polygon FROM area_city_geo WHERE ext_path = '安徽省 六安市')
	);
	
-- 查看安徽省的所有市、区
SELECT
	id,
	ext_path,
	geo,
	polygon 
FROM
	area_city_geo 
WHERE
	ext_path LIKE '%安徽省%';
```

### 优化说明
> MySQL的空间索引很难生效 [官方文档](https://dev.mysql.com/doc/refman/8.0/en/spatial-index-optimization.html)，从而导致空间查询异常缓慢
> 可通过增加一个geometry类型的字段 polygon_envelope 来自建索引
> 查询的时候先查 polygon_envelope ，然后再查 polygon 字段，速度就会快很多(10倍+)
> 也可同时切换成MyISAM引擎，会比InnoDB引擎的空间查询快很多。


```sql
-- 自建索引
UPDATE area_city_geo SET polygon_envelope = ST_Envelope ( polygon );

-- 查询示例
SELECT
	* 
FROM
	area_city_geo 
WHERE
	deep = 2 
	AND ST_Intersects (
		polygon_envelope,
		ST_GeomFromText ( 'POINT(117.122632 31.822205)', 0 )
	) = 1 
	AND ST_Intersects (
		polygon,
		ST_GeomFromText ( 'POINT(117.122632 31.822205)', 0 )
	) = 1
```
