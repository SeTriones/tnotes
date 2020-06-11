sed是一个流式编辑器，每次从stdin或者输入文件读取一行，根据命令替换修改数据，并且输出到stdout，直到处理完所有的输入行。

# sed的基本使用：
sed options script file
options包括：
- -e script。比如sed -e 's/red/black/; s/small/big/'，有多个命令的时候，命令之间用;分开。命令末尾和;之间不能有空格
- -f file。可以在file中添加多个sed命令，一行一条命令。

## sed替换的模式
s/pattern/replacement/flags
flags包括：
- [1-9]+： 替换第几次匹配的地方
- g：替换所有出现的地方
- p：替换行的内容需要打印到stdout（可以结合sed -n，sed -n 's/a/b/p' test，这样就把test中被替换的行打印到了stdout）
- w file：将替换结果写入文件

如果pattern或者replacement中含有/，可以通过\/转义；或者用其他字符做分隔符：sed 's!/bin/bash!/bin/csh!' /etc/passwd

## sed的套路
sed首先需要确定作用于文本的哪些行，无论是上面的替换还是下面要讲的删除/插入。确定作用行的基本格式是[address]command。如果需要在一行执行多个命令，格式则是[address]{command; command}，例如：sed '2{s/fox/bird/; s/cow/sheep/}' test
### 数字方式寻址
数字方式中指定的地址可以是单个数字行号，起始行号、逗号以及结尾行号指定的一定范围的行。例如：2s/a/b/，3,5s/a/b/，10,$/s/a/b
### 文本方式过滤
地址通过pattern来指定，就是/pattern/command。这里的pattern可以采用正则。

##  sed的其他命令
### 删除
删除是d。前面说的[address]d，可以指定删除的范围。此外，d还有一个特性，可以用两个pattern来指定删除的范围。
sed '/1/,/3/d' test，就是从匹配有1的行删除到匹配有3的行。
如果有匹配1的行，但是找不到匹配有3的行，就会从匹配1的行开始删除直到结束。
### 插入
格式是sed '[address][i/a][\newline]+'
- i，在指定行之前插入一个新行。
- a，在指定行之后插入一个新行。

### 修改
sed '[patter]c\new line'
例如: sed '2c\test line' text或者sed '/old lines/c\new line' txt
需要注意的是，如果是sed '2,3c\new line' txt，会把第二行和第三行变成new line，而不是分别替换第二行和第三行。
### 替换
sed '[address]y/oldchars/newchars/'
oldchars和newchars的字符数必须一致。oldchars中的字符会被替换成newchars中对应位置的字符。
### 打印行
sed '[address]p'，配合-n参数，可以只打印被替换之后的行。
如果同时还需要原始的行，可以这样sed '/pattern/p; s/pattern/new content/p' text
### 打印行号
sed '[address]='
### 打印不可见符号
sed '[address]l'
### 写入文件
w
### 读取文件内容并插入数据流
sed '[address]r data' text

# sed高级用法
## sed的空间
sed有两个空间，模式空间（pattern space），模式空间是一块缓冲区，保存了当年要处理的文本；保持空间（hold space），可以通过保持临时保持一些文本数据。
## n命令
n让sed移动到下一行
例如：
```
[root@iZ250bl4iyxZ tmp]# cat test.txt 
first line
test lines
test lines
test lines
[root@iZ250bl4iyxZ tmp]# sed '/first/{n; s/test/my/g}' test.txt 
first line
my lines
test lines
test lines
```
## N命令
N命令会把下一行也推入模式空间中（下一次命令再执行的时候，从下下一行开始读取。）。这时候需要注意的是，在模式空间的多行中，它们之间的换行符依旧存在。模式空间不包括它内部最后一行的换行符。
例如
```
[root@iZ250bl4iyxZ tmp]# cat test.txt 
first line
test lines
test lines
test lines
[root@iZ250bl4iyxZ tmp]# sed '/first/{N; s/line/movie/g}' test.txt   
first movie
test movies
test lines
test lines
```
需要注意的是，如果d命令的pattern匹配上了模式空间中的任意一行，那么整个模式空间的所有行将被删除。sed提供了D命令，只删除模式空间的第一行。同理，p命令打印整个模式空间，P命令只打印模式空间的第一行。
D命令有一个特殊的特性：D      Delete up to the first embedded newline in the pattern space.  Start next cycle, but  skip  reading  from the input if there is still data in the pattern space. 意味着如果配合N使用，甚至可以把整个文本放到模式空间，持续做多行匹配。
## 保持空间的使用
* h 把模式空间复制到保持空间
* H 把模式空间附加到保持空间
* g 把保持空间复制到模式空间
* G 把保持空间附加到模式空间
* x 交换保持空间和模式空间

## ！命令
!命令作用于其他命令之前，让这些命令起到相反的作用。
例如：!p表示不打印匹配模式的行，打印其他不匹配的行。$!N则表示对于最后一行不再append下一行到模式空间。
## branch
[address]b label
在address行跳过后面的命令，直接跳转到label处。如果label为空，则跳转到脚本末尾。
```
[root@iZ250bl4iyxZ tmp]# cat test.txt 
this is panda.
that is house.
this is plane.
that is ship.
[root@iZ250bl4iyxZ tmp]# sed '/this/b lb; s/is/is not/g; :lb; s/\./!/g' test.txt 
this is panda!
that is not house!
this is plane!
that is not ship!
```
## test命令
t（test）命令和b命令类似，不过它跳转是基于上一条命令是否成功匹配（执行）。
## 模式替换
### &
&符号可以取代被匹配的pattern
```
[root@iZ250bl4iyxZ tmp]# echo "this is a cat" | sed 's/cat/"&"/g'
this is a "cat"
```
### 分组模式替换
用\(\)包起来的，就是一个group。group用\1，\2来表示。
```
[root@iZ250bl4iyxZ tmp]# cat test.txt 
this is a cute cat.
this is a cute mouse.
[root@iZ250bl4iyxZ tmp]# sed 's/cute \(.*\)/\1/g' test.txt 
this is a cat.
this is a mouse.
```


