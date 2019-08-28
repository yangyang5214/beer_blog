---
title: poi IFERROR YEAR
date: 2019-08-28 20:58:12
tags: java
---


> 公司的 excel 模型，需要转换为网页版展示，所以，需要做一套转换，传入参数，然后计算公式，返回结果，核心就是公司计算（使用 poi 实现）。


记录一下遇到的一大问题：一开始以为是 IFERROR 函数 poi 解析不了，后来发现是 YEAR 函数的问题。



传送门： https://stackoverflow.com/questions/57684777/how-to-use-evaluateincell-with-cell-of-iferror-funcation/57685810#57685810


### 问题描述

- 这段代码设置 公式：

```
IFERROR("FY"&YEAR("2019/04/26")-"6","NA")
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828113208_82b49a984fb7a6213abcb4c183aafa39.png)

- 查看设置之后的类型：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828113243_b39f5ffcd5cddeb4162f3a4a6c20275d.png)


<!--more-->

- 调用  evaluateFormulaCell 函数计算公式:

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828113310_1d72d232043b870e68c19b990d3e08b8.png)

- 获取值

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828113339_d4ea9aa8b92d19c067bccbbb615680cc.png)

返回值的 NA

- 但是，在 excel 内是：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828113449_dc7deab23106a3a4fde16619683f29b7.png)

- 写了一段测试代码（上面是 debug）, 还是 NA

![20190828120047_d42379290f9a70bc83ababeee2a18c14.png](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828120047_d42379290f9a70bc83ababeee2a18c14.png)



也就是说 excel 能够识别，但是 poi 解析不了。


### 更新问题


```
@Test
    public void ifERRORTest() {
        Workbook workbook = new XSSFWorkbook();
        workbook.setForceFormulaRecalculation(true);
        Sheet sheet = workbook.createSheet("1");
        Cell cell = sheet.createRow(0).createCell(0);
        cell.setCellFormula("IFERROR(\"FY\"&2019-\"1\",\"NA\")");
//        cell.setCellFormula("YEAR(\"2018-01-01\")");
        FormulaEvaluator formulaEvaluator = workbook.getCreationHelper().createFormulaEvaluator();
        formulaEvaluator.evaluateInCell(cell);
        System.out.println(cell.getCellType());
        System.out.println(cell.getStringCellValue());
    }
```

这样写是可以得出正确结果的，所以，IFERROR 函数没有问题，出问题的是 YEAR("2019/01/01") 函数


![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828134914_af8213215b06291fc10e542801cb16ef.png)


![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828135138_5b8ef3bb17dc4dbf6d0644c0729a7d33.png)

### 解决

[YEAR 函数](https://support.office.com/en-us/article/year-function-c64f017a-1354-490d-981f-578e8ec8d3b9)

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190828231234_84cf1bf330e98597c4750862203e25a3.png)


excel 能够解析的原因可能是，excel 的容错性比较强，之前 excel 的公式是中文引号也可以，但是 poi 就不行。

### 总结

多上  https://stackoverflow.com/ 。

