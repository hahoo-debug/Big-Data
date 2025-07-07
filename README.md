
# mac M4 芯片安装 Hive 教程



在 macOS 上使用 **Apple Silicon（M1/M2/M4）** 运行 Hive 时遇到如下报错：
```bash
ClassCastException: class jdk.internal.loader.ClassLoaders$AppClassLoader cannot be cast to class java.net.URLClassLoader
```
通常是由于 **Java 版本不兼容** 或 **Hive 版本与 ARM 架构适配问题** 导致的。
以下是解决方案，从 Java、MySQL、Hadoop 环境搭建开始，安装并配置 Apache Hive。  


  

## 目录

  

1. Java 安装与配置（Java 8）

2. MySQL 安装与配置

3. Hadoop 安装与环境变量配置

4. Hive 安装与启动验证

  

---

  

## 1. 安装 Java 8（使用 Temurin）

  

Hive 3.x 推荐使用 Java 8。使用 Homebrew 安装：

  

```bash

brew  install  --cask  temurin@8

```

  

设置环境变量：

  

```bash

echo  'export JAVA_HOME="/Library/Java/JavaVirtualMachines/temurin-8.jdk/Contents/Home"' >> ~/.zshrc

echo  'export PATH="$JAVA_HOME/bin:$PATH"' >> ~/.zshrc

source  ~/.zshrc

```

  

确认版本：

  

```bash

java  -version

```

  

### 查看和切换已安装的 Java 版本

  

查看所有已安装的 Java 版本：

  

```bash

/usr/libexec/java_home  -V

```

  

输出示例：

  

```

Matching Java Virtual Machines (2):

21.0.1, x86_64: "/Library/Java/JavaVirtualMachines/temurin-21.jdk/Contents/Home"

1.8.0_392, x86_64: "/Library/Java/JavaVirtualMachines/temurin-8.jdk/Contents/Home"

```

  

切换到 Java 8：

  

```bash

export  JAVA_HOME="/Library/Java/JavaVirtualMachines/temurin-8.jdk/Contents/Home"

export  PATH="$JAVA_HOME/bin:$PATH"

```



---

  

## 2. 安装 MySQL（Metastore 使用）

  



  

### 安装 MySQL&#x20;

  

```bash

brew  install  mysql

```

  

配置 root 密码并登录：

  

```bash

brew  services  start  mysql

mysql  -u  root

```

  
  
  

---

  

## 3. 安装 Hadoop

  

### 下载 Hadoop 3.3.6

  

```bash

curl  -O  https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz

tar  -xzf  hadoop-3.3.6.tar.gz

mv  hadoop-3.3.6  ~/hadoop

```

  

### 配置环境变量：

  

```bash

echo  'export HADOOP_HOME=~/hadoop' >> ~/.zshrc

echo  'export PATH="$HADOOP_HOME/bin:$PATH"' >> ~/.zshrc

source  ~/.zshrc

```

  

确认版本：

  

```bash

hadoop  version

```

  

看到版本号说明安装成功

  

```bash

Hadoop  3.3.6

```

  
  

---

  

## 4. 安装 Hive（4.0.1 ）

  

### 下载 Hive 并解压

  

可以从阿里云镜像下载 Hive 4.0.1：

https://mirrors.aliyun.com/apache/hive/hive-4.0.1/?spm=a2c6h.25603864.0.0.1f9c29b1ydwkbP

  

下载完进行解压缩重命名

```bash

tar  -xzf  apache-hive-4.0.1-bin.tar.gz

mv  apache-hive-4.0.1-bin  ~/hive

```

  

### 配置环境变量：

  

```bash

echo  'export HIVE_HOME=~/hive' >> ~/.zshrc

echo  'export PATH="$HIVE_HOME/bin:$PATH"' >> ~/.zshrc

source  ~/.zshrc

```

  

### 配置 `hive-site.xml`

  

复制模板并编辑：

  

```bash

cp  $HIVE_HOME/conf/hive-default.xml.template  $HIVE_HOME/conf/hive-site.xml

```

  

在 `hive-site.xml` 中设置如下参数：

  

```xml

<?xml version="1.0" encoding="UTF-8"?>

<configuration>

<property>

<name>javax.jdo.option.ConnectionURL</name>

<value>jdbc:mysql://localhost:3306/metastore?createDatabaseIfNotExist=true&amp;useSSL=false&amp;allowPublicKeyRetrieval=true</value>

</property>

<property>

<name>javax.jdo.option.ConnectionDriverName</name>

<value>com.mysql.cj.jdbc.Driver</value>

</property>

<property>

<name>javax.jdo.option.ConnectionUserName</name>

<value>hive</value>

</property>

<property>

<name>javax.jdo.option.ConnectionPassword</name>

<value>hivepassword</value>

</property>

</configuration>
```

  

### 添加 MySQL 驱动包

  

从MySQL官网 https://downloads.mysql.com/archives/c-j/ 下载
`mysql-connector-j-8.2.0` 选择`Platform Independent`，解压缩后将.jar文件放入hive目录下的lib中：

  

```bash

cp  mysql-connector-j-8.2.0.jar  $HIVE_HOME/lib/

```

  

### 初始化 Metastore：

  

```bash

schematool  -initSchema  -dbType  mysql

```

  

如果提示 `Initialization script completed`说明正确。

  

---

  

## 5. 启动 Hive

  

```bash

hive

```

  

如果看到：

  

```

Beeline version 4.0.1 by Apache Hive

beeline>

```

  

说明安装成功 ✅

  

退出命令：

  

```sql

!quit

```

  

---
