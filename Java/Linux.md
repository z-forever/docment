### Linux

#### centos7

##### java

```powershell
# 下载 jdk, 解压文件,配置环境变量
vim /etc/profile

export JAVA_HOME=/root/java/jdk-11.0.12
export CLASSPATH=.:%JAVA_HOME%/lib/dt.jar:%JAVA_HOME%/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin

# 刷新配置
source /etc/profile
```

