# Linux常用命令
## 一、目录结构

```bash
/  根目录
├─ bin    存放二进制可执行文件
├─ boot   存放系统引导文件
├─ dev    存放硬件设备文件
├─ etc    存放系统配置文件（/etc/profile配置全局环境变量）
├─ home   普通用户家目录存放位置，每个用户对应独立子目录
├─ lib    存放程序共享库、内核模块
├─ sbin   仅root可执行的二进制命令文件
├─ usr    存放系统共享资源
├─ mnt    临时设备挂载点
├─ tmp    存放系统临时文件
└─ var    存放运行中会变动的数据文件
```

`cd`：切换目录

`cd ../`：切换到上一层目录

`cd /`：man切换到系统根目录

`cd ~`：切换到用户主目录

`cd -`：切换到上一个所在目录

## 二、目录的操作命令

### （1）增加目录操作

`mkdir`：创建一个目录

### （2）查找

`find`：查找目录

**用法：**find 目录 参数

**示例：**查找/root下的与test相关的目录  **find / -name ‘test*’**

### （3）修改或移动目录/文件

`mv`：move的缩写，用于修改或移动目录

**用法：**mv 目录名称 新目录名称**（用于给目录或文件改名）**

​		  mv 目录名称 目录的新位置**（用于移动目录或文件的位置）**

### （4）拷贝

`cp`：copy的缩写，用于复制者目录

**用法：**cp -r 目录名称 目录拷贝的目标位置 **-r代表递归的拷贝**

**示例：**将**/usr**下的**newTest**拷贝到根目录下的**test**中，使用 **cp -r /usr/newTest /test**

### （5）删除

`rm`：remove的缩写

**用法：**rm [-rf] 目录

​	`-r`：递归地删除

​	`-f`：不询问用户

​	`-rf`：不询问用户且递归的删除

## 三、文件操作

### （1）文件的创建

`touch`：创建文件

### （2）文件的查看

`cat`：使用***cat***查看/etc/sudo.conf文件，只能显示最后一屏内容

`more`：使用more查看/etc/sudo.conf文件，可以显示百分比，回车可以向下一行，    空格可以向下一页，q可以退出查看

`less`：使用less查看/etc/sudo.conf文件，可以使用键盘上的PgUp和PgDn向上 和向下翻页，q结束查看

`tail`：使用tail -10 查看/etc/sudo.conf文件的后10行，Ctrl+C结束

**注意**：命令 tail -f 文件 可以对某个文件进行动态监控，例如tomcat的日志文件，    会随着程序的运行，日志会变化，可以使用tail -f catalina-2016-11-11.log 监控文件的变化

### （3）修改文件

`vim`：vim 文件名

**示例：**编辑/test下的aaa.txt文件，使用vim aaa.txt

> **vim** 文件 -> 进入文件 -> 命令模式 -> 按 **i** 进入编辑模式 -> 编辑文件 -> 按**Esc**回到命令模式 -> 输入 **:wq/q!** 底行模式
>
> /pattern → 搜索 **pattern** 的字符串（如果搜索出多个匹配，可按 **n** 键到下一个）

### （4）删除文件

`rm`：同删除目录，可使用 **rm -f 文件名** 删除文件

`-f`：不询问用户是否删除

## 四、压缩文件操作命令

### （1）压缩

一般情况下打包和压缩是一起进行的，打包并压缩后的文件的后缀名一般**.tar.gz**。

`tar -zcvf 打包压缩后的文件名 要打包压缩的文件`

​	`-z`：调用gzip压缩命令进行压缩

​	`-c`：打包文件

​	`-v`：显示运行过程

​	`-f`：指定文件名

**示例：**打包并压缩/test下的所有文件 压缩后的压缩包指定名称为xxx.tar.gz

tar -zcvf xxx.tar.gz aaa.txt bbb.txt ccc.txt

