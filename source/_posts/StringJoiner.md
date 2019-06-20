---
title: StringJoiner
date: 2019-06-20 17:23:40
tags: java
---

> StringJoiner 的用法。

都知道 String.join 的用法。但是，如果是 list 需要 join 呢？

可能会这样去 实现 list 的  join 方法。没什么不对，但是，总感觉不高大上。所以，看了 String.join 的源码，给了一些启发。

```
public void joinMethodOne(){
    List<String> list = new ArrayList<>();
    StringBuilder idStr = new StringBuilder();
    for (Object s : list) {
        idStr.append(",").append(s);
    }
    String strs = idStr.substring(1);
}
```

### String.join 方法

```
public static String join(CharSequence delimiter, CharSequence... elements) {
    Objects.requireNonNull(delimiter);
    Objects.requireNonNull(elements);
    // Number of elements not likely worth Arrays.stream overhead.
    StringJoiner joiner = new StringJoiner(delimiter);
    for (CharSequence cs: elements) {
        joiner.add(cs);
    }
    return joiner.toString();
}
```

<!--more-->

核心就是调用了 StringJoiner 类，调用了 StringJoiner.add 方法


### StringJoiner

#### 属性：

- prefix  前缀
- delimiter 分隔符
- suffix 后缀
- value StringBuilder 类型
- emptyValue         

```
this.emptyValue = this.prefix + this.suffix;
```

#### 构造器

- StringJoiner( delimiter)  调用的是  this(delimiter, "", "");
- StringJoiner( delimiter, prefix, suffix)


#### 方法 add

```
public StringJoiner add(CharSequence newElement) {
    prepareBuilder().append(newElement);
    return this;
}

private StringBuilder prepareBuilder() {
    if (value != null) {
        value.append(delimiter);
    } else {
        value = new StringBuilder().append(prefix);
    }
    return value;
}
```

其实，还是调用的是 StringBuilder 方法去做的。



转化为方法（只有分隔符的情况）：

```
public static void joinMethodTwo(List<String> list) {
    StringBuilder idStr = null;
    for (Object s : list) {
        if (idStr == null) {
            idStr = new StringBuilder();
            idStr.append(s);
        } else {
            idStr.append(",").append(s);
        }
    }
    System.out.println(idStr);
}
```


但是：joinMethodOne and joinMethodTwo 的区别是 joinMethodTwo 巧妙的避过了 首次添加分隔符。而且，joinMethodOne 如果 list 集合为 empty ,还会出现  substring(1) StringIndexOutOfBoundsException 的异常。如果，加上判断，则 strs 初始化浪费空间，所以建议使用第二种。

```
public static void joinMethodOne(List<String> list) {
    StringBuilder idStr = new StringBuilder();
    for (Object s : list) {
        idStr.append(",").append(s);
    }
    String strs = idStr.substring(1);
    System.out.println(strs);
}
public static void joinMethodTwo(List<String> list) {
    StringBuilder idStr = null;
    for (Object s : list) {
        if (idStr == null) {
            idStr = new StringBuilder();
            idStr.append(s);
        } else {
            idStr.append(",").append(s);
        }
    }
    System.out.println(idStr);
}
```

或者使用 StringJoiner 。封装一个方法

```
public static String listJoin(List<String> list, String delimiter) {
    Objects.requireNonNull(delimiter);
    Objects.requireNonNull(list);
    StringJoiner joiner = new StringJoiner(delimiter);
    for (CharSequence cs : list) {
        joiner.add(cs);
    }
    return joiner.toString();
}
```

### StringJoiner 源码

```
public final class StringJoiner {

    private final String prefix;
    private final String delimiter;
    private final String suffix;

    private StringBuilder value;

    private String emptyValue;

    public StringJoiner(CharSequence delimiter) {
        this(delimiter, "", "");
    }

    public StringJoiner(CharSequence delimiter,
                        CharSequence prefix,
                        CharSequence suffix) {
        Objects.requireNonNull(prefix, "The prefix must not be null");
        Objects.requireNonNull(delimiter, "The delimiter must not be null");
        Objects.requireNonNull(suffix, "The suffix must not be null");
        // make defensive copies of arguments
        this.prefix = prefix.toString();
        this.delimiter = delimiter.toString();
        this.suffix = suffix.toString();
        this.emptyValue = this.prefix + this.suffix;
    }

    public StringJoiner setEmptyValue(CharSequence emptyValue) {
        this.emptyValue = Objects.requireNonNull(emptyValue,
            "The empty value must not be null").toString();
        return this;
    }

    @Override
    public String toString() {
        if (value == null) {
            return emptyValue;
        } else {
            if (suffix.equals("")) {
                return value.toString();
            } else {
                int initialLength = value.length();
                String result = value.append(suffix).toString();
                // reset value to pre-append initialLength
                value.setLength(initialLength);
                return result;
            }
        }
    }

   
    public StringJoiner add(CharSequence newElement) {
        prepareBuilder().append(newElement);
        return this;
    }

    public StringJoiner merge(StringJoiner other) {
        Objects.requireNonNull(other);
        if (other.value != null) {
            final int length = other.value.length();
            // lock the length so that we can seize the data to be appended
            // before initiate copying to avoid interference, especially when
            // merge 'this'
            StringBuilder builder = prepareBuilder();
            builder.append(other.value, other.prefix.length(), length);
        }
        return this;
    }

    private StringBuilder prepareBuilder() {
        if (value != null) {
            value.append(delimiter);
        } else {
            value = new StringBuilder().append(prefix);
        }
        return value;
    }

    public int length() {
        // Remember that we never actually append the suffix unless we return
        // the full (present) value or some sub-string or length of it, so that
        // we can add on more if we need to.
        return (value != null ? value.length() + suffix.length() :
                emptyValue.length());
    }
}

```