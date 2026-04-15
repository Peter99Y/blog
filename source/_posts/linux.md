---
title: Linux
---

## IP

- IP

  每台联网的电脑都会有一个ip地址用于和其他计算机进行通信；
  127.0.0.1 是本机ip地址；
  0.0.0.0 可用于指代本机；也可在端口用来绑定关系；在一些情况下可允许任意ip访问；

- 主机名
  除了ip地址，每台电脑都有一个名字，称为主机名；
  `hostname` 显示主机名；

- 域名解析
  IP地址不便记忆，可通过主机名或字符串地址去代替数字化的IP地址，比如 www.baidu.com；
  访问 www.baidu.com 时，操作系统首先会去本地的hosts文件中查找字符化的主机名与ip的映射关系 (windows是C:/Windows/System32/drivers/etc/hosts, linux是/etc/hosts)，若没有找到，则联网去DNS服务器查询，还是没有就是 404；

  `ifconfig` 显示网卡信息

  ens33：CentOS 7 默认网卡名称
  ens33 > inet：网卡ip地址

- 固定ip
  虚拟机每次重启设备后都会获取一次新的ip，所以需要固定ip (步骤省略，自行搜索)；

## concept

- Linux 系统最大权限账户是 root；

- Linux 只有一个**根目录** '/', 所有文件都存放在根目录下；
  如 '/usr/local/hello.txt' 起始斜杠 '/' 表示根目录，后面的斜杠就是目录层级关系；

- Linux 系统的命令行终端，在启动的时候，默认会加载当前用户的 home 目录 '~' 作为**工作目录**；
- 每个用户的个人账户目录路径：`/home/username`;

### shortcut

| key              | 功能                                     | 栗子 |
| :--------------- | :--------------------------------------- | :--- |
| history          | 显示历史命令                             |      |
| ! + 首字母       | 在历史命令中，执行匹配上的最近的一条命令 | !ls  |
| ctrl + d         | 退出当前登录用户                         |      |
| ctrl + l / clear | 清屏                                     |      |

## sudo

普通用户一般只有 home 目录内操作的权限，以及其他地方只读权限；
使这条命令**临时赋予管理员权限**去执行一些命令，

- 配置 sudo 认证
  1. 先切换到 root 账号，
  2. 执行 `visudo` 命令后会自动通过 vim 编辑器打开 /etc/sudoers (或通过 `vim /etc/sudoers`)；
  3. 文件最后添加 username All = (ALL) NOPASSWD: ALL；
  4. 保存后切换普通用户运行 sudo 命令即可；
- `sudo mkdir /test.txt`
  如在根目录中创建一个目录/文件 (需临时赋予 root 权限才能创建)

## common

- `whoami`
  显示当前用户名
- `su - root` (switch user)
  \'-\' 表示加载环境变量；若没跟用户名默认切换到 root 用户

- `exit / ctrl d`
  回退到上一个用户；

- `pwd`
  打印当前所处的工作目录 (print working directory);

- `cd` (change directory)
  `cd /` 切换到根目录；
  `cd` or `cd ~` 切换到home目录；

- `ls`
  -a: 显示所有文件，包括隐藏文件；
  -l: 以列表的形式显示文件 (默认平铺方式)；
  -al / -l -a: 显示所有文件，并显示列表形式；
  -lh: 显示文件大小，并展示单位 (默认单位的就是KB; h需和l一起使用)；
  parameter: 参数是 /home/admin 路径；

- mkdir
  `mkdir test` 创建一个名为 test 的目录；
  `mkdir -p father/son` 创建多层级的目录；

- touch
  `touch text.txt` 创建一个名为 test.txt 的文件；
  `cat text.txt` 查看文件全部内容；
  `more text.txt` 查看文件内容，若文件内容过多，通过键盘**空格**分页展示，通过键盘q退出；

- cp
  -r：递归复制；
  `cp -r test test2` 复制目录 test 到 test2，
  `cp text.txt text2.txt` 复制文件；

- mv
  移动文件/移动目录/改名
  `mv text2.txt ./text3.txt` 若 text3.txt 不存在，则改名为 text3.txt；
  `mv test2 test` 将 test2 目录移动到 test 目录下；
  `mv test2 ..` 将 test2 目录移动上个目录；

- rm
  -f：强制删除 (使用 -f 直接删除不会出现确认提示)
  -r：删除目录及目录下的所有文件
  `rm text.txt demo.txt` 删除文件(可跟多个文件)；
  `rm -r test` 删除目录及目录下的所有文件；
  `rm tes*` tes\* 匹配任何以test开头的文件，\*test 匹配任何以test结尾的文件，\*test\* 匹配任何包含test的文件；

