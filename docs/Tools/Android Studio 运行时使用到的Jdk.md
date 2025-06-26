# [Android Studio 运行时使用到的Jdk](https://developer.android.com/build/jdks?hl=zh-cn#kts)

### AS 的jdk 查询路径如下：

在AS新版本中， 不支持配置JDK,会按照以下路径查找JDK版本。

Android Studio 的启动脚本会按以下顺序查找 JVM：

1. `STUDIO_JDK` 环境变量
2. `studio.jdk` 目录（在 Android Studio 发行版中）
3. Android Studio 发行版中的 `jbr` 目录（JetBrains 运行时）。推荐。
4. `JDK_HOME` 环境变量
5. `JAVA_HOME` 环境变量
6. `PATH` 环境变量中的 `java` 可执行文件

## Gradle 的JDK配置

### Android Studio 中的 Gradle JDK 配置

如需修改现有项目的 Gradle JDK 配置，请依次前往 **File**（或在 macOS 上依次前往 **Android Studio**） **> Settings > Build, Execution, Deployment > Build Tools > Gradle**，打开 Gradle 设置。**Gradle JDK** 下拉菜单包含以下可供选择的选项：

- 宏（例如 `JAVA_HOME` 和 `GRADLE_LOCAL_JAVA_HOME`）
- `vendor-version` 格式的 JDK 表条目（例如 `jbr-17`），存储在 [Android 配置文件](https://developer.android.com/studio/intro/studio-config?hl=zh-cn#file_location)中
- 下载 JDK
- 添加特定 JDK
- 从操作系统的默认 JDK 安装目录中本地检测到的 JDK

## 终端中的Gradle JDK配置

同环境变量中的JDK配置

## 哪个 JDK 会编译我的 Java 源代码？

默认是Gradle的JDK 版本，为了保持一致，可以设置toolchain

```groovy
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}
```



