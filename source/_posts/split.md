---
title: split
date: 2019-04-08 23:03:22
tags: java
---

> 记一次改了一个逗号就报错的问题。


前人写的代码。注意最后有个空格，非常重要。

```
String[] param_list = (params + ",,,,,,,,,,,,,,,,,, ").split(",");
```

### case 1 

先记一个简单的吧。

split 的参数是一个正则表达式。如果参数是 **.** ，需要转义。
```
    @Test
    public void splitTest(){
        String s = "12233.2323";
        String[] strings = s.split("\\.");
        for (String string : strings) {
            System.out.println(string);
        }
    }
```

<!--more-->

### case 2 

因为以前的业务逻辑问题，所以，api 如果是多个参数传过来的话，传的是逗号拼接的字符串。

例子：params=20000,2019-03-30,FY,M

按照逗号分隔为数组，后面按照索引值进行取值。

有个逻辑前置性处理了，比如，参数不够 3 位，后面要一连串的判断第三位存不存在。所以，直接处理参数：

params = params + ",,,,,,,,,,,,, "(原代码，注意最后有空格)，

后面，我接手代码了。发现没必要那么多的 ,(逗号)，最多 6 个 ，所以就修改为：

params = params + ",,,,,,"(注意没有空格)

```
    @Test
    public void splitTest2(){
        String s1 = "1,2";
        String s2 = "2,3";
        s1 = s1 + ",,,";
        s2 = s2 + ",,, ";
        String[] strings1 = s1.split(",");
        String[] strings2 = s2.split(",");
        System.out.println("s1 length:"+strings1.length);
        System.out.println("s2 length:"+strings2.length);
    }


    # 结果
    s1 length:2
    s2 length:5
```


结果不一行呀，所以，以至于后面代码出现了数组了数组下标越界的异常。

#### 问题分析

```
//split 调用了其他方法，并且参数 为 0 
public String[] split(String regex) {
        return split(regex, 0);
}
```


直接看 if (limit == 0)。如果后面的都是 空，则 执行 resultSize--，后面进行字符串的截取。
```
public String[] split(String regex, int limit) {
        /* fastpath if the regex is a
         (1)one-char String and this character is not one of the
            RegEx's meta characters ".$|()[{^?*+\\", or
         (2)two-char String and the first char is the backslash and
            the second is not the ascii digit or ascii letter.
         */
        char ch = 0;
        if (((regex.value.length == 1 &&
             ".$|()[{^?*+\\".indexOf(ch = regex.charAt(0)) == -1) ||
             (regex.length() == 2 &&
              regex.charAt(0) == '\\' &&
              (((ch = regex.charAt(1))-'0')|('9'-ch)) < 0 &&
              ((ch-'a')|('z'-ch)) < 0 &&
              ((ch-'A')|('Z'-ch)) < 0)) &&
            (ch < Character.MIN_HIGH_SURROGATE ||
             ch > Character.MAX_LOW_SURROGATE))
        {
            int off = 0;
            int next = 0;
            boolean limited = limit > 0;
            ArrayList<String> list = new ArrayList<>();
            while ((next = indexOf(ch, off)) != -1) {
                if (!limited || list.size() < limit - 1) {
                    list.add(substring(off, next));
                    off = next + 1;
                } else {    // last one
                    //assert (list.size() == limit - 1);
                    list.add(substring(off, value.length));
                    off = value.length;
                    break;
                }
            }
            // If no match was found, return this
            if (off == 0)
                return new String[]{this};

            // Add remaining segment
            if (!limited || list.size() < limit)
                list.add(substring(off, value.length));

            // Construct result
            int resultSize = list.size();
            if (limit == 0) {
                while (resultSize > 0 && list.get(resultSize - 1).length() == 0) {
                    resultSize--;
                }
            }
            String[] result = new String[resultSize];
            return list.subList(0, resultSize).toArray(result);
        }
        return Pattern.compile(regex).split(this, limit);
    }
```

#### 最后解决

我看着拼接 ,(逗号)，最后 split 不优雅。所以，事先定义数组，然后塞值。

```
        String s = "11,223";
        String[] params = new String[]{"","","","","",""};
        String[] strings = s.split(",");
        for (int i = 0; i < strings.length; i++) {
            params[i] = strings[i];
        }
```

今天，深入看了一下，split 的 api ,看来还是 split 优雅。明天上班改回来。

```
//第二个参数传 -1（不传 0 就 ok）
String[] params = s.split(",",-1);
```