或：tar -zcvf xxx.tar.gz /test/*

### （2）解压

`tar [-xvf] 压缩文件`

​	`-x`：代表解压

**示例：**将/test下的xxx.tar.gz解压到当前目录下

`tar -xvf xxx.tar.gz`

**示例：**将/test下的xxx.tar.gz解压到根目录/usr下

`tar -xvf xxx.tar.gz -C /usr` **-C代表指定解压的位置**

## 五、其他命令

### （1）查看当前位置

`pwq`：查看当前位置

### （2）搜索命令

`grep 要搜索的字符串 要搜索的文件`

**示例：**搜索/etc/sudo.conf文件中包含字符串to的行

`grep to /etc/sudo.conf`

### （3）管道

管道是Linux命令中重要的一个概念，其作用是**将一个命令的输出用作另一个命令的输入**

示例：分页查询帮助信息

`ls --help | more`

示例：查询名称中包含java的进程

`ps –ef | grep java`

示例：查看当前系统中所有的进程中包括system字符串的进程

`ps -ef | grep system`

### （4）查看进程

`ps -ef`

**示例：**查看当前系统中运行的进程

`ps`：正在运行的某个进程的状态

`ps –ef` ：查看所有进程

`ps –ef | grep ssh`：查找某一进程

###  （5）杀死进程

`kill 2868`：杀掉2868编号的进程

`kill -9 2868`：强制杀死进程

### （6）网络通信命令

`ifconfig`：查看当前系统的网卡信息

`ping`：查看与某台机器的连接情况

`netstat -an`：查看当前系统的端口使用

### （7）重定向输出>和>>

`>`：重定向输出，覆盖原有内容；

`>>`：重定向输出，又追加功能。

`cat /etc/passwd > a.txt`：将输出定向到 **a.txt** 中

`cat /etc/passwd >> a.txt`：输出并且追加

**示例：**ifconfig > ifconfig.txt

## 六、Linux权限命令

### （1）查看权限

权限是Linux中的重要概念，每个文件/目录等都具有权限

`ls -l`：查看**某个目录下的文件或目录的权限**

```bash
    -     r w x     r w x     r - -
文件类型  属主权限   属组权限  其他用户权限
```

文件的类型：

1. `d`：代表目录
2. `-`：代表文件
3. `l`：代表链接（可以认为是window中的快捷方式）

后面的9位分为3组，每3位置一组，分别代表属主的权限，与当前用户同组的 用户的权限，其他用户的权限

`r`：代表权限是可读，r也可以用数字4表示

`w`：代表权限是可写，w也可以用数字2表示

`x`：代表权限是可执行，x也可以用数字1表示

| 权限分类 | 属主 (user) | 属主 (user) | 属主 (user) | 属组 (group) | 属组 (group) | 属组 (group) | 其他用户 | 其他用户 | 其他用户 |
| -------- | ----------- | ----------- | ----------- | ------------ | ------------ | ------------ | -------- | -------- | -------- |
| 符号权限 | r           | w           | x           | r            | w            | x            | r        | w        | x        |
| 数字权限 | 4           | 2           | 1           | 4            | 2            | 1            | 4        | 2        | 1        |

### （2）修改权限

修改文件/目录的权限的命令：`chmod`

**示例：**修改/test下的aaa.txt的权限为属主有全部权限，属主所在的组有读写权限，

其他用户只有读的权限

`chmod u=rwx,g=rw,o=r aaa.txt`

上述示例还可以使用数字表示：

`chmod 764 aaa.txt`

## 七、创建用户

### （1）切换 root 管理员身份

`su`：执行`su`回车后输入 root 密码，从普通用户切换为超级管理员 **root**

### （2）新建系统用户

`useradd 用户名`：创建新用户，系统会自动在`/home`目录生成对应家目录

**示例：**`useradd zhang` 创建用户 zhang

### （3）设置用户登录密码

`passwd 用户名`：为指定用户配置登录密码，执行后两次输入新密码完成设置

```cmd
[root@localhost yang]# passwd zhang
Changing password for user zhang.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

### （4）查看 /home 下所有用户家目录

`ll /home/`：列出所有用户的家目录，每条记录包含权限、属主、属组、大小、创建时间、目录名

```
total 4
drwx------. 15 yang  yang  4096 Oct 15 09:56 yang
drwx------. 3  zhang zhang 78 Oct 15 10:09 zhang
```

### （5）切换普通用户

`su 用户名`：从 root 切换到目标普通用户

**示例：**`su zhang` 切换至 zhang 用户

### （6）目录权限限制说明

用户家目录默认权限`drwx------`：仅目录**属主**拥有读写执行权限，同组用户、其他用户无任何访问权限

示例场景：zhang 用户执行`cd /home/gao/`，权限不足，系统提示`Permission denied`（权限拒绝）

```
[zhang@localhost gao]$ cd /home/yang/
bash: cd: /home/yang/: Permission denied
```

