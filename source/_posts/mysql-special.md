---
title: mysql special
date: 2019-06-05 09:40:35
tags: [java,mysql]
---



> 记录一些特别的sql

### select for delete

两张表关联删除其中一个表的数据

```
DELETE c 
FROM
	company_compare c
	JOIN ticker_info t ON c.compare_company_id = t.company_id 
WHERE
	weight = 100 
	AND ticker_status = 'ts_delisted'
```


### select for update

```
UPDATE system_region r,system_dictionary d 
SET r.name_cn = d.name_cn 
WHERE
	r.CODE = d.id
```


<!--more-->