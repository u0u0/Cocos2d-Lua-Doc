# 从命令行编译 Android Studio 项目

首先，你需要正确安装 Android Studio 并能用IDE编译出apk。参考[Android 项目的编译与打包](./index.md)。

本文档已 Mac 环境为例，Windows可供参考。

## 配置 JAVA_HOME

Mac早取消了命令行自动安装JDK，需要自行去官网下载。别急，其实 Android Studio 是自带了jdk的，我们只需要指定到 Android Studio 的 jdk目录即可。

```
$vim ~/.bash_profile
```

加入

```
export JAVA_HOME=/Applications/Android\ Studio.app/Contents/jre/jdk/Contents/Home
```

重新加载 Shell 的配置文件：

```
$source ~/.bash_profile
```

## 升级项目的 gradle 配置

编辑`youproject/frameworks/runtime-src/proj.android/build.gradle`文件，两个repositories均加入`google()`到第一项。

`youproject/frameworks/runtime-src/proj.android/app/build.gradle`和`youproject/frameworks/runtime-src/proj.android/libcocos2dx/build.gradle`中的 compile 修改为 implementation。

参考[commit: fix for build from console by ./gradlew](https://github.com/u0u0/Quick-Cocos2dx-Community/commit/0d3dc53095099f9ae64a543b7ebbad6aa9626ffc)

## 运行项目下的 ./gradlew

```
$./gradlew

Download https://dl.google.com/dl/android/maven2/com/android/tools/build/gradle/3.0.1/gradle-3.0.1.pom
Download https://dl.google.com/dl/android/maven2/com/android/tools/build/gradle-core/3.0.1/gradle-core-3.0.1.pom
Download https://dl.google.com/dl/android/maven2/com/android/tools/build/gradle-api/3.0.1/gradle-api-3.0.1.pom
...
```

第一次运行会自动下载依赖包，耐心等待下载完成。正确安装会看到如下信息：

```
> Task :help

Welcome to Gradle 4.1.

To run a build, run gradlew <task> ...

To see a list of available tasks, run gradlew tasks

To see a list of command-line options, run gradlew --help

To see more detail about a task, run gradlew help --task <task>


BUILD SUCCESSFUL in 13m 30s
1 actionable task: 1 executed
```

## 配置 release key

编辑`frameworks/runtime-src/proj.android/app/build.gradle`，

加入

```
    signingConfigs {
        config {
            storeFile file(getRootDir().getPath() + "/my.key")
            storePassword 'you store key'
            keyAlias 'alias_name'
            keyPassword 'you alias key'
        }
    }
```

这里 my.key 是放在proj.android下的，`getRootDir().getPath()`会自动获取当前目录。

修改 buildTypes 的 release 选项，添加`signingConfig signingConfigs.config`，如下所示。

```
    buildTypes {
        release {
            signingConfig signingConfigs.config // added line
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

```

## 编译测试

### 编译 release 版本

```
./gradlew assembleRelease
```

log如下：

```
Starting a Gradle Daemon, 1 busy and 1 incompatible Daemons could not be reused, use --status for details

BUILD SUCCESSFUL in 25s
52 actionable tasks: 6 executed, 46 up-to-date
```

apk文件存放在：`youproject/frameworks/runtime-src/proj.android/app/build/outputs/apk/release`目录下

### 清理环境

```
./gradlew clean
```