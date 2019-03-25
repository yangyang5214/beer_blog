---
title: String.indexOf
date: 2019-02-24 10:18:44
tags: java
---


> 类似 String 的 indexOf 的实现

```
   /**
     * 返回  s2 在 s1 中首次出现的位置
     * <p>
     * 逻辑：
     * 1、找到第一个字符匹配
     * 2、然后依次向后比较
     * 3、如果累计比较的次数 == s2 的大小,则返回第一次匹配的位置
     *
     * @param s1
     * @param s2
     * @return
     */
    public static int indexOf(String s1, String s2) {
        int sourceSize = s1.length();
        int targetSize = s2.length();

        if (sourceSize < targetSize) {
            return -1;
        }

        char[] source = s1.toCharArray();
        char[] target = s2.toCharArray();

        char first = target[0];
        int max = sourceSize - targetSize;

        for (int i = 0; i <= max; i++) {
            //Look for first character
            if (first != source[i]) {
                while (++i <= max && source[i] != first) ;
            }

            /* Found first character, now look at the rest of s2 */
            if (i <= max) {
                int j = i + 1;
                int end = i + targetSize;
                for (int k = 1; j < end && source[j] == target[k]; j++, k++) ;

                if (j == end) {
                    /* Found whole string. */
                    return i;
                }
            }
        }
        return -1;
    }
```