## root

Linux中权限管控分为针对用户权限，针对用户组权限；
某个文件可以控制用户的权限，也可以控制用户组权限；

创建用户组需要root权限；

- `groupadd groupname`
  创建用户组
- `groupdel groupname`
  删除用户组
- `getent group`
  (get entries 获取条目的缩写)
  显示所有用户组

---

- `useradd [-g -d] username`
  -g：指定用户组(前提是必须存在用户组)，不加 -g 选项默认会创建同名用户组并自动加入；
  -d：创建用户时指定用户目录，默认是 /home/username；
  `useradd -g testGroup test1 -d /home/test111`
  创建用户 test1，并加入 testGroup 用户组，并指定用户目录为 /home/test111，既可切换到 test1 用户；
- `userdel [-r] username`
  -r 删除用户 /home/username 目录，否则保留 (test1用户需先退出登录)；

---

- `usermod -aG groupname username`
  将指定的用户加入指定的用户组

- `id [username]`

- `groups [username]`
  显示用户所属哪些用户组

## find

find 起始路径 -name "keyword"

`find ~ -name "text3"` 查找目录/文件，按关键字从 home 目录开始
`find ./ -name "te*"` 查找任何以 te 开头的目录/文件，从当前目录开始

## tail

显示文件最后几行

- `tail [-f -10] 文件路径`
  -f 表示持续跟踪；
  -10 表示查看尾部显示的行数，默认10行；
- `tail -10 hello.txt`
- `tail -f hello.txt`
  持续跟踪文件 hello.txt, 另外一个终端对 hello.txt 修改就可以实时查看；

## date

Linux 默认时区是非中国的东八区，需切换到root用户修改为东八区时区；
`rm -f /etc/localtime`
`sudo ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`

- `date` 查看日期和时间； (2026年 04月 08日 星期三 23:00:29 PDT)
  -d 按照给的字符串显示日期，一般用于日期计算

  `date "+%Y-%m-%d %H:%M:%S"` 根据格式查看时间 (2026-04-08 23:03:12)
  `date -d "+1 day"` 显示明天的日期
  `date -d "+1 month"` 显示下个月的日期

- 扩展：通过ntp自动联网校准系统时间
  `yum -y install ntp`
  `systemctl start ntpd` ntp安装完后会自动注册为系统服务，此时就能通过systemctl命令启动服务了；
  `systemctl enable ntpd` 设置为开机自启动；

## grep

筛选文件中的内容

- `grep [-n] "keyword" 文件路径`
  -n 显示行号

- `grep -n "hello" test/text.txt`
  搜索 hello 关键字从 text.txt 文件中

- `cat text.txt | grep -n "hello"`
  若使用管道符，grep 筛选关键字 'hello', 从左侧命令的结果作为 grep 的输入数据 (从text.txt 文件中搜索 hello 关键字)

- `ls | grep "text"`
  搜索 text 关键字 从 ls 查询出的所有文件数据中 (查询出目录/文件)

## wc

(Word Count) 统计内容数量

- `wc [-l -w -c -m] 文件路径`
  -l：统计行数
  -w：统计空格/换行所隔开的单词数量
  -c：统计bytes字节数量 (字节数和字符数可能相同)
  -m：统计字符数量

- `wc text.txt`
  4 (总行数) 14 (单词数量) 72 (字节数) text.txt (文件名)

- `cat text.txt | wc -l -w`
  4 (总行数) 14 (单词数量) ， 统计行数与数量从管道中读取

- `ls ./test | wc -l`
  统计文件数量从test目录中

- `cat text.txt | grep "hello" | wc -l`
  统计行数从 grep 筛选关键字 'hello' 的结果

## echo

- `echo "hello world"`
  等价于 console.log("hello world")
- ``echo `pwd` ``
  打印命令执行的结果，而不是打印字符串

- `echo "hello world" > text.txt`
  使用重定向符将 "hello world" 覆盖 text.txt 文件内容

- `echo "hello world" >> text.txt`
  使用重定向符将 "hello world" 追加到 text.txt 文件中

- `cat "text.txt" > text2.txt `
  使用重定向符将 text.txt 文件所有内容覆盖到 text2.txt 文件

- `cat "text.txt" >> text2.txt `
  使用重定向符 将 text.txt 文件的内容追加到 text2.txt 文件

## vim

Linux 的文本编辑器

