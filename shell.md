# Shell编程



### Shell基础

- 使用该命令查询系统中可用的Shell 

  *cat /etc/Shells*

- 脚本的开头需要声明使用的shell程序

  *#!/bin/sh*

- 执行脚本

  1. 赋予脚本可执行权限，通过 **./** 执行。

     *chmod +x HelloWorld.sh*

     *./HelloWorld.sh*

  2. 直接通过bash命令执行。

     *bash HelloWorld.sh*

- 脚本排错

  1. 通过sleep命令延迟脚本执行，观察echo输出。

  2. 通过**-x**参数观察脚本执行输出。

     *bash -x HelloWorld.sh*




## 变量

- 局部变量

  在同一个Shell中，可以通过local命令显式声明局部变量，仅限于函数内使用。

- 全局变量

  在Shell中创建的变量默认都是当前Shell中的全局变量。

- 环境变量

  通过export命令导出的变量称为环境变量，子Shell可以访问该环境变量。

- 特殊变量

  1. 位置参数
    
    假设一个脚本在运行时可以接受参数，那么从左到右第一个参数被记作\$1，第二个参数为\$2，以此类推，第n个参数为\$N。所有参数记作\$@或\$\*，参数的总个数记作\$\#，而脚本本身记作\$0。
    
  2. 脚本或命令返回值：**\$?**
  
     \$?永远是上一个命令的返回值
  
- 常量

  声明后不可修改

  ```shell
  readonly value=1
  declare -r value=1
  ```

  

#### 数组

- 数组声明

  ```bash
  # 普通声明
  declare -a array
  # 声明的同时赋值
  declare -a names=('john' 'hei')
  # 声明的同时赋值2
  names=('john' 'hei')
  # 只针对其中一些元素赋值
  array=([3]=3 [5]=5 [7]=7)
  ```

- 数组取值

  ```bash
  # 获取单个值
  ${array[0]}
  # 获取以空格隔开的所有值
  ${array[@]} or ${array[*]}
  ```

- 数组长度

  ```bash
  # 用#来获取元素个数
  ${#array[@]}
  ```

- 数组截取

  ```bash
  # 取出数组的第一索引开始的三个元素
  ${Array[@]:1:3}
  ```

- 连接数组

  ```shell
  Conn=(${Array[@]} ${Name[@]})
  echo ${Conn[@]}
  ```



#### 命令替换

命令替换是指将命令的标准输出作为值赋给某个变量。以下两种方式等价，但是*$()*方式支持嵌套。

```shell
DATE_01=`date`
DATE_02=$(date)
```



## 算术运算

Shell只支持整数计算，也就是所有可能产生小数的运算都会舍去小数部分。

1. 使用let整数运算

   ```bash
   let num=2*2
   ```

2. 使用$[]做运算

   ```bash
   echo $[1+1]
   ```

3. 使用declare

   ```bash
   declare -i num
   num=1+1
   ```

4. 算术扩展 *$((算术表达式))*

   若表达式中的变量没有定义，则在计算时，其值会被假设为0

   ```bash
   #例子：计算2*i+1的值
   i=2
   echo $((2*i+1)) #注意这里变量i前并没有$符
   echo $((2*(i+1))) #用括号改变运算优先级
   
   #例子：将2*i+1的值赋值给变量var
   var=$((2*i+1))
   ```

5. 浮点运算可使用bc



## 特殊字符

#### 通配符

\*号：匹配任意个字符，不包括点号和斜线号。

?号：匹配任意单个字符。

[ ]方括号：匹配括号内其中一个字符，可以使用-号表示连续字符，如[a-z]。

{}大括号：匹配大括号内其中一个字符。

如：file_{a,b}，匹配file_a或者file_b；



#### 引号

双引号：除了**\$ ,\\ ,`**三个符号保留功能，其余当做普通字符处理。

单引号：全部字符当做普通字符处理。



#### 大括号

*${#str}* 返回字符串长度

*${#array[@]}* 返回数组长度

*${str:start}* 返回从start索引开始的字符

*${str:start:length}* 返回从start索引开始的length个字符



## 测试

#### 语法

```bash
# 使用test命令
test expression
# 使用方括号
[expression]
# 如果方括号内有空格，则需要对称存在
[ expression ]
```



#### 文件测试

##### 语法

```bash
#文件测试方法一
test file_operator FILE
#文件测试方法二
[ file_operator FILE ]

#测试一个文件是否存在
test -e /var/log/messages
[ -e /var/log/messages ]
```



##### Oerator

| operator | 描述                           |
| -------- | ------------------------------ |
| -e       | 存在文件 \|\| 存在目录，返回真 |
| -d       | 存在 && 目录，返回真           |
| -f       | 存在 && 普通文件，返回真       |
| -x       | 存在 && 可执行文件，返回真     |



#### 字符串测试

##### 语法

同上

##### Operator

| operator    | 描述         |
| ----------- | ------------ |
| -z "string" | 为空，返回真 |
| -n "string" | 非空，返回真 |
| =、!=、>、< |              |



#### 整数比较

##### 语法

同上

##### Operator

| operator | 描述             |
| -------- | ---------------- |
| -ep      | 相等，返回真     |
| -gt      | 大于，返回真     |
| -lt      | 小于，返回真     |
| -ge      | 大于等于，返回真 |
| -le      | 小于等于，返回真 |
| -ne      | 不等于，返回真   |



#### 逻辑测试

##### 语法

```bash
# 逻辑非的使用
[ ! -e /var/log/messages ]
# 逻辑与的使用
[ -e /var/log/messages -a -e /var/log/messages01 ]
```

##### Operator

| operator | 描述 |
| -------- | ---- |
| !        | 非   |
| -a       | 与   |
| -o       | 或   |



##### 逻辑运算符语法

```bash
! [ -e /var/log/messages ]

[ -e /var/log/messages ] && [ -e /var/log/messages01 ]
```

##### Operator

| operator | 描述 |
| -------- | ---- |
| !        | 非   |
| &&       | 与   |
| \|\|       | 或   |



## 判断

#### if语法

```bash
# if else
if expression; then
  command1
else
  command2
  ...
fi

# if elseif
if expression; then
  command1
elif expression; then
  command2
else
  ...
fi
```



#### case语法

```bash
case VAR in
var1) command1 ;;
var2) command2 ;;
var3) command3 ;;
...
*) command ;; # default
esac
```

case判断语句（var1、var2...）只能是常量或者正则表达式。



## 循环

#### for循环

```bash
# for循环语法
for VARIABLE in list
do
command
done

# list的形式1
for FRUIT in apple orange banana pear

# list的形式2
fruits="apple orange banana pear"
for FRUIT in ${fruits}

#list的形式3
fruits=('apple' 'orange' 'banana' 'pear')
for FRUIT in ${fruits[@]}

#list也可以接收一个命令的输出
for VAR in $(ls)

```

#### 类C的for循环

```bash
# 语法
for ((expression1; expression2; expression3))
do
	command
done

# demo
for ((i=1; i<=10; i++))
do
	command
done
```



#### while循环

```bash
# 语法
while expression
do
	command
done

# demo
CONTER=5
while [[ $CONTER -gt 0 ]]
do
	let "CONTER-=1"
done
echo
```



#### Select循环

```bash
# 语法
select MENU in (list)
do
	command
done

# demo
echo "Which car do you prefer?"
select CAR in Benz Audi VolksWagen
do
	break
done
```



## 函数

#### 普通函数

```bash
# 语法
function FN(){
	command
}

# 调用函数
FN
```

函数的返回值一般正确返回0，错误返回非0。



#### 带参数的函数

函数的参数通过位置参数获取，即\$1、\$2...

```bash
# demo
function test(){
  echo hello $1
}
test 'zhu'
```



#### 函数库

通过source命令可以引入其他shell脚本，直接使用里面的函数方法。

```bash
# 引入外部shell脚本
source ./func.sh
# 使用func.sh里面定义好的function
_somefunc
```





## 重定向

系统在启动一个进程的同时会为该进程打开三个文件：标准输入（stdin）、标准输出（stdout）、标准错误输出（stderr），分别用文件标识符0、1、2来标识。



#### I/O重定向符号

| 符号 | 描述                                               |
| ---- | -------------------------------------------------- |
| \>   | 标准输出覆盖重定向                                 |
| \>\> | 标准输出追加重定向                                 |
| \>&  | 标识输出重定向                                     |
| <    | 标准输入重定向                                     |
| \|   | 管道：从一个命令中读取输出并且作为另一个命令的输入 |

#### 标准输出覆盖重定向

使用标准输出覆盖重定向符号可以将原本输出到显示器上的内容重定向到一个文件中，且覆盖原文件内容。

```bash
ls -l > ls.txt

# 将标准错误输出到文件中
ls -l somefile 2> ls.txt
```



#### 标准输入追加重定向

原理与前者一样，区别是追加内容，非覆盖。

```bash
ls -l >> ls.txt
```



#### 标准输出重定向

标识输出重定向的作用是将一个标识的输出重定向到另一个标识的输入。比如想要将标准输出和标准错误同时定向到同一个文件中。

```bash
COMMAND > stdout_stderr.txt 2>&1
```

上面演示的命令从左到右可以读为：执行COMMAND命令，将标准输出的内容重定向到stdout_stderr.txt中，如果有标准错误输出也同时重定向到该文件中。

可以利用系统中的一个特殊设备/dev/null，将所有错误输出重定向到该设备中——系统会将任何输入到该设备的内容全部删除（就像一个宇宙黑洞）。



#### 标准输入重定向

标准输入重定向可以将原本应由从标准输入设备中读取的内容转由文件内容输入，也就是将文件内容写入标准输入中。

```bash
command < file.txt
```



#### 管道

管道就是将一个命令的输出作为另一个命令的输入。





## 常用命令

- alias

  在当前shell环境中定义别名，**alias lal="ls -al"**。如果需要定义永久别名，在**.bashrc**中修改。

- **"."**

  执行某个脚本，脚本没有运行权限也可以执行。

  **. ./script.sh**

- 任务前后台切换：bg、fg、jobs

  该命令用于将任务放置后台运行，一般会与Ctrl+z、fg、&符号联合使用。典型的使用场景是运行比较耗时的任务。比如打包某个占用较大空间的目录，若在前台执行，在任务完成前将会一直占用当前的终端，而导致无法执行其他任务，此时就应该将这类任务放置后台。

  ```
  [root@localhost ~]# tar -zcf usr.tgz /usr
  tar: Removing leading &#x60;/' from member names  #开始打包
          #占用前台导致无法运行其他任务，此处用Ctrl+z组合键暂停前台任务
  [1]+  Stopped                 tar -zcf usr.tgz /usr
  [root@localhost ~]# jobs  #查看暂停的任务，刚刚的tar任务编号为1
  [1]+  Stopped                 tar -zcf usr.tgz /usr
  [root@localhost ~]# bg 1  #把tar任务放置后台继续运行
  [1]+ tar -zcf usr.tgz /usr &   #tar任务继续运行了
  [root@localhost ~]# fg 1  #使用fg把后台任务调至前台运行
  tar -zcf usr.tgz /usr
  #如果预知某个任务耗时很久，可以一开始就将命令放入后台运行
  [root@localhost ~]# tar -zcf usr.tgz /usr &
  ```

- shift

  向左移动脚本的参数

- read

  从标准输入中读取一行到变量

- local

  声明局部变量

- pwd

  打印当前工作目录

- let

  整数运算命令

  *let K=2+5*

- export

  在父Shell中创建的局部变量不能被其子Shell获取，export命令可以将变量导出，使得子Shell可以*读取*该变量。

- exit

  退出当前脚本，其后可以接一个状态值，默认是0。

- echo

  echo用于打印字符，若要打印斜杠转义字符，需要加上**-e**参数。

  *echo -e "hello \n world"*

- cd

  改变当前目录。如果不加参数，默认会进入当前用户目录。