Distinct可以限制一列而查询多列吗？  


比如： 

```sql
SELECT DISTINCT user_id, id, device_id, device_type, sku_id FROM tb_points_equipment_detail
WHERE points_count = 0 and update_time < 1598274497
```

这里的DISTINCT会同时作用在`user_id, id, device_id, device_type, sku_id`上，而不会单单只作用在`user_id`上

也就是说， 查询出来的结果中，user_id会存在许多相同的值。

