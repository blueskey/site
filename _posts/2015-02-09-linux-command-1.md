---
layout: post
title:  "linux 命令（一）"
date:   2015-02-09 20:55:59
categories: linux command 
---

#### cmp

用来比较两个文件是否有差异，可以用于比较二进制文件等（diff通常用于文本文件的比较）。

当相互比较的两个文件完全一样时，则该指令不会显示任何信息。若发现有差异，预设会标示出第一个不通之处的字符和列数编号。

-c或--print-chars：除了标明差异处的十进制字码之外，一并显示该字符所对应字符；

-b或—print-bytes ： 除了标明差异处的十进制字码之外，一并显示该字符所对应字节；

-i<字符数目>或--ignore-initial=<字符数目>：指定一个数目； 

-l或——verbose：标示出所有不一样的地方； 

-s或--quiet或——silent：不显示错误信息； 

-v或——version：显示版本信息； 

--help：在线帮助。

    实例：

1、比较两个文件是否相同

[apache@vm4 tmp]$ cmp as.png asc.png

as.png asc.png differ: char 19, line 3

2、显示全部不同

[apache@vm4 ju]$ cmp -l a.bak b.bak 

2 141 142

3 141 142

4 141 142

5 12 167

3、要比较两个文件，而不写任何消息。 如果文件相同，则给出值 0，如果不同，则给出值 1， 或者如果发生错误，则给出值 2

[apache@vm4 ju]$ cmp -s a.bak b.bak 

#### cat 
- - -

cat [-AbeEnstTuv] [--help] [--version] fileName 

说明：把档案串连接后传到基本输出（萤幕或加 > fileName 到另一个档案，>>fileName追加到文件中） 

cat主要有三大功能：

1.一次显示整个文件，2.从键盘创建一个文件，3.将几个文件合并为一个文件。

参数： 

-n 或 --number 由 1 开始对所有输出的行数编号 

-e 每行结束处显示$

-t —show-tabs 每个tab显示为^I

-b 或 --number-nonblank 和 -n 相似，只不过对于空白行不编号 

-s 或 --squeeze-blank 当遇到有连续两行以上的空白行，就代换为一行的空白行 

    实例：

1、cat >cat.txt #从键盘创建新的文件

我 来测试cat命令的。。cat>cat.txt #输入的内容

测试完毕

^C #crtl+c退出

2、cat -n textfile1 > textfile2 #把 textfile1 的档案内容加上行号后输入 textfile2 这个档案里 

3、cat -b textfile1 textfile2 >> textfile3 #把 textfile1 和 textfile2 的内容加上行号（空白行不加）之后将内容附加到 textfile3 里。 

4、cat -n textfile1 > textfile2 #把 textfile1 内容加上行号后将内容写到textfile2中

5、cat -b textfile1 textfile2 >> textfile3 #把 textfile1 和 textfile2 的内容加上行号（空白行不加）之后将内容附加到 textfile3 里。 

6、cat /dev/null > textfile3 #清空textfile3文件内容

#### sort
- - -

$ sort --help 

用法：sort [选项]... [文件]...

串联排序所有指定文件并将结果写到标准输出。

参数：

-b, --ignore-leading-blanks 忽略前导的空白区域

-d, --dictionary-order 只考虑空白区域和字母字符

-f, --ignore-case 忽略字母大小写

-g, --general-numeric-sort 按照常规数值排序

-i, --ignore-nonprinting 只排序可打印字符

-h, --human-numeric-sort 使用易读性数字(例如： 2K 1G)

-n, --numeric-sort 根据字符串数值比较

-r, --reverse 逆序输出排序结果

-k, --key=位置1[,位置2] 以哪个区间值排序

-c, --check, --check=diagnose-first 检查输入是否已排序，若已有序则不进行操作

-t, --field-separator=分隔符 使用指定的分隔符代替非空格到空格的转换

    实例：

1、$ cat /etc/passwd |sort

sort 是默认以第一个数据来排序，而且默认是以字符串形式来排序,所以由字母 a 开始升序排序。

2、$ cat /etc/passwd |sort -t ':' -k 3 

默认是以字符串来排序的，如果想要使用数字排序：

3、$ cat /etc/passwd |sort -t ':' -k 3n

默认是升序排序，如果要倒序排序

4、$ cat /etc/passwd |sort -t ':' -k 3nr

5、如果要对/etc/passwd,先以第六个域的第2个字符到第4个字符进行正向排序，再基于第一个域进行反向排序

