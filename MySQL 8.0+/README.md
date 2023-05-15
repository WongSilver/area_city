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