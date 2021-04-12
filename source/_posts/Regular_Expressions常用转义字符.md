title: Regular Expressions常用转义字符
tags:
  - RE
id: 526
categories:
  - 综合
date: 2015-06-07 13:54:17
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/12692258,2560,1600.jpg-thumb1
metaAlignment: center
photos:
comments: true
---
记录RE一些常见的转义字符
<!--more-->
字符简写式

\a 报警符

[\b] 退格字符

\c x 控制字符

\d (= [0-9])数字字符

\D ( = [^0-9]) 非数字字符

\o xxx 字符的八进制值

\w ( = [_a-zA-Z0-9]) 单词字母

\W ( = [^_a-zA-Z0-9])非单词字符

\0 空字符

\x xx 字符的十六进制值

\u xxx 字符的Unicode值



**##匹配空白符**

\f 换页符

\h 水平空白符

\H 非水平空白符

\n 换行符

\r 回车符

\s ( = [ \t\n\r])空白符（匹配空格、制表符、换行符、回车符）

\S 非空白符

\t 水平制表符

\v 垂直制表符

\V 非垂直制表符



正则表达式中的选项

(?d) Unix中的行

(?i) 不区分大小写

(?J) 容许重复的名字

(?m) 多行

(?s) 单行（dotall）

(?u) Unicode

(?U) 默认最短匹配

(?x) 忽略空格和注释
