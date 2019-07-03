## jdk1.8环境设置（centos7）

```shell
下载对应的安装包jdk-8u201-linux-x64.tar.gz放置/opt目录下

tar -zxvf jdk-8u201-linux-x64.tar.gz

cd /etc

vi profile

在末尾添加
export JAVA_HOME=/opt/jdk1.8.0_201
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH

重新加载profile文件
source profile

检查jdk版本
java -version

```

