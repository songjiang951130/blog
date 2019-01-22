---
title: 字符串和切片
date: 2018-12-26 10:29:03
# updated_at: 2000-12-19 21:52:40
categories:
- go
tags:
- string
- slice
- 切片
---

 在 go 中字符串属于不可变类型，对字符串的修改会报错

 ```golang
 str :="hello world"
 str[0] = "A"
 //输出 cannot assign to str[0]
 ```
 所以所有对字符串的修改必须转化为切片（slice）, 对切片进行修改，再转为字符串

以下为示例代码
```golang
//对英文字符串，直接可以转为字节切片
byteArray := []byte(s)
//中文得转为 rune 类型的切片
runeArray := []rune(s)
```
 