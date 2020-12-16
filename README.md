# Table of Contents
- [Overview](#overview)
- [Prerequisite](#prerequisite)
- [Test 1: default command with JDK-x64](#test-1-default-command-with-jdk-x64)
- [Test 2: optimized command with JDK-x64](#test-2-optimized-command-with-jdk-x64)
- [Test 3: default command with JDK-aarch64](#test-3-default-command-with-jdk-aarch64)
- [Test 4: optimized command with JDK-aarch64](#test-4-optimized-command-with-jdk-aarch64)
- [References](#references)

## Overview
This article lists the tests I ran in [my YouTube video](https://youtu.be/o6q8zPmfVLU),  where I compared the compilation speed of my M1 Macbook Air vs my Intel Core i9 Macbook Pro.

Test 1 and Test 2, which run on JDK-x64, were done on _both_ computers.

Test 3 and Test 4, which run on JDK-aarch64, were done _only_ on my M1 Macbook Air.

## Prerequisite
Before you run the tests, make sure you have JDK and Maven installed.

To download Zulu JDK-aarch64, check out [this page](https://www.azul.com/downloads/zulu-community/?os=macos&architecture=arm-64-bit&package=jdk). On my Mac, the JDK is installed under:
```
/Library/Java/JavaVirtualMachines/zulu-16.jdk
```

(Update on 12/15/2020: the tests will be more fair if you use Zulu JDK (with the _same_ version) in _both_ M1 Mackbook Air and i9 Macbook Pro. In my orignal tests I was using non-Zulu JDK in my i9 Macbook Pro. Earlier today I installed Zulu Java 8 on _both_ computers and tested again, and the results were highly consistent with the final result I showed in my video)

Next, checkout a Java repo, like "Apache Commons Lang".
```
cd
mkdir -p src/apache
cd src/apache
git clone https://github.com/apache/commons-lang.git
cd commons-lang
```
## Test 1: default command with JDK-x64

I ran this command on _both_ my M1 Macbook Air and my i9 Macbook Pro.

Here I assume your `$JAVA_HOME` points to JDK-x64.

Run the following to compile the repo.
```
mvn clean compile
```

If you get the `Too many files with unapproved license` error, try the following to get around it:
```
mvn clean -Drat.skip=true compile
```

You will see how long the compilation takes from the output of the command.

## Test 2: optimized command with JDK-x64

I ran this command on _both_ my M1 Macbook Air and my i9 Macbook Pro.

```
MAVEN_OPTS="-XX:+TieredCompilation -XX:TieredStopAtLevel=1" \
mvn clean -T 1C -Drat.skip=true compile -offline
```

Explanations:
- `-XX:+TieredCompilation -XX:TieredStopAtLevel=1`: speeds up JVM startup.
- `-T 1C`: use 1 thread per available CPU core
- `-offline`: do not download from remote repos.

## Test 3: default command with JDK-aarch64

Now you need to point `$JAVA_HOME` to JDK-aarch64. This one is only applicable to M1 chips.

```
JAVA_HOME="/Library/Java/JavaVirtualMachines/zulu-16.jdk/Contents/Home" \
mvn clean -Drat.skip=true compile
```

## Test 4: optimized command with JDK-aarch64

Now point `$JAVA_HOME` to JDK-aarch64, and add the flags that speed up the build. This one is only applicable to M1 chips.
```
JAVA_HOME="/Library/Java/JavaVirtualMachines/zulu-16.jdk/Contents/Home" \
MAVEN_OPTS="-XX:+TieredCompilation -XX:TieredStopAtLevel=1" \
mvn clean -T 1C -Drat.skip=true compile -offline
```

## References
- [How to Speed up Your Maven Build](https://www.jrebel.com/blog/how-to-speed-up-your-maven-build)
- [JEP 391: macOS/AArch64 Port](https://bugs.openjdk.java.net/browse/JDK-8251280)
