---
title: cut、sed、awk
tags: linux bash
---

# cut、sed、awk

## 1、cut

### 语法
```
cut  [-bn] [file]
cut [-c] [file]
cut [-df] [file]
```

选项与参数：
- `-d`：定义分隔字符。与 `-f` 一起使用；
- `-f`：依据 `-d` 的分隔字符将一段信息分割成为数段，用 `-f` 取出第几段的意思；
- `-c`：以字符 (characters) 的单位取出固定字符区间；

### 示例
a.log
```
a1:b1:c1:d1
a2:b2:c2:d2
a3:b3:c3:d3
```
例1
```bash
cut -d ':' -f 1,3 a.log     # 取分割出的第一段和第三段
cut -c 1,4 a.log            # 取第一个和第四个字符
```

## 2、sed

- 参考：<https://www.runoob.com/linux/linux-comm-sed.html>
- 参考：<https://blog.51cto.com/12810168/2293936>

### 作用和工作原理
- 作用：处理文件内容（增删改查），学了sed之后可以对较大的文件或者大批量的文件进行高效率的处理。
- 原理：sed读取一行，首先将这行放入缓存，然后才对这行进行处理，处理完后，将缓存区的内容发送到终端，
        其中sed对应的缓存区空间称为：模式空间。


### 语法
```bash
sed [-hnV][-e<script>][-f<script文件>][文本文件]
```

参数说明：
```
-e<script>或--expression=<script> 以选项中指定的script来处理输入的文本文件。 可以通过`-e <script> -e <script>`处理多个命令
-f<script文件>或--file=<script文件> 以选项中指定的script文件来处理输入的文本文件。
-h或--help 显示帮助。
-n或--quiet或--silent 仅显示script处理后的结果；取消默认输出，sed默认会输出所有文本内容，使用-n参数后只显示处理过的行
-V或--version 显示版本信息。
-i操作原始文件（默认不会操作原始文件）
```

动作说明（后面的单引号中的内容）：
```
- a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～ 增
- i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)； 增
- c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！        行改
- d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；                  除
- p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～    查
- s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！    行替换
```

### script详细说明
说明：script部分一般用单引号包裹，分为'位置 动作'，根据动作的不同，动作的参数有所不同，如：动作`d`后没有参数，而`s`则有正则替换

位置说明：位置可以是直接数字，也可以是正则匹配到的行
- `1 p`   打印第一行
- `1,3 p` 打印1到3行
- `2,$ d` 删除第二行及以后的所有行
- `/jone/ s/SEO/XXX` 将jone所在行中的SEO换成XXX

### 示例

ps：复制一份`/etc/passwd`出来做测试

```bash
sed -n '2 p' passwd 
sed '2 i this is insert line.' passwd 
sed '1,10 d' passwd 
sed '1,10 d' passwd 
sed '1,10 d' passwd | nl
sed '1 c this is first' passwd      
sed -e '1,10 d' -e '/^ga/ s/ga/GA/g' passwd | nl
sed -i -e '1,10 d' -e '/^ga/ s/ga/GA/g' passwd
sed 's/^[a-Z]*/{&}/' passwd       # & 作为后向引用，todo：好像正则不支持()的子匹配引用，只能用&代表整体匹配到的值
sed 's/^[a-Z]*/{&}/w /tmp/shell/new' passwd # 将结果写入文件，这是针对`s`正则用法，其他动作可以采用重定向
```
### 模式空间和保持空间
- 参考：<https://www.cnblogs.com/LiuYanYGZ/p/9710432.html>
![](./img/img102.jpg)


