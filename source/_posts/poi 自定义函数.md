---
title: poi 自定义函数
tags: java
date: 2019-09-04 09:47:44
---


> 参考链接 ：https://svn.apache.org/repos/asf/poi/trunk/src/examples/src/org/apache/poi/ss/examples/formula/UserDefinedFunctionExample.java


averageifs: https://support.office.com/zh-cn/article/AVERAGEIFS-%E5%87%BD%E6%95%B0-48910C45-1FC0-4389-A028-F7C5C3001690


### poi 函数

#### 获取 poi 函数

```
public void getSupportFunction() {
    System.out.println(FunctionEval.getSupportedFunctionNames());
    System.out.println(FunctionEval.getNotSupportedFunctionNames());
}
```

#### 支持

[ABS, ACOS, ACOSH, ADDRESS, AND, AREAS, ASIN, ASINH, ATAN, ATAN2, ATANH, AVEDEV, AVERAGE, CEILING, CHAR, CHOOSE, CLEAN, CODE, COLUMN, COLUMNS, COMBIN,
 CONCATENATE, COS, COSH, COUNT, COUNTA, COUNTBLANK, COUNTIF, DATE, DAY, DAYS360, DEGREES, DEVSQ, DGET, DMAX, DMIN, DOLLAR, DSUM, ERROR.TYPE, EVEN, EXACT, EXP, FACT, FALSE, FIND, FIXED, FLOOR, FREQUENCY, FV, GEOMEAN, HLOOKUP, HOUR, HYPERLINK, IF, INDEX, INDIRECT, INT, INTERCEPT, IPMT, IRR, ISBLANK, ISERR, ISERROR, ISLOGICAL, ISNA, ISNONTEXT, ISNUMBER, ISREF, ISTEXT, LARGE, LEFT, LEN, LN, LOG, LOG10, LOOKUP, LOWER, MATCH, MAX, MAXA, MDETERM, MEDIAN, MID, MIN, MINA, MINUTE, MINVERSE, MIRR, MMULT, MOD, MODE, MONTH, NA, NOT, NOW, NPER, NPV, ODD, OFFSET, OR, PERCENTILE, PI, PMT, POISSON, POWER, PPMT, PRODUCT, PROPER, PV, RADIANS, RAND, RANK, RATE, REPLACE, REPT, RIGHT, ROMAN, ROUND, ROUNDDOWN, ROUNDUP, ROW, ROWS, SEARCH, SECOND, SIGN, SIN, SINH, SLOPE, SMALL, SQRT, STDEV, SUBSTITUTE, SUBTOTAL, SUM, SUMIF, SUMPRODUCT, SUMSQ, SUMX2MY2, SUMX2PY2, SUMXMY2, T, TAN, TANH, TEXT, TIME, TODAY, TRANSPOSE, TREND, TRIM, TRUE, TRUNC, UPPER, VALUE, VAR, VARP, VLOOKUP, WEEKDAY, YEAR]

#### 不支持

[ABSREF, APP.TITLE, ARGUMENT, ASC, AVERAGEA, BETADIST, BETAINV, BINOMDIST, CALL, CELL, CHIDIST, CHIINV, CHITEST, CONFIDENCE, CORREL, COVAR, CRITBINOM, DATEDIF, DATESTRING, DATEVALUE, DAVERAGE, DB, DBCS, DCOUNT, DCOUNTA, DDB, DPRODUCT, DSTDEV, DSTDEVP, DVAR, DVARP, ENABLE.TOOL, END.IF, ERROR, EVALUATE, EXEC, EXPONDIST, FDIST, FINDB, FINV, FISHER, FISHERINV, FORECAST, FTEST, GAMMADIST, GAMMAINV, GAMMALN, GET.CELL, GET.DOCUMENT, GET.WINDOW, GET.WORKBOOK, GET.WORKSPACE, GETPIVOTDATA, GOTO, GROWTH, HARMEAN, HYPGEOMDIST, INFO, ISPMT, KURT, LAST.ERROR, LEFTB, LENB, LINEST, LOGEST, LOGINV, LOGNORMDIST, MIDB, N, NEGBINOMDIST, NORMDIST, NORMINV, NORMSDIST, NORMSINV, NUMBERSTRING, PEARSON, PERCENTRANK, PERMUT, PHONETIC, PRESS.TOOL, PROB, QUARTILE, REGISTER.ID, RELREF, REPLACEB, RETURN, RIGHTB, RSQ, SAVE.TOOLBAR, SEARCHB, SKEW, SLN, STANDARDIZE, STDEVA, STDEVP, STDEVPA, STEP, STEYX, SYD, TDIST, TIMEVALUE, TINV, TRIMMEAN, TTEST, TYPE, USDOLLAR, VARA, VARPA, VDB, WEIBULL, WINDOW.TITLE, ZTEST]

