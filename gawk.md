# gawk基本使用
gawk options program file
* gawk -F '{commond1;command2}'
* gawk -F -f script_file text
script_file:
```
{
      command1
            command2
}
```
# gawk的内建变量
* FIELDWIDTHS 空格分开，确切指定了每一个字段的宽度
* FS 输入字段分隔符（-F）
* RS 输入数据行分隔符
* OFS 输出字段分隔符
* ORS 输出行分隔符
* FNR 当前数据文件数据行数量
* NR 累计处理的数据行数量
* NF 当年数据行中字段总数

默认RS和ORS都是换行符

# 自定义变量
* var='test'
* var=4

## 在命令行给变量赋值
gawk -f script n=2 text
变量n的取值就是2
如果要在BEGIN块让命令行赋值生效，需要加上-v参数，在-f script之前

# 数组
arr[index]=value
index可以是字符串，也可以是数字

## 遍历数组
for (var in array) { command }
var是index

## 删除元素
delete arr[index]

# 正则匹配
## 行匹配
* gawk '/regex/{command}'
## 字段匹配
* gaw '$x ~ /regex/{command}'
* gaw '$x !~ /regex/{command}'

# if
* if (condition) command; else command
* 多行if
```
if (condition) {
      command1
            command2
} else {
      command3
            command4
}
```

# while
```
while (condition) {
      command...
}
```

# do while
和C一样

# for
和C一样

# 定义函数
```
function name([vars]) {
      statsments
}
```
最好放在BEGIN之前。

# 内建函数
数学和字符串两类，参考man page（Numeric Functions和String Functions）

