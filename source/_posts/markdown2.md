---
title: markdown2
date: 2019-06-18 22:47:38
tags: python
---


> [markdown2 的学习](https://github.com/trentm/python-markdown2)


### install

```
pip install markdown2
```

### quick usage

```
import markdown2


if __name__ == '__main__':
    print(markdown2.markdown("*boo!*"))
    print(markdown2.markdown("### boo"))
    print(markdown2.markdown("**beer**"))
    print(markdown2.markdown("![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/20190610102702_es_deprecated.png)"))
```
结果：

```
<p><em>boo!</em></p>

<h3>boo</h3>

<p><strong>beer</strong></p>

<p><img src="https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/20190610102702_es_deprecated.png" alt="" /></p>
```

或者 脚本：

```
python -m markdown2 foo.md > foo.html
```