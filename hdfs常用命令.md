hdfs是一个分布式的文件存储系统，既然是一个存储系统，那么就会有相应的命令去操作存储在系统上的文件。

操作hdfs系统可以使用hadoop fs 也可以使用 hdfs dfs ,两者效果一样。(hadoop dfs命令已不再建议使用)

帮助命令
一切命令都从帮助开始，命令是记不完的，只有学会使用帮助，才能免却记忆的痛苦

hadoop fs -help
当执行-help命令后，可以列出所有命令帮助信息。

查看某一命令的帮助信息可在-help后面跟上所要查询的命令

hadoop fs -help cat
常用命令
hadoop fs （hdfs dfs）文件操作
ls 显示目录下的所有文件或者文件夹
使用方法： hadoop fs -ls [uri形式目录]

示例: hadoop fs -ls / 显示根目录下的所有文件和目录

显示目录下的所有文件可以加 -R 选项

示例: hadoop fs -ls -R /
cat 查看文件内容
使用方法：hadoop fs -cat URI [URI …]

示例： hadoop fs -cat /in/test2.txt
3.mkdir 创建目录

使用方法：hadoop fs -mkdir [uri形式目录]

示例: hadoop fs –mkdir /test

创建多级目录 加上 -p

示例: hadoop fs -mkdir -p /a/b/c
4.rm 删除目录或者文件

使用方法:hadoop fs -rm [文件路径] 删除文件夹加上 -r

示例: hadoop fs -rm /test1.txt

删除文件夹加上 -r，

示例:hadoop fs -rm -r /test
5.put 复制文件

将文件复制到hdfs系统中，也可以是从标准输入中读取文件，此时的dst是一个文件

使用方法: hadoop fs -put <localsrc> ... <dst>

示例：

Hadoop fs -put /usr/topabu/temp/test1.txt /

从标准输入中读取文件：hadoop fs -put -/in/myword
6.cp 复制系统内文件

使用方法：hadoop fs -cp URI [URI …] <dest>

将文件从源路径复制到目标路径。这个命令允许有多个源路径，此时目标路径必须是一个目录。
示例：

hadoop fs -cp /in/myword/word
7.copyFromLocal 复制本地文件到hdfs

使用方法：hadoop fs -copyFromLocal <localsrc> URI

除了限定源路径是一个本地文件外，和put命令相似
8.get 复制文件到本地系统

使用方法：hadoop fs -get[-ignorecrc] [-crc] <src> <localdst>

复制文件到本地文件系统。可用-ignorecrc选项复制CRC校验失败的文件。使用-crc选项复制文件以及CRC信息。

示例：hadoop fs -get/word /usr/topabu/temp/word.txt
copyToLocal 复制 文件到本地系统
使用方法：hadoop fs -copyToLocal [-ignorecrc] [-crc] URI <localdst>

除了限定目标路径是一个本地文件外，和get命令类似。

示例：hadoop fs -copyToLocal /word /usr/topabu/temp/word.txt
mv
将文件从源路径移动到目标路径。这个命令允许有多个源路径，此时目标路径必须是一个目录。不允许在不同的文件系统间移动文件。

使用方法：hadoop fs -mv URI [URI …] <dest>

示例：hadoop fs -mv /in/test2.txt /test2.txt
11.du 显示文件大小

显示目录中所有文件的大小。

使用方法：hadoop fs -du URI [URI …]

示例: hadoop fs -du /

显示当前目录或者文件夹的大小可加选项 -s

示例: hadoop fs -du -s /
12.touchz 创建空文件

使用方法：hadoop fs -touchz URI [URI …]

创建一个0字节的空文件

示例:hadoop fs -touchz /empty.txt
chmod 改变文件权限
使用方法：hadoop fs -chmod[-R] <MODE[,MODE]... | OCTALMODE> URI [URI …]

与Linux平台下chmod命令相似，改变文件的权限。使用-R将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。
chown 改变文件所有者
使用方法：hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI]

改变文件的拥有者。使用-R将使改变在目录结构下递归进行。命令的使用者必须是超级用户。

示例:hadoop fs -chown -R test /a 
chgrp 改变文件所在组
使用方法：hadoop fs -chgrp [-R] GROUP URI [URI …]

改变文件所属的组。使用-R将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。

示例：hadoop fs -chgrp -R test /a
hdfs dfsadmin 管理命令
-report
查看文件系统的基本信息和统计信息。

示例：hadoop dfsadmin -report
-safemode
enter | leave | get | wait：安全模式命令。
安全模式是NameNode的一种状态，在这种状态下，NameNode不接受对名字空间的更改（只读）,不复制或删除块。
NameNode在启动时自动进入安全模式，当配置块的最小百分数满足最小副本数的条件时，会自动离开安全模式。
enter是进入，leave是离开。

示例:
hadoop dfsadmin -safemode get
hadoop dfsadmin -safemode enter
-refreshNodes
重新读取hosts和exclude文件，使新的节点或需要退出集群的节点能够被NameNode重新识别。这个命令在新增节点或注销节点时用到。

示例：hadoop dfsadmin -refreshNodes
-finalizeUpgrade
终结HDFS的升级操作。DataNode删除前一个版本的工作目录，之后NameNode也这样做。
-upgradeProgress
status| details | force：请求当前系统的升级状态 | 升级状态的细节| 强制升级操作
-metasave filename
保存NameNode的主要数据结构到hadoop.log.dir属性指定的目录下的<filename>文件中。
-setQuota<quota><dirname>……<dirname>
为每个目录<dirname>设定配额<quota>。目录配额是一个长整形整数，强制设定目录树下的名字个数。
-clrQuota<dirname>……<dirname>
为每个目录<dirname>清除配额设定。