`vim 文件路径`，进入文本编辑的命令模式界面，通过编辑 text.txt 文件 (若文件不存在会创建该文件)；

| 当前模式 | 命令      | 功能                     |
| :------- | :-------- | ------------------------ |
| 命令     | `/`       | 搜索                     |
| 命令     | `n`       | 向下搜索                 |
| 命令     | `N`       | 向上搜索                 |
| -        | -         |
| 命令     | yy        | 复制当前行               |
| 命令     | p         | 粘贴                     |
| 命令     | dd        | 删除当前行               |
| -        | -         |
| 命令     | u         | 撤销修改                 |
| 命令     | ctrl+r    | 重做修改                 |
| -        | -         |
| 命令     | gg        | 光标跳到开头             |
| 命令     | G         | 光标跳到末尾             |
| -        | -         |
| 命令     | `i`       | 从命令模式切换到插入模式 |
| 插入     | `Esc`     | 从插入模式切换到命令模式 |
| 命令     | `:`       | 从命令模式切换到底线模式 |
| -        | -         |
| 底线     | `:set nu` | 显示行号                 |
| 底线     | `:q`      | 退出                     |
| 底线     | `:q!`     | 强制退出 (不保存)        |
| 底线     | `:w`      | 保存                     |
| 底线     | `:wq`     | 保存并退出               |

## yum

Linux 支持的安装软件方式有 yum 为 CentOs 安装软件，apt 为 Ubuntu 安装；
yum 命令需要 root 权限, 可通过 sudo 方式 或 su root 切换到 root 用户；
yum -y install wget 需要先安装 wget，再通过 wget 安装其他软件；

- `yum [-y] [search | install  | remove | update] 软件名称`
  -y 表示自动安装，无需手动确认安装/卸载过程;
  yum 安装的服务所有用户都共享;

## systemctl

Linux 的服务管理工具，可查看、启动、停止、重启服务；

- `systemctl [status | start | stop | restart | enable | disable] 服务名称`
  查看/启动/停止/重启/开机自启动/开机不自启动；

- Linux 内置服务
  firewalld 防火墙服务
  NetworkManager 主网络服务
  network 副网络服务
  sshd/ssh 登录服务 (FinalShell远程登录使用的就是这个服务)

- 外置服务，部分第三方服务有注册 如：
  httpd 服务
  nginx 服务
  mysql 数据库服务
  ntp 时间同步服务

## network

- `ping [-c count] ip/domain`
  ping 命令用于测试指定的网络服务器是否联通状态；

- `wget [-b] url`
  (Web Get 从网络中获取文件) 类似于 windows 的迅雷下载文件一样；
  -b 命令表示后台运行；
  wget 命令用于下载网络文件，并把日志文件保存当前工作目录的 wget-log 文件中；

- `curl [-O] url`
  (See URL) 发送http请求，可获取 html 内容，也可用于下载文件；
  -O 仅限下载文件，当url是下载链接时，此项可保存文件；

## port

计算机之间通信，IP地址只能代表一台电脑，通过端口就能锁定这台电脑某个具体程序；
公认端口 1 - 1023之间，通常是系统内置预留使用，如22是ssh远程链接服务端口，80是 http 端口，443是 https 服务的端口；
注册端口 1024 - 49151之间，通常用于自定义绑定程序、服务使用；
动态端口 49152 - 65535之间，通常用于临时使用，不会固定绑定程序，当程序对外网络请求时临时使用；

- `sudo yum -y install nmap`
  `nmap 127.0.0.1`
  nmap 命令用于查看本机计算机有哪些端口正在运行状态

## process

程序运行在操作系统中，每一个程序在运行时，便被操作系统注册为一个进程，并为每一个进程分配一个进程PID；

`ps [-ef]`
-e 表示显示所有进程；
-f 显示进程的详细信息；

`kill [-9] PID`
-9 强制结束进程；

## environment

使用一系列的命令本质上就是一个个可执行的程序，无论当前处工作目录在哪里都能执行这些命令，比如 cd 命令本体就是 '/usr/bin/cd' 程序，ls命令本体就是 '/bin/ls' 程序;
这些命令就是通过环境变量中 PATH 的值来执行的，PATH存储了所有可执行程序的目录；

- `env`
  查看所有环境变量；

- `env | grep PATH`
  PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/admin/.local/bin:/home/admin/bin
  值里面有多个目录，多个目录之间用冒号分隔；
  比如执行cd时，会先从'/usr/local/bin'目录中查找，再从'/usr/bin'目录中查找... 直到找到该命令，最后找不到就会报错；

