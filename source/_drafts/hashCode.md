
> hashCode。哈希码产生的依据：哈希码并不是完全唯一的，它是一种算法，让同一个类的对象按照自己不同的特征尽量的有不同的哈希码，但不表示不同的对象哈希码完全不同。也有相同的情况，看程序员如何写哈希码的算法。


### Object 类的 hashCode 
```
public native int hashCode();
```

### String 类的 hashCode
```
public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```

```
/** The value is used for character storage. */
private final char value[];

/** Cache the hash code for the string */
private int hash; // Default to 0


public String(String original) {
        this.value = original.value;
        this.hash = original.hash;
    }
```

如果初始化一个 String  , 调用hashCode 方法。

eg : new String("11)

this.hash = "11".hash  // hash = 0

然后调用hashCode 方法

 for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
  }


val[] = ["1","1"]


i = 0  时，val[0] = 49，h = 49 

i = 1 时，val[1] = 49，h = 49 * 31 + 49 = 1568

也会产生hash碰撞

```
@Test
 public void charTest(){
        System.out.println("gdejicbegh".hashCode());
        System.out.println("hgebcijedg".hashCode());
 }

-801038016
-801038016
```
### Integer 的 hashCode 方法

直接返回值

```
    @Override
    public int hashCode() {
        return Integer.hashCode(value);
    }

    public static int hashCode(int value) {
        return value;
    }
```
