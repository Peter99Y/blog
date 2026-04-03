---
title: Linux
---

## 目录结构

Linux 只有一个根目录 '/', 所有文件都存放在根目录下；
`/usr/local/hello.txt` 起始斜杠'/'表示根目录，后面的斜杠就是层级关系；

### HOME

Linux 系统的命令行终端，在启动的时候，默认会加载当前用户的HOME目录 ('~') **作为工作目录**；
每个用户的个人账户目录路径：`/home/username`;

## 命令行格式

`command [-options] [parameter]`

command: 命令本身，-options: 可选的命令选项 (控制命令的行为细节)，parameter: 可选的命令参数 (多用于命令的指向目标)

`ls -l /usr/local`：ls是命令，-l是选项，/usr/local是参数，以列表的形式显示/usr/local目录下的内容；
`cp -r test1 test2`: cp是命令，-r是选项，test1和test2是参数，将test1目录下的内容复制到test2目录下；

## common

- `pwd` 打印当前所处的工作目录 (print working directory);

- cd (change directory)
  `cd /` 切换到根目录；
  `cd` / `cd ~` 切换到home目录；

- ls
  -a: 显示所有文件，包括隐藏文件；
  -l: 以列表的形式显示文件 (默认平铺方式)；
  -al / -l -a: 显示所有文件，并显示列表形式；
  -lh: 显示文件大小，并展示单位 (默认单位的就是KB; h需和l一起使用)；
  parameter: 参数是 /home/admin 路径；

- mkdir (创建目录)
  `mkdir test` 创建一个名为test的目录；
  `mkdir -p father/son` 创建多层级的目录；

- touch (创建文件)
  `touch text.txt` 创建一个名为test.txt的文件；
  `cat text.txt` 查看文件全部内容；
  `more text.txt` 查看文件内容，若文件内容过多，通过键盘**空格**分页展示，通过键盘q退出；

- cp (复制)
  `cp -r test test2` 复制目录 test 到 test2，-r表示递归复制；
  `cp text.txt text2.txt` 复制文件；

- mv (移动)
  移动文件/目录
  `mv text2.txt ./text3.txt` 若 text3.txt 不存在，则改名为text3.txt；
  `mv test2 test` 将test2目录移动到test目录下；
  `mv test2 ..` 将test2目录移动上个目录；

- rm (删除)
  -f 表示强制删除 (root用户下不能删除文件，使用-f直接删除就不会出现询问的提示)
  -r 删除目录及目录下的所有文件
  `rm text3.txt textN.txt` 删除哪些文件；
  `rm -r test` 删除目录及目录下的所有文件；
  tes\* 匹配任何以test开头的文件，\*test 匹配任何以test结尾的文件，\*test\* 匹配任何包含test的文件；

## find

find 起始路径 -name "文件名"

`find ~ -name "text3"` 按文件名查找文件从home目录开始
`find ./ -name "text3*"` 匹配任何以 text3 开始的文件从当前目录开始
`find ./ -name "text3*"` 匹配任何以 text3 结尾的文件从当前目录开始

## grep

筛选文件中的内容

grep [-n] "keyword" 文件路径

-n 显示行号

`grep -n "hello" test/text.txt` 搜索 hello 关键字从 text.txt 文件中
`cat text.txt | grep -n "hello"` 若使用管道符 '|'，grep 筛选关键字 'hello', 从左侧命令的结果作为 grep 的输入数据 (从text.txt 文件中搜索 hello 关键字)
`ls | grep "text"` 搜索 text 关键字 从 ls 查询出的所有文件数据中 (查询出目录、文件)

## wc

统计内容数量

wc [-l -m -c -w] 文件路径
-l 统计行数
-w 统计空格所隔开的单词数量
-c 统计bytes字节数量 (字节数和字符数可能相同)
-m 统计字符数量

`wc text.txt`
4 (总行数) 14 (空格所隔开的单词数量) 72 (bytes字节数) text.txt (文件名)

`cat text.txt | wc -l -w` 统计行数与数量从管道中读取
4 (总行数) 14 (空格所隔开的单词数量)

`ls ./test | wc -l` 统计文件数量从test目录中

`cat text.txt | grep "hello" | wc -l` 统计行数从 grep 筛选关键字 'hello' 的结果中

## echo

`echo "hello world"` 等价于 console.log("hello world")
``echo `pwd` `` 打印命令执行的结果，而不是打印字符串

`echo "hello world" > text.txt` 将 "hello world" 写入 text.txt 文件

`cat "text.txt" > text2.txt ` 将 text.txt 文件的内容复制到 text2.txt 文件

`cat "text.txt" >> text2.txt ` 将 text.txt 文件的内容追加到 text2.txt 文件

## vim

是 Linux 的文本编辑器
