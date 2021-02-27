---
title: github TOC
date: 2020-12-11 21:34:35
tags: [python,github, tools]
---

https://github.com/yangyang5214/note

自动化生成目录 TOC

### 需求

```
ubuntu@VM-0-10-ubuntu:/opt/cloud/note$ tree
.
├── compilation
│   └── 1-base.md
├── python3-cookbook
│   ├── 1.md
│   ├── 2.md
│   ├── 3.md
│   └── 4.md
├── README.md
└── toc.py

2 directories, 7 files
```

目录文件是这样的，直接同步到 GitHub，然后 README.md 生成 TOC 


### 成品

![](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20201212092849.png)


<!--more-->

### 代码


so. 道理大家都懂

https://github.com/yangyang5214/note/blob/master/toc.py

```
# -*- coding: utf-8 -*-
import os
import sys

try:
    reload(sys)
    sys.setdefaultencoding('utf8')
except Exception:
    import importlib
    importlib.reload(sys)

def main():
    data = {}
    for f in os.listdir('.'):
        if f.startswith('.') or f in ignore_file:
            continue
        data[f] = {}
        for sub_f in os.listdir(f):
            if sub_f.startswith('.'):
                continue
            title = get_title(os.path.join(f, sub_f))
            data[f][sub_f] = title.encode('utf-8').decode('utf-8')

    if not data:
        return

    with open('README.md', 'w') as f:
        f.write("\n")
        for key, val in data.items():
            f.write('## {}'.format(key))
            f.write("\n")
            for k, v in val.items():
                f.write(' - [{}](./{}/{})'.format(v, key, k))
                f.write("\n")
            f.write("\n")


def get_title(file):
    with open(file, 'r') as f:
        for line in f.readlines():
            if line.startswith('##'):
                return line.strip().replace('## ', '').strip()


ignore_file = {
    'README.md',
    'toc.py'
}

if __name__ == '__main__':
    main()
```


直接在项目的根目录运行

```
python toc.py 
```

兼容 python2 python3 


