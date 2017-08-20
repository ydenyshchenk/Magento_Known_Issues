# Magento Known Issues
### Current document will be appended time to time with new stupid issues :)

## [Q] Some product images missing product values for attributes 'image', 'small_image', 'thumbnail' are set on product edit page
Possibly issue caused by "no selection" values set on store scopes.
Execute the next command to check it:
```sql
select cpev_s.*, cpev_0.value as store_value from catalog_product_entity_varchar cpev_s join eav_attribute ea on cpev_s.attribute_id = ea.attribute_id left join catalog_product_entity_varchar cpev_0 on cpev_0.row_id = cpev_s.row_id and cpev_0.attribute_id = cpev_s.attribute_id and cpev_0.store_id = 0 where cpev_s.value = 'no_selection' and ea.attribute_code in ('image', 'small_image', 'thumbnail') and cpev_s.store_id > 0 and cpev_s.value != cpev_0.value and cpev_s.value = 'no_selection';
```
It will return something like that if table really has different values on default and store scopes:
```sql
+----------+--------------+----------+--------+--------------+----------------------------+
| value_id | attribute_id | store_id | row_id | value        | store_value                |
+----------+--------------+----------+--------+--------------+----------------------------+
|    67722 |           87 |        1 |    481 | no_selection | /3/5/355sss1_main.jpg      |
|    67723 |           88 |        1 |    481 | no_selection | /3/5/355sss1_main.jpg      |
|    67724 |           89 |        1 |    481 | no_selection | /3/5/355sss1_main.jpg      |
|    67814 |           87 |        1 |    503 | no_selection | /s/k/skb2031_main.jpg      |
|     6769 |           87 |        2 |    503 | no_selection | /s/k/skb2031_main.jpg      |
|    67815 |           88 |        1 |    503 | no_selection | /s/k/skb2031_main.jpg      |
|     6770 |           88 |        2 |    503 | no_selection | /s/k/skb2031_main.jpg      |
|    67816 |           89 |        1 |    503 | no_selection | /s/k/skb2031_main.jpg      |
|     6771 |           89 |        2 |    503 | no_selection | /s/k/skb2031_main.jpg      |
+----------+--------------+----------+--------+--------------+----------------------------+
9 rows in set (0.06 sec)
```
In this case you need to delete these rows by the next SQL command:
```sql
delete cpev_s.* from catalog_product_entity_varchar cpev_s join eav_attribute ea on cpev_s.attribute_id = ea.attribute_id left join catalog_product_entity_varchar cpev_0 on cpev_0.row_id = cpev_s.row_id and cpev_0.attribute_id = cpev_s.attribute_id and cpev_0.store_id = 0 where cpev_s.value = 'no_selection' and ea.attribute_code in ('image', 'small_image', 'thumbnail') and cpev_s.store_id > 0 and cpev_s.value != cpev_0.value and cpev_s.value = 'no_selection';
```