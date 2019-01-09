---
title: linux常用命令
tags:
  - linux
  - 命令
categories:
  - 后端
comments: false    // 是否开启评论
date: 2018-08-03 14:10:54
---
### mkdir 创建文件夹/目录
命令参数：

      -m, --mode=模式，设定权限<模式> (类似 chmod)，而不是 rwxrwxrwx 减 umask
    
      -p, --parents     可以是一个路径名称。
                        此时若路径中的某些目录尚不存在,加上此选项后,
                        系统将自动建立好那些尚不存在的目录,即一次可以建立多个目录; 
    
      -v, --verbose     每次创建新目录都显示信息

          --help        显示此帮助信息并退出
    
          --version     输出版本信息并退出

### cp 复制
cp命令语法

    cp [options] sourcedir   destdir
参数说明：

    -a：此选项通常在复制目录时使用，它保留链接、文件属性，并复制目录下的所有内容。其作用等于dpR参数组合。
    -d：复制时保留链接。这里所说的链接相当于Windows系统中的快捷方式。
    -f：覆盖已经存在的目标文件而不给出提示。
    -i：与-f选项相反，在覆盖目标文件之前给出提示，要求用户确认是否覆盖，回答"y"时目标文件将被覆盖。
    -p：除复制文件的内容外，还把修改时间和访问权限也复制到新文件中。
    -r：若给出的源文件是一个目录文件，此时将复制该目录下所有的子目录和文件。
    -l：不复制文件，只是生成链接文件。

### 解压缩文件夹
tar命令

    　　解包：tar zxvf FileName.tar
    
    　　打包：tar czvf FileName.tar DirName
zip命令

    　　解压：unzip FileName.zip
    
    　　压缩：zip FileName.zip DirName
### rm 删除文件夹

rm -rf 目录名字   

    -r 向下递归，不管有多少级目录，一并删除
    -f 直接强行删除，没有任何提示
删除文件夹实例：

    rm -rf /var/log/httpd
删除文件使用实例：

    rm -f /var/log/httpd/access.log