<!--more-->

### 自定义 poi  函数


#### INFO 函数

举个栗子：INFO 函数 poi 不支持

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904171233_1e90c87c6819c1a090a3a6d05b396314.png)


excel 是可用的：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904171700_ffca1a60a33bbff38fee62670641800e.png)


![测试 test 函数](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904172417_83bcf10ac2a60114e10a2b940a2d01b9.png)


测试代码：

```
 public void registerFunctionOfINFO() {
     Workbook workbook = getWorkbook();
     workbook.setForceFormulaRecalculation(true);
     FormulaEvaluator formulaEvaluator = workbook.getCreationHelper().createFormulaEvaluator();
     Sheet sheet = workbook.getSheetAt(0);
     Cell cell = sheet.getRow(0).getCell(0);
     formulaEvaluator.evaluateInCell(cell);
     System.out.println(cell.getStringCellValue());
 }
```


错误信息：NotImplementedFunctionException: INFO

```
Exception in thread "main" org.apache.poi.ss.formula.eval.NotImplementedException: Error evaluating cell Sheet1!A1
	at org.apache.poi.ss.formula.WorkbookEvaluator.addExceptionInfo(WorkbookEvaluator.java:344)
	at org.apache.poi.ss.formula.WorkbookEvaluator.evaluateAny(WorkbookEvaluator.java:285)
	at org.apache.poi.ss.formula.WorkbookEvaluator.evaluate(WorkbookEvaluator.java:216)
	at org.apache.poi.xssf.usermodel.BaseXSSFFormulaEvaluator.evaluateFormulaCellValue(BaseXSSFFormulaEvaluator.java:56)
	at org.apache.poi.ss.formula.BaseFormulaEvaluator.evaluateInCell(BaseFormulaEvaluator.java:145)
	at org.apache.poi.xssf.usermodel.XSSFFormulaEvaluator.evaluateInCell(XSSFFormulaEvaluator.java:85)
	at org.apache.poi.xssf.usermodel.XSSFFormulaEvaluator.evaluateInCell(XSSFFormulaEvaluator.java:34)
	at com.yangyang.java.UserDefinedFunctionPoi.registerFunctionOfINFO(UserDefinedFunctionPoi.java:45)
	at com.yangyang.java.UserDefinedFunctionPoi.main(UserDefinedFunctionPoi.java:26)
Caused by: org.apache.poi.ss.formula.eval.NotImplementedFunctionException: INFO
	at org.apache.poi.ss.formula.functions.NotImplementedFunction.evaluate(NotImplementedFunction.java:40)
	at org.apache.poi.ss.formula.OperationEvaluatorFactory.evaluate(OperationEvaluatorFactory.java:153)
	at org.apache.poi.ss.formula.WorkbookEvaluator.evaluateFormula(WorkbookEvaluator.java:541)
	at org.apache.poi.ss.formula.WorkbookEvaluator.evaluateAny(WorkbookEvaluator.java:275)
	... 7 more
```

自定义实现 INFO 函数：


为了测试，就直接返回固定值 windwos

```
public class INFOFuncation implements Function {
    @Override
    public ValueEval evaluate(ValueEval[] args, int srcRowIndex, int srcColumnIndex) {
        return new StringEval("windwos");
    }
}
```

使用 ：         `FunctionEval.registerFunction("INFO", new INFOFuncation());`  注册。


结果：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904172721_3930997527da696c79a227b0465bd9a0.png)



#### AVERAGEIFS 函数


![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904173033_6e906ae66a8fbe7047609c9458003242.png)


同样 自定义函数，返回固定值：

