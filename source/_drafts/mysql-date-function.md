---
title: mysql_date_function
tags: mysql
---

### LAST_DAY 每月最后一天

```
SELECT *
from derived_Daily
where ticker_id = 2010002294
and trading_date = LAST_DAY(trading_date)
```
![LAST_DAY](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190704101137_3d5a8e5454bfd35d5a15ebc233bb4152.png)

### WEEKDAY  每周五 

0 为周一，以此类推，周日为 6 

```
SELECT *
from derived_Daily
where ticker_id = 2010002294
and WEEKDAY(trading_date) =0
ORDER BY trading_date desc
```
![20190704101302_16274bb42e4816ad63b95ad6bd02de0e.png](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190704101302_16274bb42e4816ad63b95ad6bd02de0e.png)

###  年报 FY 

取每年的最后一天的数据:

#### DAYOFYEAR 
(DAYOFYEAR(trading_date) = 365 or DAYOFYEAR(trading_date) = 366)


```
SELECT *
from derived_Daily
where ticker_id = 2010002294
and (DAYOFYEAR(trading_date) = 365 or DAYOFYEAR(trading_date) = 366)
ORDER BY trading_date desc
```

![day_of_year](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190704102045_c342d0d6f182116be6d176d6e153514f.png)

#### 年报  FY 

```
SELECT *
from derived_Daily
where ticker_id = 2010002294
and trading_date = concat(YEAR(trading_date),'-12-31')
```

![YEAR](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190704103516_ab0e2a83232c63e7a94695ff493e3700.png)

### 半年报   FH  

例如： 2018-06-30，2018-12-31

```
SELECT *
from derived_Daily
where ticker_id = 2010002294
and (trading_date = concat(YEAR(trading_date),'-06-30') or trading_date = concat(YEAR(trading_date),'-12-31'))
```

![FH](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190704104434_a057b74f4852f3a96ef9cb8c24047b90.png)

### 季度报 FQ

例如： 2018-03-31,2018-06-30，2018-09-30，2018-12-31

![FQ](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190704104630_a54b83d3fbb9e1dfed18cb4ef98a074a.png)

```
SELECT *
from derived_Daily
where ticker_id = 2010002294
and (trading_date = concat(YEAR(trading_date),'-03-31') 
or trading_date = concat(YEAR(trading_date),'-06-30')
or trading_date = concat(YEAR(trading_date),'-09-30')
or trading_date = concat(YEAR(trading_date),'-12-31'))
```