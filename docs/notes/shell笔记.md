## 一、基础语法

### 1. 美元符号`$`语法

- `$$`：当前shell进程的ID
- `$?`：上一个命令进程的退出状态码
- `$!:`：上一个后台进程的ID
- `$@`/`$*`：表示进程的所有参数
<details>
<summary>测试4种参数解析方法</summary>

```bash
# 1. 显式地定义四种不同的模式名称
modes=('$@' '$*' '"$@"' '"$*"')
# 2. 开始遍历
for mode in "${modes[@]}"
do
  echo "=============================="
  echo "当前测试模式: $mode"
  echo "=============================="
  # 使用 eval 动态执行代码：for p in $mode; do echo "-> $p"; done
  eval "
  for p in $mode
  do
    echo \"参数内容: \$p\"
  done
  "
done
```
</details>

\$@/\$*不在双引号中时，会把参数视为分开的个体。在双引号中$*会把所有参数视为一个整体。

=== "$@模式"
    ```txt
    ==============================
    当前测试模式: $@
    ==============================
    参数内容: 1
    参数内容: 2
    参数内容: 3
    参数内容: aa
    ```

=== "$*模式"
    ```txt
    ==============================
    当前测试模式: $*
    ==============================
    参数内容: 1
    参数内容: 2
    参数内容: 3
    参数内容: aa
    ```

=== ""$@"模式"
    ```txt
    ==============================
    当前测试模式: "$@"
    ==============================
    参数内容: 1
    参数内容: 2
    参数内容: 3
    参数内容: aa
    ```

=== ""$*"模式"
    ```txt
    ==============================
    当前测试模式: "$*"
    ==============================
    参数内容: 1 2 3 aa
    ```


- `$0`：脚本文件名，如果在shell环境echo $0，将得到当前的shell名称
- `$1`/`$2`/`$3`：进程的第1个参数、第2个参数、第3个参数
- `${n}`：进程的第n个参数，超过10个参数时，需要使用${n}语法
    - `${n:-default}`：进程的第n个参数，如果不存在，则使用default值

### 2. `[]条件语法`

在其他编程语言中的if语句，shell中使用`[]`语法来实现。具体的，`if(cond) {// do something}`，在shell中可以写为
```bash
if [ $var -gt 5 ] then
  echo "var is greater than 5"
fi
```

同样的，shell也支持while循环
```bash
while [ $var -gt 5 ]
do
  echo "var is greater than 5"
done
```

> 在上面的`-lt`，是其他编程语言中的小于号，表示小于。其他操作符包括`-gt`: `>`，`-ge`: `>=`，`-ne`: `!=`，`-eq`: `==`，`-le`: `<=`

### 3. `for循环语法`

```bash
# 遍历所有参数
for i in $@
do
  echo $i
done
```

## 二、常用内置命令
### 1. 自定义ls命令输出

```bash
# 自定义时间格式
alias ll="ls -al --time-style=+%Y%m%d\ %H:%M:%S"
```
### 2. tty设备通信

```bash
# 向tty9发送数据
echo "hello tty9" > /dev/pts/9
```
> `>`是重定向操作符，用于将输出重定向到文件或设备。  
> 也就是说，/dev/pts/9即使是一个tty设备，它也是一个文件，只是linux将这种文件视为一个设备，这个设备可以显式交互。

### 3. 查找指定进程

```bash
# 查找指定进程
ps aux | grep "进程名"
```

=== "`ps aux`"
    `ps aux`更倾向于显示进程占用的资源信息
    ```bash title="ps aux" 
    USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND  
    ```

=== "`ps -ef`"
    `ps -ef`显示父子进程关系
    ```bash title="ps -ef" 
    UID        PID  PPID  C STIME TTY          TIME CMD
    ```

> ps的参数：

>  1. 进程选择
>     - -e / -A：显示所有进程。
>     - -a：显示所有终端上的进程（不包含会话领导者）。
>     - -u：显示指定用户的进程（常与 -u root 或 -u $USER 组合）。
>     - -p：显示指定 PID
>     - -t：显示与指定 TTY（终端）关联的进程。
>  2. 输出格式
>     - -f：显示完整格式（包含 UID, PID, PPID, C, STIME, TTY, TIME, CMD）。
>     - -l：显示长格式（包含更多技术细节，如优先级、F 标志等）。
>     - -j：显示与作业（Jobs）相关的格式。
>     - -o：自定义输出列（例如 ps -o pid,comm 只显示 PID 和命令名称）。
>  3. 其他辅助功能
>     - -L：显示进程关联的线程（LWP 和 NLWP）。
>     - -C：通过命令名称来过滤选择进程（例如 -C bash）