### 文件和目录 
    cd /home 进入 '/ home' 目录' 
    cd .. 返回上一级目录 
    cd ../.. 返回上两级目录 
    cd 进入个人的主目录 
    cd ~user1 进入个人的主目录 
    cd - 返回上次所在的目录 
    pwd 显示工作路径 
    ls 查看目录中的文件 
    ls -F 查看目录中的文件 
    ls -l 显示文件和目录的详细资料 
    ls -a 显示隐藏文件 
    ls *[0-9]* 显示包含数字的文件名和目录名 
    tree 显示文件和目录由根目录开始的树形结构(1) 
    lstree 显示文件和目录由根目录开始的树形结构(2) 
    mkdir dir1 创建一个叫做 'dir1' 的目录' 
    mkdir dir1 dir2 同时创建两个目录 
    mkdir -p /tmp/dir1/dir2 创建一个目录树 
    rm -f file1 删除一个叫做 'file1' 的文件' 
    rmdir dir1 删除一个叫做 'dir1' 的目录' 
    rm -rf dir1 删除一个叫做 'dir1' 的目录并同时删除其内容 
    rm -rf dir1 dir2 同时删除两个目录及它们的内容 
    mv dir1 new_dir 重命名/移动 一个目录 
    cp file1 file2 复制一个文件 
    cp dir/* . 复制一个目录下的所有文件到当前工作目录 
    cp -a /tmp/dir1 . 复制一个目录到当前工作目录 
    cp -a dir1 dir2 复制一个目录 
    ln -s file1 lnk1 创建一个指向文件或目录的软链接 
    ln file1 lnk1 创建一个指向文件或目录的物理链接 

### 文件搜索 

    find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录 
    find / -user user1 搜索属于用户 'user1' 的文件和目录 
    find /home/user1 -name \*.bin 在目录 '/ home/user1' 中搜索带有'.bin' 结尾的文件 
    find /usr/bin -type f -atime +100 搜索在过去100天内未被使用过的执行文件 
    find /usr/bin -type f -mtime -10 搜索在10天内被创建或者修改过的文件 
    find / -name \*.rpm -exec chmod 755 '{}' \; 搜索以 '.rpm' 结尾的文件并定义其权限 
    find / -xdev -name \*.rpm 搜索以 '.rpm' 结尾的文件，忽略光驱、捷盘等可移动设备 
    locate \*.ps 寻找以 '.ps' 结尾的文件 - 先运行 'updatedb' 命令 
    whereis halt 显示一个二进制文件、源码或man的位置 
    which halt 显示一个二进制文件或可执行文件的完整路径 

### 打包和压缩文件 

    bunzip2 file1.bz2 解压一个叫做 'file1.bz2'的文件 
    bzip2 file1 压缩一个叫做 'file1' 的文件 
    gunzip file1.gz 解压一个叫做 'file1.gz'的文件 
    gzip file1 压缩一个叫做 'file1'的文件 
    gzip -9 file1 最大程度压缩 
    rar a file1.rar test_file 创建一个叫做 'file1.rar' 的包 
    rar a file1.rar file1 file2 dir1 同时压缩 'file1', 'file2' 以及目录 'dir1' 
    rar x file1.rar 解压rar包 
    unrar x file1.rar 解压rar包 
    tar -cvf archive.tar file1 创建一个非压缩的 tarball 
    tar -cvf archive.tar file1 file2 dir1 创建一个包含了 'file1', 'file2' 以及 'dir1'的档案文件 
    tar -tf archive.tar 显示一个包中的内容 
    tar -xvf archive.tar 释放一个包 
    tar -xvf archive.tar -C /tmp 将压缩包释放到 /tmp目录下 
    tar -cvfj archive.tar.bz2 dir1 创建一个bzip2格式的压缩包 
    tar -jxvf archive.tar.bz2 解压一个bzip2格式的压缩包 
    tar -cvfz archive.tar.gz dir1 创建一个gzip格式的压缩包 
    tar -zxvf archive.tar.gz 解压一个gzip格式的压缩包 
    zip file1.zip file1 创建一个zip格式的压缩包 
    zip -r file1.zip file1 file2 dir1 将几个文件和目录同时压缩成一个zip格式的压缩包 
    unzip file1.zip 解压一个zip格式压缩包 
### YUM 软件包升级器 - （Fedora, RedHat及类似系统） 
    yum install package_name 下载并安装一个rpm包 
    yum localinstall package_name.rpm 将安装一个rpm包，使用你自己的软件仓库为你解决所有依赖关系 
    yum update package_name.rpm 更新当前系统中所有安装的rpm包 
    yum update package_name 更新一个rpm包 
    yum remove package_name 删除一个rpm包 
    yum list 列出当前系统中安装的所有包 
    yum search package_name 在rpm仓库中搜寻软件包 
    yum clean packages 清理rpm缓存删除下载的包 
    yum clean headers 删除所有头文件 
    yum clean all 删除所有缓存的包和头文件 
### 文本处理 
    cat file1 file2 ... | command <> file1_in.txt_or_file1_out.txt general syntax for text manipulation using PIPE, STDIN and STDOUT 
    cat file1 | command( sed, grep, awk, grep, etc...) > result.txt 合并一个文件的详细说明文本，并将简介写入一个新文件中 
    cat file1 | command( sed, grep, awk, grep, etc...) >> result.txt 合并一个文件的详细说明文本，并将简介写入一个已有的文件中 
    grep Aug /var/log/messages 在文件 '/var/log/messages'中查找关键词"Aug" 
    grep ^Aug /var/log/messages 在文件 '/var/log/messages'中查找以"Aug"开始的词汇 
    grep [0-9] /var/log/messages 选择 '/var/log/messages' 文件中所有包含数字的行 
    grep Aug -R /var/log/* 在目录 '/var/log' 及随后的目录中搜索字符串"Aug" 
    sed 's/stringa1/stringa2/g' example.txt 将example.txt文件中的 "string1" 替换成 "string2" 
    sed '/^$/d' example.txt 从example.txt文件中删除所有空白行 
    sed '/ *#/d; /^$/d' example.txt 从example.txt文件中删除所有注释和空白行 
    echo 'esempio' | tr '[:lower:]' '[:upper:]' 合并上下单元格内容 
    sed -e '1d' result.txt 从文件example.txt 中排除第一行 
    sed -n '/stringa1/p' 查看只包含词汇 "string1"的行 
    sed -e 's/ *$//' example.txt 删除每一行最后的空白字符 
    sed -e 's/stringa1//g' example.txt 从文档中只删除词汇 "string1" 并保留剩余全部 
    sed -n '1,5p;5q' example.txt 查看从第一行到第5行内容 
    sed -n '5p;5q' example.txt 查看第5行 
    sed -e 's/00*/0/g' example.txt 用单个零替换多个零 
    cat -n file1 标示文件的行数 
    cat example.txt | awk 'NR%2==1' 删除example.txt文件中的所有偶数行 
    echo a b c | awk '{print $1}' 查看一行第一栏 
    echo a b c | awk '{print $1,$3}' 查看一行的第一和第三栏 
    paste file1 file2 合并两个文件或两栏的内容 
    paste -d '+' file1 file2 合并两个文件或两栏的内容，中间用"+"区分 
    sort file1 file2 排序两个文件的内容 
    sort file1 file2 | uniq 取出两个文件的并集(重复的行只保留一份) 
    sort file1 file2 | uniq -u 删除交集，留下其他的行 
    sort file1 file2 | uniq -d 取出两个文件的交集(只留下同时存在于两个文件中的文件) 
    comm -1 file1 file2 比较两个文件的内容只删除 'file1' 所包含的内容 
    comm -2 file1 file2 比较两个文件的内容只删除 'file2' 所包含的内容 
    comm -3 file1 file2 比较两个文件的内容只删除两个文件共有的部分 
