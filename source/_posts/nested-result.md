---
title: nested_result
date: 2019-04-10 20:21:57
tags: java
---


> 背景：es 通过多关键字搜索，出来多个数据集。然后，需要将结果返回。问题来了，多个集合的顺序排列问题。业务采用结果嵌套展示。下面例子说明。

比如：三个关键字的集合为：[[A, B, C, D], [1, 2, 3], [QQ, WX, LOL, TX, MMP]]。所以，我们需要的结果是：[A，1，QQ，B，2，WX，C，3，LOL,D,TX,MMP]


### 解决

先构造数据。


思路：

- 先找到 max_size 
- 外层循环确定索引位置
- 里面循环来确定取哪个集合的值
- subList.size() > max 来判断取值边界


<!--more-->
```
@Test
public void nestedResultTest(){
    //结果集
    List<String> resultList = new ArrayList<>
    //构造数据
    List<List<String>> list = buildData();
    int max = 0;
    //获取 max_size
    for (List<String> subList : list) {
        if (subList.size() > max){
            max = subList.size();
        }
    }
    for (int i = 0; i < max; i++) {
        for (List<String> subList : list) {
            if (subList.size() > i){
                resultList.add(subList.get(i)
            }
        }
    }
    System.out.println(resultList);
}
public List<List<String>> buildData(){
    List<String> list1 = Arrays.asList("A","B
    List<String> list2 = Arrays.asList("1","2
    List<String> list3 = Arrays.asList("QQ","
    List<List<String>> list = new ArrayList<>
    list.add(list1);
    list.add(list2);
    list.add(list3);
    return list;
}


# 结果
[A, 1, QQ, B, 2, WX, C, 3, LOL, D, TX, MMP]
```


