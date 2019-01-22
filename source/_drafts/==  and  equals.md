object 类

```
public boolean equals(Object obj) {
        return (this == obj);
    }
```

String 类
```
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

String 类重写了Object类的equals 方法，先判断内存地址是否相同。然后将字符串转化为字符数组然后依次比较每个字符是否相等。

Integer 类
```
@Override
    public int hashCode() {
        return Integer.hashCode(value);
}

public static int hashCode(int value) {
        return value;
}
```







