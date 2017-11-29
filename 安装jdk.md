# 官网
http://www.oracle.com/technetwork/java/javase/downloads/index.html

# 卸载自带的OpenJdk
```
# rpm -qa | grep java
python-javapackages-3.4.1-11.el7.noarch
tzdata-java-2017c-1.el7.noarch
java-1.7.0-openjdk-headless-1.7.0.151-2.6.11.1.el7_4.x86_64
java-1.8.0-openjdk-headless-1.8.0.151-1.b12.el7_4.x86_64
java-1.7.0-openjdk-1.7.0.151-2.6.11.1.el7_4.x86_64
javapackages-tools-3.4.1-11.el7.noarch
java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4.x86_64

# rpm -qa | grep jdk
java-1.7.0-openjdk-headless-1.7.0.151-2.6.11.1.el7_4.x86_64
java-1.8.0-openjdk-headless-1.8.0.151-1.b12.el7_4.x86_64
java-1.7.0-openjdk-1.7.0.151-2.6.11.1.el7_4.x86_64
java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4.x86_64
copy-jdk-configs-2.2-3.el7.noarch

# yum -y remove java-1.7.0-openjdk-headless java-1.8.0-openjdk-headless java-1.7.0-openjdk java-1.8.0-openjdk
```

# 安装
```
# cd /usr/local

# tar -zxvf jdk-8u151-linux-x64.tar.gz

# mv jdk1.8.0_151 jdk
```

# 配置环境变量
```
# vim /etc/profile
export JAVA_HOME=/usr/local/jdk
export CLASSPATH=$JAVA_HOME/jre/lib:$JAVA_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin

# source /etc/profile

# java -version
java version "1.8.0_151"
Java(TM) SE Runtime Environment (build 1.8.0_151-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.151-b12, mixed mode)

# javac
Usage: javac <options> <source files>
where possible options include:
  -g                         Generate all debugging info
  -g:none                    Generate no debugging info
  -g:{lines,vars,source}     Generate only some debugging info
  -nowarn                    Generate no warnings
  -verbose                   Output messages about what the compiler is doing
  -deprecation               Output source locations where deprecated APIs are used
  -classpath <path>          Specify where to find user class files and annotation processors
  -cp <path>                 Specify where to find user class files and annotation processors
  -sourcepath <path>         Specify where to find input source files
  -bootclasspath <path>      Override location of bootstrap class files
  -extdirs <dirs>            Override location of installed extensions
  -endorseddirs <dirs>       Override location of endorsed standards path
  -proc:{none,only}          Control whether annotation processing and/or compilation is done.
  -processor <class1>[,<class2>,<class3>...] Names of the annotation processors to run; bypasses default discovery process
  -processorpath <path>      Specify where to find annotation processors
  -parameters                Generate metadata for reflection on method parameters
  -d <directory>             Specify where to place generated class files
  -s <directory>             Specify where to place generated source files
  -h <directory>             Specify where to place generated native header files
  -implicit:{none,class}     Specify whether or not to generate class files for implicitly referenced files
  -encoding <encoding>       Specify character encoding used by source files
  -source <release>          Provide source compatibility with specified release
  -target <release>          Generate class files for specific VM version
  -profile <profile>         Check that API used is available in the specified profile
  -version                   Version information
  -help                      Print a synopsis of standard options
  -Akey[=value]              Options to pass to annotation processors
  -X                         Print a synopsis of nonstandard options
  -J<flag>                   Pass <flag> directly to the runtime system
  -Werror                    Terminate compilation if warnings occur
  @<filename>                Read options and filenames from file
```
