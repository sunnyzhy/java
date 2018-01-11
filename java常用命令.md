# java -Xms512m -Xmx512m -XX:MaxPermSize=256m -jar xxx.jar
```
-Xms<size> 设置java堆大小的初始值

-Xmx<size> 设置java堆大小的最大值

-XX:+UseGCLogFileRotation 开启回转日志文件

-XX:GCLogFileSize=8K 设置单个文件最大的文件大小

-XX:NumberOfGCLogFiles=1 设置回转日志文件的个数
```

实例
```
java -Xms1g -Xmx1g -XX:MaxPermSize=256m -XX:+HeapDumpOnOutOfMemoryError -XX:+DisableExplicitGC -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:+PrintGCTimeStamps -XX:+PrintGCDetails -Xloggc:xxx.log -XX:+UseGCLogFileRotation -XX:GCLogFileSize=10M -XX:NumberOfGCLogFiles=20 -jar xxx.jar
```