## 3、awk
### 语法
说明：**行匹配语句 `awk ''` 只能用单引号**
```
awk [选项参数] '{command}' filename  filename2                           # 多个命令以分号分隔。可以同时操作多个文件
awk 'BEGIN {command1} pattern {command2} END{command3}' filename        # 注意：BEGIN ,END 需要大写
```
- awk 基础用法，参考：<https://www.runoob.com/linux/linux-comm-awk.html>
- awk 工作原理，参考：<https://www.runoob.com/w3cnote/awk-work-principle.html>
- awk 中的5中模式，参考：<http://www.zsythink.net/archives/2025>
    + 1、空模式
    + 2、关系运算模式，   例：`awk 'NR>=2 && NR <=5{print $0}' passwd`
    + 3、BEGIN/END模式
    + 4、正则模式        例：`awk '/^root/ {print $0}' passwd`；例2：`awk '$2 ~ /th/ {print $2,$4}' log.txt`
    + 5、行范围模式       例：`awk '/^root/,/^mysql/ {print $0}' passwd`     # 匹配行范围（包含边界的两行）


### 选项参数说明：
```
-F fs or --field-separator fs
指定输入文件折分隔符，fs是一个字符串或者是一个正则表达式，如`-F :`，注意：这里`:`没有包裹。
-v var=value or --asign var=value
赋值一个用户定义变量。
-f scripfile or --file scriptfile
从脚本文件中读取awk命令。
-mf nnn and -mr nnn
对nnn值设置内在限制，-mf选项限制分配给nnn的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。
-W compact or --compat, -W traditional or --traditional
在兼容模式下运行awk。所以gawk的行为和标准的awk完全一样，所有的awk扩展都被忽略。
-W copyleft or --copyleft, -W copyright or --copyright
打印简短的版权信息。
-W help or --help, -W usage or --usage
打印全部awk选项和每个选项的简短说明。
-W lint or --lint
打印不能向传统unix平台移植的结构的警告。
-W lint-old or --lint-old
打印关于不能向传统unix平台移植的结构的警告。
-W posix
打开兼容模式。但有以下限制，不识别：/x、函数关键字、func、换码序列以及当fs是一个空格时，将新行作为一个域分隔符；操作符**和**=不能代替^和^=；fflush无效。
-W re-interval or --re-inerval
允许间隔正则表达式的使用，参考(grep中的Posix字符类)，如括号表达式[[:alpha:]]。
-W source program-text or --source program-text
使用program-text作为源代码，可与-f命令混用。
-W version or --version
打印bug报告信息的版本。
```

### 运算符
```
运算符	                        描述
= += -= *= /= %= ^= **=	        赋值
?:	                            C条件表达式
||	                            逻辑或
&&	                            逻辑与
~ 和 !~	                        匹配正则表达式和不匹配正则表达式
< <= > >= != ==	                关系运算符
空格	                            连接
+ -	                            加，减
* / %	                        乘，除与求余
+ - !	                        一元加，减和逻辑非
^ ***	                        求幂
++ --	                        增加或减少，作为前缀或后缀
$	                            字段引用
in	                            数组成员
```

### 内建变量
```
变量	                            描述
$n	                            当前记录的第n个字段，字段间由FS分隔
$0	                            完整的输入记录
ARGC	                        命令行参数的数目
ARGIND	                        命令行中当前文件的位置(从0开始算)
ARGV	                        包含命令行参数的数组
CONVFMT	                        数字转换格式(默认值为%.6g)ENVIRON环境变量关联数组
ERRNO	                        最后一个系统错误的描述
FIELDWIDTHS	                    字段宽度列表(用空格键分隔)
FILENAME	                    当前文件名
FNR	                            各文件分别计数的行号
FS	                            字段分隔符(默认是任何空格)
IGNORECASE	                    如果为真，则进行忽略大小写的匹配
NF	                            一条记录的字段的数目
NR	                            已经读出的记录数，就是行号，从1开始
OFMT	                        数字的输出格式(默认值是%.6g)
OFS	                            输出记录分隔符（输出换行符），输出时用指定的符号代替换行符
ORS	                            输出记录分隔符(默认值是一个换行符)
RLENGTH	                        由match函数所匹配的字符串的长度
RS	                            记录分隔符(默认是一个换行符)
RSTART	                        由match函数所匹配的字符串的第一个位置
SUBSEP	                        数组下标分隔符(默认值是/034)
```

### 示例
log.txt文本内容如下：
```
2 this is a test
3 Do you like awk
This's a test
10 There are orange,apple,mongo
```

