---
title: Go语言中包的使用
date: 2022-03-20 23:09:57
tags: go
---

# Go语言中包的使用

Go语言中使用package这种语法元素来组织源码，所有语法可见性均定义在package这个级别

## 1.main包

go语言的入口main()函数所在的包叫main，main包想引用别的代码需要import导入

## 2.package

src目录是以代码包的形式组织并保存Go源码文件的。每个代码包都和src目录下的文件夹一一对应。每个子目录都是一个代码包

代码包包名和文件目录名不要求一致，但是同一个目录下的源码文件第一行声明所属的包必须一致

包需要满足：

1. 一个目录下的同级文件**归属一个包**
2. 在同一个包下面的package名建议设为该目录名，但不强制
3. 包名为main的包为应用程序的入口包，其他包不能使用。

包可以嵌套定义

包中函数通过标识符首字母大写来判断是否为public

## 3.import

要引用其他包，可以使用import关键字，可以单个导入或者批量导入。

### 通常导入

```go
//单个导入
import "package"
//批量导入
import (
	"package1"
	"package2"
)
```

### 点操作导入包

```go
import(
	. "fmt"
)
```

这个点操作的含义就是这个包导入之后在你调用这个包的函数时，你可以**省略前缀的包名**。如`fmt.Println("hello world")`可以省略写成`Println("hello world")`


### 起别名

别名操作顾名思义我们可以把包命名成另一个我们用起来容易记忆的名字。导入时，可以为包定义别名

```go
import(
	p1 "package1"
	p2 "package2"
)
//使用时就可以使用别名调用函数
p1.Method()
```


### _操作

如果仅仅需要导入包时执行初始化操作，并不需要使用包内的其他函数，常量等资源。则可以在导入包时，**匿名导入**。  
这个操作经常是让很多人费解的一个操作符。

```go
import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
)
```

_操作其实是引入该包，而不直接使用包里面的函数，调用了该包里**init函数**，也就是说，使用_作为该包的别名，仅会执行init();


## 5.init()包初始化

下面我们详细的来介绍一下这两个函数: init()、main()是go语言中的保留函数。我们可以在源码中，定义init(函数。此函数会在包被导入时执行，例如如果是在main 中导入包，包中存在 init()，那么init()中的代码会在main()函数执行前执行，用于初始化包所需要的特定资料。例如∶

包源码

```go
package utils

import "fmt"

func Utils()  {
	fmt.Println("我是uitls包下的Utils函数")
}
func init(){
	fmt.Println("uitls包的init()执行了");
}

```

main函数

```go
package main

import "firstgolang/utils"

func main() {
	utils.Utils()
}
```

执行结果

```go
uitls包的init()执行了
我是uitls包下的Utils函数
```

### init函数和main函数的对比

**相同点：**

* 两个函数在定义时不能有任何的**参数和返回值**
* 函数只能由go程序自动调用，不能人为引用。

**不同点**：

* init可以应用于多个包中，而且可以重复定义多个
* main函数只能用于main包中，且只能定义一个

**两个函数的执行顺序**