- `echo $PATH`
  通过 $ + 环境变量的key，可以获取这个环境变量的值，并通过echo命令输出到屏幕上；

- 设置环境变量
  1. 普通用户通过vim 打开 `vim ~/.bashrc` 文件 (所有用户 `vim /etc/profile`)；
  2. 文件最后添加 export MYNAME=xxx；
  3. 保存退出 并执行 `source ~/.bashrc`使文件中环境变量立即生效 (所有用户 `source /etc/profile`)；
  4. 通过 `echo $MYNAME` 获取到环境变量的值；

## download & upload

方式1：使用 FinalShell 直接在目录中右键上传和下载即可 (推荐)；
方式2：lrzsz；

- `yum -y install lrzsz`
  lrzsz 命令用于上传下载文件;
- `sz 文件路径`
  下载文件；
- `rz`
  上传文件，会弹出一个窗口，选择要上传的文件；

## compression & decompression

Linux和 Mac常见2中压缩格式，后缀分为.tar 和 .gz；
.tar 称为barball归档文件，简单的讲文件组装到一个 .tar 文件中，并没有太多文件体积的减少，仅仅是简单的封装；
.gz 也常见为 .tar.gz，gzip格式压缩文件，使用gzip压缩算法将文件压缩到一个文件内，极大地减少了文件体积；

- `tar [-c -v -x -f -z -C] 参数1 ... 参数n`
  -z 使用gzip压缩/解压算法进行压缩/解压，不使用就是普通的tarball格式压缩文件 (一般处于第一位)；

  -c 创建压缩文件； -x 解压压缩文件；
  -v 显示压缩进度；
  -f 压缩/解压文件名 (必须处于最后一位)；

  -C 将压缩文件解压到指定目录下；

- `tar -cvf test.tar text1.txt text2.txt`
  将 text1.txt 和 text2.txt 两个文件压缩到 test.tar 文件中；

- `tar -zcvf test.tar.gz text1.txt text2.txt`
  将 text1.txt 和 text2.txt 压缩到 test.tar.gz 文件中，并使用gzip压缩算法；

---

- `tar -xvf test.tar`
  解压 test.tar 文件，并解压到当前目录下 (如工作目录处于 /home/admin/test 中, 解压 test.tar 内的文件会覆盖掉 test 目录下的同名所有文件)；

- `tar -zxvf test.tar.gz`
  解压 test.tar.gz 文件，并解压到 /home/admin/compression 目录下，并使用gzip算法；

- `tar -xvf test.tar -C /compression`
  解压 test.tar 文件，并解压到 /home/admin/test/compression 目录下 (需先mkdir compression目录)；

---

- `zip [-r] test.zip 参数1 ... 参数n`
  -r 如果选择的**有文件夹**需要带上-r参数；

- `zip -r test.zip text1.txt test.tar`
  将 text1.txt文件、test.tar文件夹 压缩到 test.zip 文件中；

---

- `unzip [-d] 参数`
  -d 指定解压目录

- `unzip test.zip -d /compression`
  将test.zip 文件解压到当前目录下 (如解压 test.zip 内的文件与当前工作目录相同文件会有确认提示) (不用先创建compression目录)；

## MySql

需 root 权限；

- `wget https://dev.mysql.com/get/mysql80-community-release-el7-11.noarch.rpm`
- `rpm -ivh mysql80-community-release-el7-11.noarch.rpm`
- `yum install mysql-community-server`
  安装完成

---

- `systemctl start mysqld`
- `systemctl enable mysqld`
- `systemctl status mysqld`
  启动并设为开机自启

---

- `grep 'temporary password' /var/log/mysqld.log`
  通过日志查看初始密码

- `mysql -u root -p`
  登录 mysql，粘贴上面拿到的初始密码；

- `ALTER USER 'root'@'localhost' IDENTIFIED BY 'Study333!';`
  在mysql内修改 mysql 登录密码 (需有大写字母、小写字母、数字、特殊字符、不能123连续字符)

- `exit`

- `mysql -u root -p `
  使用新密码重新登录

---

默认MySql只允许本机连接访问，可以修改配置允许远程访问 (正式环境不推荐)

- `grant all privileges on *.* to 'root'@'%' identified by 'Study333!';`
- or `UPDATE mysql.user SET Host='%' WHERE User='root';`
  %代表允许所有ip访问；
- `FLUSH PRIVILEGES;`
  刷新权限立即生效；

---

- `netstat -antp | grep 3306`
- or `nmap 127.0.0.1`
  mysql默认绑定了3306端口，可以通过端口检测查看mysql是否启动；