$ cat /etc/passwd |sort -t ':' -k 6.2,6.4 -k 1r

6、查看/etc/passwd有多少个shell:对/etc/passwd的第七个域进行排序，然后去重:

$ cat /etc/passwd | sort -t':' -k 7 -u

#### uniq
- - -

用法：uniq [选项]... [文件] 

从输入文件或者标准输入中筛选相邻的匹配行并写入到输出文件或标准输出。通常与sort 一起用。

-c, --count 在每行前加上表示相应行目出现次数的前缀编号

-d, --repeated 只输出重复的行

-D, --all-repeated[=delimit-method 显示所有重复的行

delimit-method={none(default),prepend,separate}

以空行为界限

-f, --skip-fields=N 比较时跳过前N 列

-i, --ignore-case 在比较的时候不区分大小写

-s, --skip-chars=N 比较时跳过前N 个字符

-u, --unique 只显示唯一的行

-z, --zero-terminated 使用'\0'作为行结束符，而不是新换行

-w, --check-chars=N 对每行第N 个字符以后的内容不作对照

若域中为先空字符(通常包括空格以及制表符)，然后非空字符，域中字符前的空字符将被跳过。

提示：uniq 不会检查重复的行，除非它们是相邻的行。

    实例：

文件：uniq.txt;

apples green

apples red

peaches green

pears yellow

bananas yellow

cherries blue

apples green

apples yellow

bananas yellow 

bananas yellow 

1、 要删除名为 fruit 文件中的重复行并将其保存到一个名为 newfruit 的文件中(相同行不相邻，则不会被删除）

[apache@vm4 ju]$ uniq uniq.txt u.txt

2、显示不重复的行

[apache@vm4 ju]$ uniq -u uniq.txt

3、显示真正不重复的行

[apache@vm4 ju]$ sort -k 1 uniq.txt |uniq -u 

apples red

apples yellow

peaches green

pears yellow

3、显示真正重复的行

[apache@vm4 ju]$ sort -k 1 uniq.txt |uniq -d

apples green

bananas yellow

cherries blue

4、列出重复行及次数

[apache@vm4 ju]$ sort -k 1 uniq.txt |uniq -cd

2 apples green

3 bananas yellow

2 cherries blue

#### tail 
- - -

tail 用于输入文件中的尾部内容。可跟多个文件。tail命令默认在屏幕上显示指定文件的末尾10行。如果给定的文件不止一个，则在显示的每个文件前面加一个文件名标题。如果没有指定文件或者文件名为“-”，则读取标准输入。 注意：如果表示字节或行数的N值之前有一个”+”号，则从文件开头的第N项开始显示，而不是显示文件的最后N项。 

-c或——bytes=：输出文件尾部的N（N为整数）个字节内容； 

-f或；--follow：显示文件最新追加的内容。“name”表示以文件名的方式监视文件的变化。“-f”与“-fdescriptor”等效； 

-F：与选项“-follow=name”和“--retry"连用时功能相同； 

-n或——line=： 输出文件的尾部N（N位数字）行内容。 

-q或——quiet或——silent：当有多个文件参数时，不输出各个文件名； 

-s<秒数>或——sleep-interal=<秒数>：与“-f”选项连用，指定监视文件变化时间隔的秒数； 

-v或——verbose：当有多个文件参数时，总是输出各个文件名； 

    实例：

$ tail /opt/360hqb/canvas/logs/canvas/erro.log #默认显示最后10行

$ tail +20 /opt/360hqb/canvas/logs/canvas/erro.log #显示文件内容，从第20行至文件末尾

$ tail -c 10 /opt/360hqb/canvas/logs/canvas/erro.log #显示文件的最后10个字符

$ tail -f /opt/360hqb/canvas/logs/canvas/runtime.log 

$ tail -f -s 2 /opt/360hqb/canvas/logs/canvas/runtime.log 

#### touch
- - -

文件处理,主要有两个功能：一是用于把已存在文件的时间标签更新为系统当前的时间（默认方式），它们的数据将原封不动地保留下来；二是用来创建新的空文件。

-a：或--time=atime或--time=access或--time=use 只更改存取时间；

-c：或--no-create 不建立任何文件；

-d：<时间日期> 使用指定的日期时间，而非现在的时间；

-f：此参数将忽略不予处理，仅负责解决BSD版本touch指令的兼容性问题；

-m：或--time=mtime或--time=modify 只更该变动时间；

-r：<参考文件或目录> 把指定文件或目录的日期时间，统统设成和参考文件或目录的日期时间相同；

-t：<日期时间> 使用指定的日期时间，而非现在的时间；

    实例：

<table> 1、/var/root/ju # touch test #新建一个名为test的新文件 /var/root/ju # ls -l total 0 -rw-r--r-- 1 root root 0 Oct 25 14:06 test   
<table> 2、/var/root/ju # touch -a test #注意时间有变化 /var/root/ju # ls -l total 0 -rw-r--r-- 1 root root 0 Oct 25 14:07 test </table> 3、/var/root/ju # touch -d '2014-10-13 10:39:22' test   
4、/var/root/ju # touch -t ‘201410131039.22’ test #修改时间戳  
  
  
  
#### file  
---
命令用来探测给定文件的类型。     -b 不输出文件名 (简要模式).  
-c 检查时打印输出幻数文件的解析结果.常与 -m 一起使用，用来在安装幻数文件之前调试它.  
-f <名称文件>：指定名称文件，其内容有一个或多个文件名称时，让file依序辨识这些文件，格式为每列一个文件名称.  
    指定包含幻数的文件列表.可以是单个文件，也可以是 用冒号分开的多个文件.  
-n 每检查完一个文件就强制刷新标准输出. 仅在检查一组文件时才有效. 一般在将文件类型输出到管道时才采用此选项.  
-v 打印程序版本并退出.  
-z  试图查看压缩文件内部信息.  
-L：直接显示符号连接所指向的文件类别
    

    实例：

1、file *

Applications: directory

a: UTF-8 Unicode text

2、file -z goodscenter.zip

goodscenter.zip: empty (Zip archive data, at least v1.0 to extract)

3、file -b install.log #不显示文件名称 UTF-8 Unicode text

4、file /var/tmp/

/var/tmp/: sticky directory 

#### mkdir
---

命令用来创建目录. 如果在目录名的前面没有加任何路径名，则在当前目录下创建由dirname指定的目录；如果给出了一个已经存在的路径，将会在该目录下创建一个指定的目录。在创建目录时，应保证新建的目录与它所在目录下的文件没有重名。

-Z：设置安全上下文，当使用SELinux时有效；

-m<目标属性>或--mode<目标属性>建立目录的同时设置目录的权限；

-p或--parents 若所要建立目录的上层目录目前尚未建立，则会一并建立上层目录；

    实例：

1、mkdir -m 700 /tmp/test 

2、 mkdir -p a/b/c/d

rmdir

删除空目录. 子目录被删除之前应该是空目录。就是说，该目录中的所有文件必须用rm命令全部，另外，当前工作目录必须在被删除目录之上，不能是被删除目录本身，也不能是被删除目录的子目录。 -p或--parents：删除指定目录后，若该目录的上层目录已变成空目录，则将其一并删除；

实例：

rmdir -p a/b/c/d

fmt

- - -

用法：fmt [-宽度] [选项]... [文件]... 

重新格式化文件中每个段落并输出到标准输出。

选项：

-c --crown-margin 保持前两行的缩进

-p, --prefix=字符串 只对以指定字符串开头的行重新格式化，

将前缀重新附着到被重新格式化的行上

-s, --split-only 分割过长的行，但不自动补足

-t, --tagged-paragraph 缩进首行以使其不同于第二行

-u, --uniform-spacing 每两个单词间保留一个空格，每句之后保留两个空格

-w, --width=宽度 最大行宽(默认为75 列宽度)

    实例：

1、 $ fmt -w 10 dal-digest.log

2、 $ fmt -s dal-digest.log

3、 $ fmt -t dal-digest.log

#### mv
---

用来对文件或目录重新命名，或者将文件从一个目录移到另一个目录中。 

mv命令可以用来将源文件移至一个目标文件中，或 将一组文件移至一个目标目录中。

源文件被移至目标文件有两种不同的结果：

1、 如果目标文件是到某一目录文件的路径，源文件会被移到此目录下，且文件名不变。 如果目标文件不是目录文件，则源文件名（只能有一个）会变为此目标文件名，并覆盖己存在的同名文件。如果源文件和目标文件在同一个目录下，mv的作用就是 改文件名。

2、当目标文件是目录文件时，源文件或目录参数可以有多个，则所有的源文件都会被移至目标文件中。所有移到该目录下的文件都将保留以前的文件名。

选项：

-b：当文件存在时，覆盖前，为其创建一个备份； 

-f：若目标文件或目录与现有的文件或目录重复，则直接覆盖现有的文件或目录； 

-i：交互式操作，覆盖前先行询问用户，如果源文件与目标文件或目标目录中的文件同名，则询问用户是否覆盖目标文件。用户输入”y”，表示将覆盖目标文 件；输入”n”，表示取消对源文件的移动。这样可以避免误将文件覆盖。

-S<后缀>：为备份文件指定后缀，而不使用默认的后缀；

-u：当源文件比目标文件新或者目标文件不存在时，才执行移动操作。

    实例：

1、mv -i o3.txt o2.txt

overwrite o2.txt? (y/n [n])

2、mv /usr/men/* .

今天来个超简单的Linux命令：

#### clear
---

用于清除当前屏幕终端上的任何信息。

直接输入clear命令当前终端上的任何信息就可被清除。等同快捷键ctrl+l。

#### rename
---

个性文件名称，用字符串替换的方式批量改变文件名。

参数: 

原字符串：将文件名需要替换的字符串；

目标字符串：将文件名中含有的原字符替换成目标字符串；

文件：指定要改变文件名的文件列表。

? 可替代单个字符

* 可替代多个字符[charset] 可替代charset集中的任意单个字符

    实例：

1、将main1.c重命名为main.c

rename main1.c main.c main1.c

rename支持正则表达式

2、rename "s/AA/aa/" * #把文件名中的AA替换成aa

3、rename "s//.html//.php/" * #把.html 后缀的改成 .php后缀 

#### less
---

 作用与more十分相似，都可以用来浏览文字档案的内容，不同的是less命令允许用户向前或向后浏览文件，而more命令只能向前浏览。用less命令显示文件时，用PageUp键向上翻页，用PageDown键向下翻页。要退出less程序，应按Q键。 -e：文件内容显示完毕后，自动退出；

-f：强制显示文件；

-g：不加亮显示搜索到的所有关键词，仅显示当前显示的关键字，以提高显示速度；

-l：搜索时忽略大小写的差异；

-N：每一行行首显示行号；

-s：将连续多个空行压缩成一行显示；

-S：在单行显示较长的内容，而不换行显示；

-x<数字>：将TAB字符显示为指定个数的空格字符

/字符串：向下搜索“字符串”的功能

?字符串：向上搜索“字符串”的功能

n：重复前一个搜索（与 / 或 ? 有关）

N：反向重复前一个搜索（与 / 或 ? 有关）

b 向后翻一页

d 向后翻半页

h 显示帮助界面

Q 退出less 命令

u 向前滚动半页

y 向前滚动一行

    实例：

1、$ less canvas-default.log runtime.log dal-digest.log

 输入 ：n或p可切换

2、$ history |less

#### rev 
---

非常简单，它会一行接一行地颠倒所输入的字符串。"rev"命令的名称来源于英文reverse。可以在命令指示符中输入"rev"然后按回车。按ctrl+c可退出回到命令提示符。

    实例：

1、$ rev

我是反的

的反是我

2、$ rev rev.txt

反有没我

反有没也我

补充：cat、tac与rev

cat是从第一行显示到最后一行，而tac是从最后一行显示到第一行，而rev 则是从最后一个字符显示到第一个字符

#### ping
---

用来测试主机之间网络的连通性。执行ping指令会使用ICMP传输协议，发出要求回应的信息，若远端主机的网络功能没有问题，就会回应该信息，因而得知该主机运作正常.

-d：使用Socket的SO_DEBUG功能；

-c<完成次数>：设置完成要求回应的次数；

-f：极限检测；（ 仅仅root用户可以使用这个选项）

-i<间隔秒数>：指定收发信息的间隔时间；

-I<网络界面>：使用指定的网络界面送出数据包；

-l<前置载入>：设置在送出要求信息之前，先行发出的数据包；

-n：只输出数值；

-p<范本样式>：设置填满数据包的范本样式；

-q：不显示指令执行过程，开头和结尾的相关信息除外；

-r：忽略普通的Routing Table，直接将数据包送到远端主机上； -R：记录路由过程；

-s<数据包大小>：设置数据包的大小；

-t<存活数值>：设置存活数值TTL的大小；

-v：详细显示指令的执行过程。

#### sed
---
sed是一种流编辑器，它是文本处理中非常中的工具，能够完美的配合正则表达式使用，功能不同凡响。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出。Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。




