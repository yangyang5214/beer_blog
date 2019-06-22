---
title: hexo next photo cos
tags: tools
date: 2019-01-11 14:00:00
---

> 参考 ：https://www.jianshu.com/p/7f6425b3ffb2

因为不会写前端呀，反正也没兴趣，反正我是直接参考的，实现了。实现效果：[beer photos](https://www.hexianwei.com/photos/)


效果图：
![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190111145242.png)

<!--more-->

### python 脚本

基本上都是直接参考的，我一直在用腾讯的cos，所以后面的 python 代码改了点东西。基本流程

- 1、剪裁图片(剪裁为正方形)
```
def cut_photo(path):
    """
    剪裁图片:取长和宽最短值，剪裁为正方形
    """
    img = Image.open(path)
    w, h = img.size
    length = h if (w > h) else w
    box = (0, 0, length, length)
    imi_photo = img.crop(box)
    im_path = "im_" + path
    imi_photo.save(im_path)
    return im_path
```
- 2、生成 json 文件

这边和原作者不一样的地方是：我每上传一张图片，追加到 data.json 里。原作者好像是文件夹操作吧。
这边追加就有个逻辑是，追到到对应的日期下，而且需要倒序排序。


data.json 文件
```
{
    "list": [
        {
            "date": "2019-01",
            "arr": {
                "year": 2019,
                "month": 1,
                "link": [
                    "2019-01-02_第二个十斤.jpg",
                    "2019-01-10_剁手买个macPro加油.jpg"
                ],
                "text": [
                    "第二个十斤",
                    "剁手买个macPro加油"
                ],
                "type": [
                    "image",
                    "image"
                ]
            }
        },
        {
            "date": "2018-07",
            "arr": {
                "year": 2018,
                "month": 7,
                "link": [
                    "2018-07-05_第一个十斤.jpg"
                ],
                "text": [
                    "第一个十斤"
                ],
                "type": [
                    "image"
                ]
            }
        }
    ]
}
```

- 3、提交github

简单的 git 操作,需要注意的是   os.system 是子线程操作，所以上个 cd 命令之后，下个git 命令其实还是在当前目录的，cd 相当于没起作用，所以需要 && 连接，一起执行
```
def git_operation():
    """
    提交github
    """

    shell = 'cd ' + blog_path + ' && ' +  'git add --all' + ' && ' +  'git commit -m "add photos"' + ' && ' + 'git push'

    os.system(shell)
```
- 4、上传图片(腾讯cos)

cos有shell 命令，自己结合改一下(我执行的是 cosupload 自定义了脚本，可以返回图片上传之后的url)

传送：https://www.hexianwei.com/2018/12/30/coscmd/
- 5、部署

我这边是执行了一个脚本（blog shell）。其实就是登陆到服务器，然后 git pull && hexo g
```
# coding: utf-8
from PIL import Image
import os
import sys
import json
import ast
from datetime import datetime

blog_path = '/home/beer/beer_blog'
data_path = '/home/beer/beer_blog/themes/next/source/lib/album/data.json'

cos_img = '/photos/'
cos_im_img = '/min_photos/'


def handle_photo(file_name):
    """
    根据图片的文件名处理成需要的json格式的数据

    json 按照date 倒序
    """

    # 读取json 文件
    with open(data_path, "r", encoding='utf-8') as f:
        # 第一次文件为空时候处理
        try:
            origin_data = json.load(f)
        except:
            origin_data = '''{'list':[]}'''

        # 转化为 dict
        date_dict = ast.literal_eval(str(origin_data))
        # 获取 data_list
        data_list = date_dict.get('list')

        date_list = []
        for i in range(len(data_list)):
            date_list.append(data_list[i].get('date'))

        new_dict, date, info = build_dict(file_name)

        # date 存在 and 不存在两种情况
        if date in date_list:
            index = date_list.index(date)
            data_list[index]['arr']['link'].append(file_name)
            data_list[index]['arr']['text'].append(info)
            data_list[index]['arr']['type'].append('image')
        else:
            data_list.append(new_dict)
            date_list.append(date)

        # 按照 date 排序
        date_list.sort(key=sort_date, reverse=True)

        # 按照 date 重构 dict
        final_data_list = []
        for date in date_list:
            for data in data_list:
                if data.get('date') == date:
                    final_data_list.append(data)

        # 保存
        final_dict = {"list": final_data_list}
        with open(data_path, "w") as f:
            json.dump(final_dict, f)


def sort_date(elem):
    return int(''.join(elem.split('-')))


# 根据图片名字生成 dict
def build_dict(file_name):
    date_str, info = file_name.split("_")
    info, type = info.split(".")
    date = datetime.strptime(date_str, "%Y-%m-%d")
    year_month = '-'.join(date_str.split('-')[0:2])
    new_dict = {"date": year_month,
                "arr": {"year": date.year,
                        "month": date.month,
                        "link": [file_name],
                        "text": [info],
                        "type": ['image']
                        }
                }
    return new_dict, year_month, info


def cut_photo(path):
    """
    剪裁图片:取长和宽最短值，剪裁为正方形
    """
    img = Image.open(path)
    w, h = img.size
    length = h if (w > h) else w
    box = (0, 0, length, length)
    imi_photo = img.crop(box)
    im_path = "im_" + path
    imi_photo.save(im_path)
    return im_path


def git_operation():
    """
    提交github
    """

    shell = 'cd ' + blog_path + ' && ' +  'git add --all' + ' && ' +  'git commit -m "add photos"' + ' && ' + 'git push'

    os.system(shell)

def cosupload_operation(file_name, im_file_name):
    """
    cosupload 上传到 腾讯cos
    """
    print("cosupload" + " " + file_name + " " + cos_img + file_name)
    os.system("cosupload" + " " + file_name + " " + cos_img + file_name)
    os.system("cosupload" + " " + im_file_name + " " + cos_im_img + file_name)


def blog_operation():
    """
    部署
    blog shell 脚本：登录到服务器，打包
    """

    os.system("blog")


if __name__ == "__main__":
    '''
    1、剪裁图片(剪裁为正方形)
    2、上传图片(腾讯cos)
    3、生成 json 文件
    4、提交github
    5、部署
    '''
    # 获取参数
    file_name = sys.argv[1]
    im_file_name = cut_photo(file_name)
    handle_photo(file_name)
    git_operation()
    cosupload_operation(file_name, im_file_name)
    blog_operation()

```