例1
```bash
$ awk 'BEGIN{printf "%4s %4s %4s %4s %4s %4s %4s %4s %4s\n","FILENAME","ARGC","FNR","FS","NF","NR","OFS","ORS","RS";printf "---------------------------------------------\n"} {printf "%4s %4s %4s %4s %4s %4s %4s %4s %4s\n",FILENAME,ARGC,FNR,FS,NF,NR,OFS,ORS,RS}'  log.txt
```
结果
```
FILENAME ARGC  FNR   FS   NF   NR  OFS  ORS   RS
---------------------------------------------
log.txt    2    1         5    1
log.txt    2    2         5    2
log.txt    2    3         3    3
log.txt    2    4         4    4
```

例2
```bash
$ awk -F\' 'BEGIN{printf "%4s %4s %4s %4s %4s %4s %4s %4s %4s\n","FILENAME","ARGC","FNR","FS","NF","NR","OFS","ORS","RS";printf "---------------------------------------------\n"} {printf "%4s %4s %4s %4s %4s %4s %4s %4s %4s\n",FILENAME,ARGC,FNR,FS,NF,NR,OFS,ORS,RS}'  log.txt
```
结果
```
FILENAME ARGC  FNR   FS   NF   NR  OFS  ORS   RS
---------------------------------------------
log.txt    2    1    '    1    1
log.txt    2    2    '    1    2
log.txt    2    3    '    2    3
log.txt    2    4    '    1    4
```

例3
```bash
# 输出顺序号 NR, 匹配文本行号
$ awk '{print NR,FNR,$1,$2,$3}' log.txt
```
结果
```
1 1 2 this is
2 2 3 Are you
3 3 This's a test
4 4 10 There are
```

例4
```bash
# 指定输出分割符
$  awk '{print $1,$2,$5}' OFS=" $ "  log.txt
```
结果
```
2 $ this $ test
3 $ Are $ awk
This's $ a $
10 $ There $
```

例5

score.txt内容如下
```
Marry   2143 78 84 77
Jack    2321 66 78 45
Tom     2122 48 77 71
Mike    2537 87 97 95
Bob     2415 40 57 62
```

```bash
#!/bin/awk -f
#运行前
BEGIN {
    math = 0
    english = 0
    computer = 0
 
    printf "NAME    NO.   MATH  ENGLISH  COMPUTER   TOTAL\n"
    printf "---------------------------------------------\n"
}
#运行中
{
    math+=$3
    english+=$4
    computer+=$5
    printf "%-6s %-6s %4d %8d %8d %8d\n", $1, $2, $3,$4,$5, $3+$4+$5    # %-6s与%6s，前者为左对齐，后者为右对齐，记忆：左对齐就左边加个杠
}
#运行后
END {
    printf "---------------------------------------------\n"
    printf "  TOTAL:%10d %8d %8d \n", math, english, computer
    printf "AVERAGE:%10.2f %8.2f %8.2f\n", math/NR, english/NR, computer/NR
}
```
备注：使用`awk -f cal.awk score.txt`

### awk条件语句与循环
- 参考：<https://www.runoob.com/w3cnote/awk-if-loop.html>
备注：循环的语法没有`{}`哦

### awk数组
- 参考：<https://www.runoob.com/w3cnote/awk-arrays.html>


### 实践
#### 测试mysql是否启动
```bash
#!/bin/bash
# printf "%d",$2 最好格式化输出数据类型，不然后期不好处理
port=$(netstat -ptlun | awk -F ":::" '/mysql/ { printf "%d",$2}')
# 都转化为字符串来比较，字符串外再包裹双引号，可能不能对等哦，如将上面的 %d改为%s
if [ "$port" == "3306" ]; then
    echo "mysql is running."
else
    echo "mysql is died."
fi
```

#### 杀掉redis的进程
```bash
#!/bin/bash

# 停止redis服务
# 注意prinf "\d "这里有空格哦；
# awk -F "[ /]+"设置多个分隔符，+加号表示连续的多个分隔符视为一个
pids=($(netstat -ptlun | awk -F "[ /]+"  '/redis-server/ {printf "%d ",$7}'))
for p in ${pids[@]}
do
        kill $p
done
```
