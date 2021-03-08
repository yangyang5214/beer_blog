---
title: crawlers for js
date: 2021-03-07 08:29:09
tags: [tools]
---

> 总结帖。爬虫可能用到的涉及 js 反爬的网站学习

### [真不卡影院](https://www.zhenbuka.com)

可以看到 f12 开启的话，会直接 debugger 断点，无限循环。。。

![](https://www.hexianwei.com/bed/zhenbuka_debug_1.png)

可以看到文件名 是 *fuck-debugger.js*, 😂，看来程序员小哥哥也是很有意思的。。。打开文件分析下：

```
let onDebug = function () {
    //乱码，不过写的是 检测到非法调试！请停止调试后刷新本页面！
    document.write('æ£€æµ‹åˆ°éžæ³•è°ƒè¯•ï¼è¯·åœæ­¢è°ƒè¯•åŽåˆ·æ–°æœ¬é¡µé¢ï¼');
    // while (true) {
    //     console.log('hi');
    //     console.clear();
    // }

};

document.onkeydown = document.onkeyup = document.onkeypress = function (event) {
    const e = event || window.event || arguments.callee.caller.arguments[0];
    
    //keyCode = 123 => F12
    if (e && e.keyCode == 123) {
        onDebug();
    }
};

let div = document.createElement('div');
Object.defineProperty(div, "id", {
    get: () => {
        clearInterval(loop);
        onDebug();
    }
});
```

虽然我是个 js 渣渣，但是也能猜到这段代码大概意思把。。。检测到 F12 就做一些不正常的事情。。。


可以使用 [ReRes 插件](https://github.com/annnhan/ReRes)，替换请求的 js 文件，偷天换日。


![](https://www.hexianwei.com/bed/zhenbuka_debug_2.png)

```
https://cq.mmiyue.com/zhenbuka/statics/js/fuck-debugger.js
https://www.hexianwei.com/js/zhenbuka_fuck-debugger.js
```

然后就可以边看电视剧，边学习网站的 api 设计

![](https://www.hexianwei.com/bed/zhenbuka_debug_3.png)


<!--more-->

### [aqistudy pm2.5 监控](https://www.aqistudy.cn/) 

> 起初是同事问这个网站怎么搞。。。我也是看了周末半天 + 周一早上， 突然开窍了（我没抓数据，没意义，就为了学习而已）


![](https://www.hexianwei.com/bed/aqistudy_1.png)

![](https://www.hexianwei.com/bed/aqistudy_2.png)

可以看到我想调试的第一步，就被拦住了。。。怎么办呢。。。
 
 
*先打开 F12 然后再进网页（hhhhhhhhhhhhhh）*


其实是这几行代码：
```
document.onkeydown = function() {
if ((e.ctrlKey) && (e.keyCode == 83)) {
  alert("检测到非法调试，CTRL + S被管理员禁用");
  return false;
}
}
document.onkeydown = function() {
var e = window.event || arguments[0];
if (e.keyCode == 123) {
  alert("检测到非法调试，F12被管理员禁用");
  return false;
}
}
document.oncontextmenu = function() {
alert('检测到非法调试，右键被管理员禁用');
return false;
}
```

进去之后呢，会发现进入了无限 debug：

```
(function anonymous(
) {
debugger
})
```

![](https://www.hexianwei.com/bed/aqistudy_3.png)

分析 js, 也就三个文件

![](https://www.hexianwei.com/bed/aqistudy_4.png)

一看这 Jquery 版本就比较怪，后来在 *jquery.min.js?v=1.1* 最后发现了一点问题，看最后几行，大概猜有点问题。。。
```
eval(function(p,a,c,k,e,d){e=function(c){return(c<a?"":e(parseInt(c/a)))+((c=c%a)>35?String.fromCharCode(c+29):c.toString(36))};if(!''.replace(/^/,String)){while(c--)d[e(c)]=k[c]||e(c);k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1;};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p;}('3 l(){2 r="v",n="5",e="8";3 o(r){m(!r)4"";h(2 t="",n=w,e=0;e<r.6;e++){2 o=r.9(e)^n;n=n*e%u+p,t+=k.j(o)}4 t}q{2 a=["r",o("z"),"g",o("x"),3(t){m(!t)4"";h(2 o="",a=r+n+e+"7",c=y,f=0;f<t.6;f++){2 i=t.9(f);c=(c+1)%a.6,i^=a.9(c),o+=k.j(i)}4 o}("@"),"b","e","d"].B().E("");!3 c(r){(1!==(""+r/r).6||0===r)&&3(){}.D(a)(),c(++r)}(0)}C(a){A(l,s)}}',41,41,'||var|function|return||length|||charCodeAt||||||||for||fromCharCode|String|txsdefwsw|if|||2333|try||100||256|V|44106|ê°­|45860|ê°¯|setTimeout|reverse|catch|constructor|join'.split('|'),0,{}));
```

最终删除掉，最后几行，同样使用 ReRes插件替换加载的 js
```
https://www.aqistudy.cn/js/jquery.min.js?v=1.1
https://www.hexianwei.com/js/jquery.min.js?v=1.1
```

运行，报错，缺少一些方法。（ jquery.min.js 文件 生成的一些方法吧）

![](https://www.hexianwei.com/bed/aqistudy_5.png)
![](https://www.hexianwei.com/bed/aqistudy_6.png)

本来想 通过 js 嵌入下列代码 删掉的，：

```
# 页面加载完成之后才有效
var badTable = document.evaluate('//script[@type="text/javascript" and contains(text(), "endebug")]', document, null, XPathResult.FIRST_ORDERED_NODE_TYPE, null).singleNodeValue;
badTable.parentNode.removeChild(badTable);
```

回到上一步 *endebug* *txsdefwsw* 方法缺失，上一步我们是直接删除了，可以猜到原来代码的意思是 创建  *endebug* *txsdefwsw* 方法。所以，我们替换成下列的空方法

即使有参数，我们定义了空方法，什么也不做  

```
eval("function endebug(){}");
eval("function txsdefwsw(){}");
```

完工。。。

![](https://www.hexianwei.com/bed/aqistudy_7.png)

看下那段加密的代码：

![](https://www.hexianwei.com/bed/aqistudy_8.png)

[格式化一下](https://www.html.cn/tool/js_beautify/)

```
function endebug(off, code) {
    if (!off) {
        ! function (e) {
            function n(e) {
                function n() {
                    return u
                }

                function o() {
                    window.Firebug && window.Firebug.chrome && window.Firebug.chrome.isInitialized ? t("on") : (a = "off", console.log(d), console.clear(), t(a))
                }

                function t(e) {
                    u !== e && (u = e, "function" == typeof c.onchange && c.onchange(e))
                }

                function r() {
                    l || (l = !0, window.removeEventListener("resize", o), clearInterval(f))
                }
                "function" == typeof e && (e = {
                    onchange: e
                });
                var i = (e = e || {}).delay || 500,
                    c = {};
                c.onchange = e.onchange;
                var a, d = new Image;
                d.__defineGetter__("id", function () {
                    a = "on"
                });
                var u = "unknown";
                c.getStatus = n;
                var f = setInterval(o, i);
                window.addEventListener("resize", o);
                var l;
                return c.free = r, c
            }
            var o = o || {};
            o.create = n, "function" == typeof define ? (define.amd || define.cmd) && define(function () {
                return o
            }) : "undefined" != typeof module && module.exports ? module.exports = o : window.jdetects = o
        }(), jdetects.create(function (e) {
            var a = 0;
            var n = setInterval(function () {
                if ("on" == e) {
                    setTimeout(function () {
                        if (a == 0) {
                            a = 1;
                            setTimeout(code)
                        }
                    }, 200)
                }
            }, 100)
        })
    }
}
```

赶紧加到自己网站把。。。（估计能拦住大部分人了）



















