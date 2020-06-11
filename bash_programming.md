# 数组
arr=(ele1 ele2 ele3) 元素之间用空格分开
随机访问数组${arr[2]}
访问整个数组${arr[*]}（for ele in ${arr[*]}）
unset ${arr[idx]}可以把idx处元素变成空，unset arr则整个数组为空。
http://zengrong.net/post/1518.htm

# 输入重定向
内联输入重定向（inline input redirection）：
command << marker
data
marker
eg:
wc << EOF
test string 1
EOF

# shell的数学运算
* expr命令(http://www.computerhope.com/unix/uexpr.htm)
* bash $[ epxr ], e.g $[ 1 + 5 ]

# exit code
$?打印上一条命令退出的状态码
0 成功，1位置错误，2误用shell命令，126不可执行，127没找到命令，128无效退出参数，128+x linux信号x的错误，130 ctrl+C中止，255 退出状态码越界。

# if-then语句
format:
```
if command
then
  command
elif command
then
  command
else
  command
fi
```

## test命令
test condition，if-then中你可以使用[ condition ]（注意方括号两边的空格）
可用的比较命令：
### 数值比较
-eq -ge -gt -le -lt -ne(not equal)
### 字符串比较
下面的>和<使用的时候必须转义
= != < > -n(字符串长度是否非0) -z(字符串长度是否为0)
### 文件比较
* -d 是否存在并且为目录
* -e 是否存在
* -f 是否存在并且是文件
* -r 是否存在并可读
* -s 是否存在并非空
* -w 是否存在并可写
* -x 是否存在并可执行
* -O 是否存在并属于当前用户
* -G 时候存在并属于当前组
* file1 -nt file2 file1是否比file2更新
* file1 -ot file2 file1是否比file2更旧
## 复合条件
* [ condition1 ] && [ condition2 ]
* [ condition1 ] || [ condition2 ]
## 圆双括号
(( expression ))，用来做高级数学运算。
支持++, --, ~, !, **, <<, >>, &, |, &&, ||
e.g:
```
val1=10
if (( val1 ** 2 > 90))
then
    (( val2 = $val1 ** 2 ))
fi
echo $val2
```
## 方双括号
[[ expression ]]，支持用正则匹配字符串
if [[ $USER == r* ]]
## case
```
case $val in
a | b)
    command1;;
c)
    command2;;
*)
    default_command;;
esac
```

# for 
```
for var in list
do
    command
done
```
## TIPS:
### list append:
list=$list" extra"可以在list后面添加一个元素
### 修改list分隔符：
list元素之间的分隔符是IFS环境变量（internal field separator），它定义了bash用来做字段分隔符的一系列字符。默认情况下是空格、tab和换行。
可以通过修改这个环境变量来达到修改分隔符的目的。这样list就可以采用各种分隔符。
e.g
```
IFS=$'\n'
IFS=$'\n:,;' # 指定多个分隔符
```
## C style for
for (( variable assignment; condition; iteration process ))
e.g
```
for (( i=0; i <10; i++ ))
do
    echo $i
done
```
# while
```
while test command
    test command2
    ...
do
    command
done
```
test command会被依次执行，最后一个命令的退出状态码会用来决定是否结束循环。
# until
```
until test command
    test command2
    ...
do
    command
done
```
和while类似。
# break && continue
break n和continue n表示作用于第n层循环，默认n=1，就是当前循环。

# 脚本输入参数
\#$是脚本命令行参数个数。${!#}（在花括号里不能使用$，需要使用!）
$*把所有参数当作一个变量
$@把所有参数当作一个list，可以遍历。
shift可以把$i+1参数移动到$i。

# 重定向
&>可以同时重定向stderr和stdout。
&1，&2可以把某些输出单独定向到stderr或者stdout。
在脚本内永久重定向可以exec 1/2>xxx，同理exec 0<input.
实际上，3~8的操作符是保留的，你可以exec 3~8>xxx，也可以exec 3~8<xxx
exec n>&-则表示关闭文件操作符。
lsof可以看到文件描述符。

# 临时文件和临时文件夹
man mktemp
mktemp youname.XXXXXX

# 信号
ctrl+c = SIGINT
ctrl+z = SIGSTP
man trap
trap command signal
trap - signal

# 重启已经停止的进程
bg $job_number

# 调整进程优先级
nice/renice

# 函数
## 函数声明：
```
function name {
    commands
}
```
或者
```
name() {
    commands
}
```
老函数声明会被新的覆盖，且不会给出任何提示。
\#?或者上一条命令执行的状态码，同理在函数调用之后，#?可以获得函数返回的状态码。
## 输入参数
func $var1 $var2...
和脚本输入一样，参数放在$1... $n。同理，#$和#@也可以用。
## 函数变量
不在函数中使用全局变量，可以用local var的方法声明成局部变量。
## 数组和函数
如果把数组作为参数传入函数，函数只会取数组的第一个值。
需要用到#@，同理也可以返回一个数组。
```
function test {
    local newarray
    newarray = `echo "$@"`
    echo ${newarray[*]}
}
arr=(1 2 3 4 5)
test ${arr[*]}
```
# source
source会在当前bash的上下文执行命令，而不是新开bash。.和source的作用是一样的。
