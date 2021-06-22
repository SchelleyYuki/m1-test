# 目录
- [概述](#概述)
- [先决条件](#先决条件)
- [测试1: 使用JDK-x64, 运行缺省Maven命令](#测试1-使用JDK-x64-运行缺省Maven命令)
- [测试2: 使用JDK-x64, 运行优化过的Maven命令](#测试2-使用JDK-x64-运行优化过的Maven命令)
- [测试3: 使用JDK-aarch64, 运行缺省Maven命令](#测试3-使用JDK-aarch64-运行缺省Maven命令)
- [测试4: 使用JDK-aarch64, 运行优化过的Maven命令](#测试4-使用JDK-aarch64-运行优化过的Maven命令)
- [引用](#引用)
- [关注我](#关注我)

## 概述
本文列出了我在[我的YouTube视频: M1性能测试](https://youtu.be/o6q8zPmfVLU)里跑过的测试命令，用于比较我的M1 Macbook Ai和我的Intel Core i9 Macbook Pro的编译速度。

测试1和测试2使用的是JDK-x64, 在两台机器上都运行了。

测试3和测试4使用的是JDK-aarch64, 只在M1 Macbook Air上运行。

## 先决条件
在你运行这些测试之前，确保你的机器安装了JDK和Maven。

Zulu JDK-aarch64的下载地址在这个[页面](https://www.azul.com/downloads/zulu-community/?os=macos&architecture=arm-64-bit&package=jdk)。在我的M1 Macbook Air上, 该JDK被安装在了如下路径: 
```
/Library/Java/JavaVirtualMachines/zulu-16.jdk
```

(2020年12月15日更新: 为了让对比测试更加地公平, 最好在i9 Macbook Pro上也使用Zulu JDK, 并且使用和在M1 Macbook Air上版本相同的Zulu JDK。在录制视频时, 我在i9 Macbook Pro上使用的JDK不是Zulu JDK。今天早些时候，我在两台机器上都安装了Zulu Java 8, 又做了一次对比测试。测试结果与我的YouTube视频里的最终结果高度吻合。）

接下来, 下载一个Java repo，比如Apache Commons Lang。
```
cd
mkdir -p src/apache
cd src/apache
git clone https://github.com/apache/commons-lang.git
cd commons-lang
```
## 测试1: 使用JDK-x64, 运行缺省Maven命令

我在M1 Macbook Air和i9 Macbook Pro上都运行了该测试。

此处我假设你的`$JAVA_HOME`指向的是JDK-x64。

以下是编译命令。
```
mvn clean compile
```

如果遇到`Too many files with unapproved license`这个错误，加一个参数就能解决，像下面这样: 
```
mvn clean -Drat.skip=true compile
```

该命令的输出会告诉你编译时长。

## 测试2: 使用JDK-x64, 运行优化过的Maven命令

我在M1 Macbook Air和i9 Macbook Pro上都运行了该测试。

```
MAVEN_OPTS="-XX:+TieredCompilation -XX:TieredStopAtLevel=1" \
mvn clean -T 1C -Drat.skip=true compile -offline
```

解释:
- `-XX:+TieredCompilation -XX:TieredStopAtLevel=1`: 加速JVM的启动速度。
- `-T 1C`: 为CPU的每个核都分配一个线程。
- `-offline`: 不要从远端repos下载, 以确保整个编译过程都在本地进行运算。

## 测试3: 使用JDK-aarch64, 运行缺省Maven命令

该测试只在M1 Macbook Air上运行。现在你需要把`$JAVA_HOME`指向JDK-aarch64。
```
JAVA_HOME="/Library/Java/JavaVirtualMachines/zulu-16.jdk/Contents/Home" \
mvn clean -Drat.skip=true compile
```

## 测试4: 使用JDK-aarch64, 运行优化过的Maven命令

该测试只在M1 Macbook Air上运行。现在你需要把`$JAVA_HOME`指向JDK-aarch64，并且在命令中加入优化参数。

```
JAVA_HOME="/Library/Java/JavaVirtualMachines/zulu-16.jdk/Contents/Home" \
MAVEN_OPTS="-XX:+TieredCompilation -XX:TieredStopAtLevel=1" \
mvn clean -T 1C -Drat.skip=true compile -offline
```

## 引用
- [How to Speed up Your Maven Build](https://www.jrebel.com/blog/how-to-speed-up-your-maven-build)
- [JEP 391: macOS/AArch64 Port](https://bugs.openjdk.java.net/browse/JDK-8251280)

## 关注我
- 官网: [SchelleyYuki.com](https://schelleyyuki.com)
- 油管: [youtube.com/c/SchelleyYuki](https://www.youtube.com/c/SchelleyYuki)
- Instagram: [instagram.com/schelleyyuki_/](https://www.instagram.com/schelleyyuki_/)
