# Jdk
JDK(Java Development Kit) 作为Java语言的软件开发工具包影响了一代又一代程序员。

Faster使用JDK1.8作为开发SDK。

我们会不断更新JDK使用版本以保证开发人员得到最新的开发体验。

## 下载安装

- [JDK1.8下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)


java目标对于JDK的版本维护存在CPU（奇数）版本与PSU（偶数）版本。奇数版本为重要补丁，偶数版本为全部补丁。Oracle建议大多数用户应当选择 CPU 版本。

## 环境变量

新建环境变量：

```
JAVA_HOME={安装路径}
```

PATH中加入：

```
%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```

## 确认安装

终端中输入 java -version

```
java version "xxx"
Java(TM) SE Runtime Environment (build xxx)
Java HotSpot(TM) 64-Bit Server VM (build xxx, mixed mode)
```