```
public class AVERAGEIFSuncation implements Function {
    @Override
    public ValueEval evaluate(ValueEval[] args, int srcRowIndex, int srcColumnIndex) {
        return new NumberEval(10);
    }
}
```

错误信息：

```
Exception in thread "main" java.lang.IllegalArgumentException: AVERAGEIFS is a function from the Excel Analysis Toolpack. Use AnalysisToolpack.registerFunction(String name, FreeRefFunction func) instead.
```

有个 bug , 我也是找半天：提示使用 `AnalysisToolpack.registerFunction(String name, FreeRefFunction func)`方法。但是：查无此类。。。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904173745_46afa004eb89eca1b4ddd6c07e6d4ce3.png)


AnalysisToolpack  VS  AnalysisToolpak . poi 的 bug ,拼写错误。

![20190904173818_50d00c1a0ff4fd35997ee986b8ee59d9.png](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904173818_50d00c1a0ff4fd35997ee986b8ee59d9.png)


注册： 

```
AnalysisToolPak.registerFunction("AVERAGEIFS", new AVERAGEIFSFuncation());
```


```
public class AVERAGEIFSFuncation implements FreeRefFunction {

    @Override
    public ValueEval evaluate(ValueEval[] args, OperationEvaluationContext ec) {
        return new NumberEval(10);
    }
}
```

运行：


![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190904174732_134598fd61aaae66b2aee6c9eb458e1c.png)


上面都是为了测试注册函数可不可用：


自己实现的 AVERAGEIFS （选择区和条件区一样，条件都是 <> 0, 所以简单实现了，如果遇到其他 case 再更新）


```
public class AverageifsFuncation implements FreeRefFunction {

    @Override
    public ValueEval evaluate(ValueEval[] args, OperationEvaluationContext ec) {
        String argStr0 = getMainContent(args[0]);
        String argStr1 = getMainContent(args[1]);
        String argStr2 = getMainContent(args[2]);

        if (!argStr0.equals(argStr1)) {
            throw new RuntimeException("暂时不支持");
        }

        XSSFEvaluationWorkbook workbook = (XSSFEvaluationWorkbook) ec.getWorkbook();

        EvaluationSheet sheet = workbook.getSheet(ec.getSheetIndex());
        List<Double> list = new ArrayList<>();
        AreaReference areaReference = new AreaReference(argStr0, SpreadsheetVersion.EXCEL2007);
        CellReference[] cellReferences = areaReference.getAllReferencedCells();
        for (CellReference cellReference : cellReferences) {
            EvaluationCell cell = sheet.getCell(cellReference.getRow(), cellReference.getCol());
            if (cell == null) {
                continue;
            }
            try {
                Double value = Double.valueOf(getCellValue(cell).toString());
                if (isInCondition(argStr2, value)) {
                    list.add(value);
                }
            } catch (NumberFormatException e) {
                //不是数字类型的
            }
        }
        double[] arrays = new double[list.size()];
        for (int i = 0; i < arrays.length; i++) {
            arrays[i] = list.get(i);
        }
        return new NumberEval(StatUtils.mean(arrays));
    }

    public Object getCellValue(final EvaluationCell cell) {
        switch (cell.getCellType()) {
            case BOOLEAN:
                return cell.getBooleanCellValue();
            case ERROR:
                return cell.getErrorCellValue();
            case NUMERIC:
                return cell.getNumericCellValue();
            case STRING:
            case BLANK:
                return cell.getStringCellValue();
            case FORMULA:
                //可能有坑
            default:
                throw new IllegalArgumentException("未知类型：" + cell.getCellType());
        }
    }

    /**
     * 判断是否满足条件
     *
     * @return
     */
    public boolean isInCondition(String condition, double value) {
        if (condition.startsWith("<>")) {
            double conditionValue = Double.parseDouble(condition.substring(condition.indexOf("<>") + 2));
            if (value == conditionValue) {
                return false;
            }
            return true;
        } else {
            throw new IllegalArgumentException("AverageifsFuncation 暂时不支持此类格式");
        }
    }

    public String getMainContent(ValueEval argStr) {
        String str = argStr.toString();
        return str.substring(str.indexOf("[") + 1, str.lastIndexOf("]"));
    }

}
```

本文完。。。！