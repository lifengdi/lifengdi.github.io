---
layout: post
title: Linux服务器查看日志命令总结
categories: [Linux, Java]
description: 一些常用的Linux服务器查看日志命令
keywords: Linux, 命令
---

## tail
用于输出文件中的尾部内容，实际应用如下：
```bash
// 显示文件倒数2行数据，并实时刷新新日志
tail -2f demo.log   

// 执行效果如下：
line9 56
line0 78

// 如果你需要停止，按Ctrl+C退出
// 假如查看的日志，实时刷新的日志量非常多的话，慎用!
```

## head
跟tail是相反的，tail是看后多少行日志；例子如下：
```bash
// 查询日志文件中的头10行日志;
head -n 10  test.log   

//查询日志文件除了最后10行的其他所有日志;
head -n -10  test.log  
```

## cat
命令用于连接文件并打印到标准输出设备上
```bash
// 显示文件全部内容
cat demo.log 

// 执行结果：
line1 123456 aa
line2 123456 bb
line3 123456 cc
line4 123456 dd
line5 654321 aa
line6 654321 bb
line7 12
line8 34
line9 56
line0 78

// 由于会显示整个文件的内容，所以如果文件大的话，慎用！

// 查询关键字的日志 得到关键日志的行号
cat -n test.log |grep "debug"   

// 选择关键字所在的中间一行
cat -n test.log |tail -n +92|head -n 20 
// 然后查看这个关键字前10行和后10行的日志:
// 表示查询92行之后的日志
tail -n +92
// 20 则表示在前面的查询结果里再查前20条记录
head -n 20 

// 分页打印,通过点击空格键翻页
cat -n test.log |grep "debug" |more 

// 使用 >xxx.txt 将其保存到文件中,到时可以拉下这个文件分析，例如：
cat -n test.log |grep "debug"  >debug.txt
```

## tac
关于`cat`命令，还有一个与之类似但写法相反的命令：`tac`。写法就是`cat`反过来写

功能也是相反的，是从后往前显示内容。示例如下：
```bash
tac demo.log

// 执行结果：
line0 78
line9 56
line8 34
line7 12
line6 654321 bb
line5 654321 aa
line4 123456 dd
line3 123456 cc
line2 123456 bb
line1 123456 aa
```
## more
类似`cat`，不过会以一页一页的形式显示，按空白键`space`就往下一页显示，按`b`键就会往回一页显示
```bash
more demo.log

// 执行结果(文件内容少的话，会直接显示全部，效果跟cat一样)：
line1 123456 aa
line2 123456 bb
line3 123456 cc
--More--(15%)
```

## less
`less`与`more`类似,但使用`less`可以随意浏览文件(使用键盘上的上下箭头),而且`less`在查看之前不会加载整个文件
```bash
less demo.log

// 执行结果(文件内容少的话，会直接显示全部，效果如下)：
line1 123456 aa
line2 123456 bb
line3 123456 cc
line4 123456 dd
line5 654321 aa
line6 654321 bb
line7 12
line8 34
line9 56
line0 78
demo.log (END)
```
当使用`less`命令查看日志时，还可以对关键字进行查找。命令如下：
```bash
// 使用斜杠（/）加关键字的形式，向后查找关键字，如查找关键字：123456
/123456   

// 使用问号（?）加关键字的形式，向前查找关键字，如查找关键字：line3
?line3

// PS：跳转到后一个关键字快捷键: N; 跳转到前一个关键字：shift + N
```

## grep 
`grep`指令用于查找内容包含指定的范本样式的文件，如果发现某文件的内容符合所指定的范本样式，`grep`指令会把含有范本样式的那一列显示出来
```bash
// 查询包含关键字`123456`的日志内容
grep "123456" demo.log

// 执行结果
line1 123456 aa
line2 123456 bb
line3 123456 cc
line4 123456 dd

// 查询包含关键字`123456`且包含`aa`的日志内容
grep "123456" demo.log | grep "aa"

// 执行结果
line1 123456 aa

// 查询不包含`aa`的日志内容
grep -v "aa" demo.log

// 执行结果
line2 123456 bb
line3 123456 cc
line4 123456 dd
line6 654321 bb
line7 12
line8 34
line9 56
line0 78

// 查询包含关键字`123456`但不包含`aa`的日志内容
grep "123456" demo.log | grep -v "aa"

// 执行结果
line2 123456 bb
line3 123456 cc
line4 123456 dd

// 查询包含关键字`123456`或`aa`的日志内容
grep "123456\|aa" demo.log
或者
grep -E "123456|aa" demo.log

// 执行结果
line1 123456 aa
line2 123456 bb
line3 123456 cc
line4 123456 dd
line5 654321 aa
```
当你通过`grep`查找关键字，但是还是有非常多匹配的结果时，可以组合前面的`less`或`more`实现分页显示，示例如下:
```bash
grep "123456" demo.log | less
grep "123456" demo.log | more

grep "123456\|aa" demo.log | less
grep "123456" demo.log | grep -v "aa" | less
```
grep -C 5 foo file 显示file文件里匹配foo字串那行以及上下5行

grep -B 5 foo file 显示foo及前5行

grep -A 5 foo file 显示foo及后5行

