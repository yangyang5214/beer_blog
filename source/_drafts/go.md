---
title: go 基础
tags: go
---

> [go 基础学习记录](http://www.runoob.com/go/go-tutorial.html)


### 安装

https://golang.google.cn/dl/ 下载，pkg 包，安装。


配置环境变量：
```
export PATH=$PATH:/usr/local/go/bin
```

- 测试 
```
package main

import "fmt"

func main() {
   /* 这是我的第一个简单的程序 */
   fmt.Println("Hello, World!")
}

```
运行
```
localhost:go beer$ go run 001.go 
Hello, World!
```

### 语言结构

- 包声明
```
package main 

第一行代码定义了包名。每个 Go 应用程序都包含一个名为 main 的包。
```
- 引入包
```
import "fmt"

引入包
```
- 函数
```
func main() {
   /* 这是我的第一个简单的程序 */
   fmt.Println("Hello, World!")
}
```
- 变量
```
var age int;
```
- 语句&表达式
```

```
- 注释
```
/* 这是我的第一个简单的程序 */

//单行注释
/*多行注释*/
```

### 数据类型

- 布尔型
```
# 只可以是常量 true or false
var b bool = true
```
- 数字类型
```
int
float32
float64
```
- 字符串类型

- 派生类型

```
(a) 指针类型（Pointer）
(b) 数组类型
(c) 结构化类型(struct)
(d) Channel 类型
(e) 函数类型
(f) 切片类型
(g) 接口类型（interface）
(h) Map 类型
```

### 变量声明

#### 单变量
```
package main

var a int
var isSuccess = false
var b = "hello beer!"


func main(){
	println(a,isSuccess,b)
}


# 结果
$ go run 002_var.go 
0 false hello beer!
```

#### 多变量

```
package main

//多变量声明
var x,y int

//因式分解关键字的写法一般用于声明全局变量
var (
	a int
	b bool
)


var c,d int = 1,2
var e,f = 124 ,"hello"

func main(){
	//:= 不带var 的只能在函数体中出现
	g,h := 234,"word"
	println(x,y,a,b,c,d,e,f,g,h)
}


$ go run 003_var_mult.go 
0 0 0 false 1 2 124 hello 234 word
```

### 常量声明

```
# 显示变量赋值
const b string = "abc"

# 隐示变量赋值
const b = "abc"

# 枚举
const (
    Unknown = 0
    Female = 1
    Male = 2
)
```

### 语言运算符

- 算术运算符
```
+ - * / % ++ --
```
- 关系运算符
```
== != > < <= >=
```
- 逻辑运算符
```
&& || ！
```
- 位运算符
```
& | ^ << >>
```
- 赋值运算符
```
= += *= /=  %= <<=  >>= &= ^= |= 
```
- 其他运算符
```
# 返回变量的存储地址
&a:// 给出变量的实际地址

# * 指针变量
*a; 是一个指针变量
```

### 条件语句
- if
```
if a < 20 {
    ...
}
```
- if else
```
if a < 20 {
    ...
}else{
    ...
}
``` 

- if else if 
- switch 

case 后面不需要加 break ，默认匹配到的话就 break.
```
switch marks {
      case 90: grade = "A"
      case 80: grade = "B"
      case 50,60,70 : grade = "C"
      default: grade = "D"  
}
```

- select 

### 循环

- for

第一种：常规
```
for a := 0; a < 10; a++{
    ...
}
```
第二种
```
for  a < b {

}
```
第三种
```
for {

}
```

- 循环控制

break

continue

[goto 不推荐](http://www.runoob.com/go/go-goto-statement.html)


### 函数
http://www.runoob.com/go/go-functions.html