### 4. 使用tar命令压缩文件

使用`--exclude=dir_name`，排除所有目录下的`__pycache__`目录

```bash
(base) xyl@arch ~ % tar --exclude=__pycache__ -czvf test.tar.gz test
# a test
# a test/init
# a test/mm
# a test/process
(base) xyl@arch ~ % tree test
# test
# ├── __pycache__
# ├── init
# │   └── __pycache__
# ├── mm
# │   └── __pycache__
# └── process
#     └── __pycache__
# 8 directories, 0 files
```

### 5. `awk`、`sed`、`grep`三剑客  

使用`awk`对纯文本文件进行格式化处理
```bash
# 读取test.csv文件，使用","作为分隔符，打印第一列、第二列、第三列的内容
awk -F"," '{print $1, $2, $3}' test.csv
```

使用`sed -g`对纯文本文件进行全局替换
```bash
# 读取`test.txt`文件，将所有"hello"替换为"world"
sed -g 's/hello/world/g' test.txt

# 添加-i参数，直接修改文件内容
sed -i 's/hello/world/g' test.txt
```

使用`grep`对纯文本文件进行搜索
```bash
# 读取`test.txt`文件，搜索所有包含"hello"的行
grep "hello" test.txt
```

### 6. 磁盘命令

使用`du`命令查看文件夹占用情况
```bash
> du -sh dirname
# du -sh ~
# 11G	/root
```

使用`df`查看磁盘使用情况

```bash
> df -h /
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/vda3        40G   21G   17G  55% /
```

使用`lsblk`命令查看磁盘挂载情况

```bash
> lsblk
# NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
# vda    253:0    0   40G  0 disk 
# ├─vda1 253:1    0    1M  0 part 
# ├─vda2 253:2    0  200M  0 part /boot/efi
# └─vda3 253:3    0 39.8G  0 part /
```

使用`fdisk -l`查看不同分区的文件系统

```bash
> fdisk -l
# Disk /dev/vda: 40 GiB, 42949672960 bytes, 83886080 sectors
# Units: sectors of 1 * 512 = 512 bytes
# Sector size (logical/physical): 512 bytes / 512 bytes
# I/O size (minimum/optimal): 512 bytes / 512 bytes
# Disklabel type: gpt
# Disk identifier: A1EA1783-7D1D-4C3C-84B6-BF7D4AF86695

# Device      Start      End  Sectors  Size Type
# /dev/vda1    2048     4095     2048    1M BIOS boot
# /dev/vda2    4096   413695   409600  200M EFI System
# /dev/vda3  413696 83886046 83472351 39.8G Linux filesystem
```

使用`cfdisk /dev/vda`手动分区或扩容

```bash
> cfdisk /dev/vda
#                         Disk: /dev/vda
#             Size: 40 GiB, 42949672960 bytes, 83886080 sectors
#     Label: gpt, identifier: A1EA1783-7D1D-4C3C-84B6-BF7D4AF86695

# Device       Start    End        Sectors     Size Type
# /dev/vda1    2048     4095       2048        1M BIOS boot                      
# /dev/vda2    4096     413695     409600      200M EFI System
# /dev/vda3    413696   83886046   83472351    39.8G Linux filesystem


# ┌──────────────────────────────────────────────────────────────────────┐
# │Partition UUID: CC43A9AA-558B-4F53-9BBD-7205EC26CC28                  │
# │Partition type: BIOS boot (21686148-6449-6E6F-744E-656564454649)      │
# └──────────────────────────────────────────────────────────────────────┘
#  [ Delete ] [ Resize ] [ Quit ] [ Type ] [ Help ]  [ Write ]  [ Dump ]

#   Device is currently in use, repartitioning is probably a bad idea.
#          Quit program without writing changes
```

### 7. date时间命令

=== "生成`年月日时分秒`格式的时间"

    ```bash
    > date '+%Y%m%d %H:%M:%S.%N'
    # Y: year, m: month, d: day
    # H: hour, M: minute, S: second
    # N: nanosecond
    ```
=== "获得时间戳"

    ```bash
    > date '+%s'
    # 1784787791
    ```