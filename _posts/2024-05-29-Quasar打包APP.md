---
layout: post   	
catalog: true 	
tags:
    - Quasar
---

# 1. Capacitor打包APP

## 1.1. 准备环境

1、下载好Android studio（安装版）

2、准备好与该Android studio相适配的jdk版本

3、在Android studio中下载Android SDK，选择与jdk版本相匹配的sdk版本，需要下载platform、tools，[通过Android studio下载的sdk中没有tools文件夹的解决办法](https://www.cnblogs.com/star12111/p/12789245.html)

4、配置环境变量`setx ANDROID_SDK_ROOT "F:\AndroidWork\sdk"`，`setx path "%ANDROID_SDK_ROOT%\platform-tools"`，`setx path "%ANDROID_SDK_ROOT%\tools"`

5、默认gradle版本、gradle插件版本即可，只需修改源，[Gradle Wrapper 国内源 - Seepine's Blog](https://seepine.com/dev/gradle/wrapper/)
## 1.2. 报错

```
* What went wrong:
Unable to start the daemon process.
This problem might be caused by incorrect configuration of the daemon.
For example, an unrecognized jvm option is used.
Please refer to the User Manual chapter on the daemon at https://docs.gradle.org/8.0.2/userguide/gradle_daemon.html
Process command line: E:\jdk-17.0.11+9\bin\java.exe --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.lang.invoke=A
LL-UNNAMED --add-opens=java.prefs/java.util.prefs=ALL-UNNAMED --add-opens=java.base/java.nio.charset=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/ja
va.util.concurrent.atomic=ALL-UNNAMED -Xmx1536m -Dfile.encoding=UTF-8 -Duser.country=CN -Duser.language=zh -Duser.variant -cp C:\Users\Hou_Yi\.gradle\wrapper\dists\gradle-8.0.2-all
\5owlw31sddu3apboq9xcnpod7\gradle-8.0.2\lib\gradle-launcher-8.0.2.jar org.gradle.launcher.daemon.bootstrap.GradleDaemon 8.0.2
Please read the following process output to find out more:
-----------------------
Error occurred during initialization of VM
Could not reserve enough space for 1572864KB object heap
```

gradle.properties 中设置`org.gradle.jvmargs=-Xmx512m`