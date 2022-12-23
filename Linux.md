# Linux

Linux 只是一个内核，不是一个完整的操作系统  
Ubuntu 是一个 linux-based 的操作系统  
shell（Bash/ZSH）是人与操作系统打交道的接口，shell 将命令转为内核能理解的语句，传给内核
terminal 显示输入输出

```bash
cat #原用来写入文件 cat  file1.txt > file2.txt
# cat file.txt 打印文件内容
cd #切换路径
chown
chomd <mode> <file> #改变文件权限 (owner/group/world)rwx 最多是7(111)
#eg: chmod 777 file.txt
#eg: chmod a+w file.txt 给所有人加上write
cp #复制文件
diff #找两个文件的区别
df #获取磁盘使用情况
du #计算目录下文件size


echo #print
grep <string> <file> #在文件中搜字符串
gzip #用lz77压缩/解压缩(-d)
head #输出头几行
jobs # 查看所有任务 可以是stopped的任务
kill <pid> #杀进程
ln #创建硬连接 ln <origin> <newlink> 若删除原文件，新文件仍指向那一段内存
ln -s #创建软连接 若删除原文件，打开新文件会提示原文件不存在
ls #展示当前文件下文件

man <command> #介绍某个命令
mkdir #新建文件夹
mv #移动文件和目录
#mv file1.txt file2.txt 也可以用来重命名
ps #查看进程
rmdir #删除文件夹
sort <file> #sort文件中的行 但不改变文件
tail #输出最后几行
tar #压缩解压缩
# tar -cf archive.tar file1 file2 压缩
# tar -xf archive.tar 解压缩
top #查看资源占用最多的进程(CPU MEM ...)
top -o mem #查看内存占用最多的进程(macos)
touch #新建文件(如果有就更新时间戳，没有就新建)
wc #统计行数字数字节数
> #写入
｜ #把前者的输出作为后者的输入


```
