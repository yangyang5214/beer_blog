---
title: clean_code
date: 2019-03-27 21:55:43
tags: [java,note]
---


长期更新

原文地址：https://www.hexianwei.com/2019/03/27/clean-code/

> 主要记录自己遇到的代码不规范的 case，并进行重构。欢迎吐槽！

### String.join 的使用

在项目里会遇到，list 拼接的问题。

```
JSONArray tickers = jsonObject.getJSONArray("tickers");
Map<String, Integer> tempMap = new TreeMap<>();
StringBuilder idStr = new StringBuilder();
for (Object s1 : tickers) {
     Integer companyId = draftingMapper.selectCompanyId(s1.toString());
        idStr.append(",").append(companyId);
            tempMap.put(s1.toString(), companyId);
        }
String companyIds = idStr.substring(1);
```
上述代码的意思是：companyId 之前使用 ‘,’ 拼接，使用 String.join 方法优化

```
JSONArray tickers = jsonObject.getJSONArray("tickers");
String companyIds = String.join(",", (List) tickers);
tickers.stream().forEach(p -> {
Integer companyId = draftingMapper.selectCompanyId(p.toString());
tempMap.put(p.toString(), companyId);
});
```

<!--more-->

### 数组的 contains

在使用数组进行判断 contains 的时候，会这样写：
```
private String[] tableArray = new String[]{"market_weekly", "market_daily", "derived_daily", "industry_derived_daily"};

if (Arrays.asList(tableArray).contains(table)) {
        //....
}
```

使用 **Arrays.asList** 转为 List 然后操作。本身就是数组然后转为集合。。。多此一举！


```
//内部实际实现就是 loop
if ( ArrayUtils.contains(tableArray,table)) {
        //....
}
```

### 反 equals 方法


使用了 反 equals 。。。
```
if (!companyId.equals("")) {
        maps = companyCompareService.comparableCompanyByCompany(companyId, range, limit, sort, sortType);
} else {
        maps = companyCompareService.comparableCompanyByIndustry(industryId, range, limit, sort, sortType);
}
```

优化

```
if (companyId.equals("")) {
        maps = companyCompareService.comparableCompanyByIndustry(industryId, range, limit, sort, sortType);
} else {
        maps = companyCompareService.comparableCompanyByCompany(companyId, range, limit, sort, sortType);
}
```

### for 循环

下面代码，因为每次取的是：i and i+1 ,所有 有个判断是  **i + 1 >= list.size()**，但是，每次 loop 都会进行判断。
```
int num = list.size();
   
for (int i = 0; i < num; i++) {
     
    if (i + 1 >= list.size()) {
                break;
    }
    
    if (list.get(i).get("company_shortname_cn").equals(list.get(i + 1).get("company_shortname_cn"))) {
                
            ...
            
    }
}
```
优化
```
int flag = list.size() -1 ;
   
for (int i = 0; i < flag; i++) {
     
    if (list.get(i).get("company_shortname_cn").equals(list.get(i + 1).get("company_shortname_cn"))) {
                
            ...
            
    }
}
```

#### 拆分方法

差不多同样的代码数量级, 关键是一个 if else 100 行的代码，完全可以提取方法去做。

```
if (Arrays.asList(tableArray).contains(table)) {
               //105 - 187 line （82 行代码）
            } else {
                //189 - 310 line  (121 行代码)
            }
}
```

### 一个方法多次调用

```
String from = draftingDate(jsonObject).get(0);
String to = draftingDate(jsonObject).get(1);


//最终返回的是 list , 索引为 0 的是 from ,索引为 1 的是 to
private List<String> draftingDate(JSONObject jsonObject) {

    ...

    list.add(from);
    list.add(to);
    return list;
}
```

一个方法执行了两次，第一次执行取索引为 0 ， 第二次取索引 为 1。多余啊！只调用一次就可以。只执行一次。
```
List<String> list = draftingDate(jsonObject);
String from = list.get(0);
String to = list.get(1);
```