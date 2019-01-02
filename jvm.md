#  jmap
jmap是JDK自带的一种用于生成内存镜像文件的工具，通过该工具，开发人员可以快速生成dump文件。

生成dump文件有以下两种方法：
1. jmap -dump:format=b,file=\<filename\> \<pid\>

2. 通过配置JVM参数生成，选项 **-XX:+HeapDumpOnOutOfMemoryError** 和 **-XX:HeapDumpPath** 所代表的含义就是当程序出现OutOfMemory时，将会在相应的目录下生成一份dump文件，而如果不指定选项 **-XX:HeapDumpPath** 则在当前目录下生成dump文件。

# MAT
MAT(Memory Analyzer Tool)工具是eclipse的一个插件(MAT也可以单独使用)，可以用来分析dump文件。
MAT工具的下载地址为：http://www.eclipse.org/mat/downloads.php

1. Overview，当成功启动MAT后，通过菜单选项"File->Open heap dump..."打开指定的dump文件后，将会生成Overview选项

2. 通过Dominator Tree分析

3. 通过Histogram分析

# vm参数
- -Xms，设置堆的最小值

- -Xmx，设置堆的最大值，**将堆的最小值-Xms参数与最大值-Xmx参数设置为一样，即可避免堆自动扩展**

- -XX:+HeapDumpOnOutOfMemoryError，虚拟机在出现内存溢出异常时，dump出当前的内存堆转储快照以便分析

- -Xss，设置栈的内存容量
