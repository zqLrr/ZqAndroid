# 8Android混淆——proguard

> ProGuard is a free Java class file shrinker, optimizer, obfuscator, and preverifier.

ProGurad是一个免费的Java文件的压缩器、优化器、混淆器和预验证器。

* The ***shrinking step*** detects and removes unused classes, fields, methods, and attributes.
* The ***optimizer step*** optimizes bytecode and removes unused instructions.
* The ***obfuscation step*** renames the remaining classes, fields, and methods using short meaningless names.
* The final ***preverification step*** adds preverification information to the classes, which is required for Java Micro Edition and for Java 6 and higher.（Android 不需要）

## 入口点

> 为了使Proguard知晓哪些代码应该保留、丢弃或者混淆，需要为代码制定一个或多个入口点.

* **shrinking**:通过seeds,递归确定哪些类、类成员要使用，哪些要废弃
* **optimizer**:进一步优化，不是入口点的类、方法、成员将会设置成`private`、`static`、`final`,无用的参数会被移除掉，一些方法将会改为内联函数。
*  **obfuscation**: 重命名不是入口点的类和类名，入口点的存在保证了可以通过原始名称访问到它。

## Proguard使用说明

[官网使用手册](https://www.guardsquare.com/manual/configuration/usage)

## R8

> 

## Android Proguard使用

> 重点是如何将配置文件部署在Android project，以及输出文件的使用

1.开启混淆

```groovy
  buildTypes {
        debug {
          //开启混淆检查
            minifyEnabled true
            // Enables resource shrinking, which is performed by the
            // Android Gradle plugin.
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
```

2. 在`proguard-rules.pro`文件中添加配置信息，

```Gr
#常用配置
-optimizationpasses 5
# 混淆时不使用大小写混合，混淆后的类名为小写
-dontusemixedcaseclassnames
# 指定不去忽略非公共库的类
-dontskipnonpubliclibraryclasses
# 指定不去忽略非公共库的成员
-dontskipnonpubliclibraryclassmembers
# 混淆时不做预校验
-dontpreverify
# 混淆时不记录日志
-verbose
# 代码优化
-dontshrink
# 不优化输入的类文件
-dontoptimize
# 保留注解不混淆
-keepattributes *Annotation*,InnerClasses
# 避免混淆泛型
-keepattributes Signature
# 保留代码行号，方便异常信息的追踪
-keepattributes SourceFile,LineNumberTable
# 混淆采用的算法
-optimizations !code/simplification/cast,!field/*,!class/merging/*

# dump.txt文件列出apk包内所有class的内部结构
-dump proguard/class_files.txt
# seeds.txt文件列出未混淆的类和成员
-printseeds proguard/seeds.txt
# usage.txt文件列出从apk中删除的代码
-printusage proguard/unused.txt
# mapping.txt文件列出混淆前后的映射
-printmapping proguard/mapping.txt

# Android类
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class * extends android.view.View

# support
-keep class android.support.** {*;}
-keep public class * extends android.support.**
-dontwarn android.support.**
-keep interface android.support.** { *; }

# androidx
-keep class androidx.** {*;}
-keep interface androidx.** {*;}
-keep public class * extends androidx.**
-dontwarn androidx.**

# 自定义控件
-keep public class * extends android.view.View{
    *** get*();
    void set*(***);
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}

# R文件
-keep class **.R$* {
 *;
}

# webview
-keepclassmembers class android.webkit.WebView {
   public *;
}
-keepclassmembers class * extends android.webkit.WebViewClient {
    public void *(android.webkit.WebView, java.lang.String, android.graphics.Bitmap);
    public boolean *(android.webkit.WebView, java.lang.String);
}
-keepclassmembers class * extends android.webkit.WebViewClient {
    public void *(android.webkit.WebView, *);
}

# 按键等事件
-keepclassmembers class * {
    void *(**On*Event);
}
# onClick
-keepclassmembers class * extends android.app.Activity{
    public void *(android.view.View);
}

#枚举类型
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

# native方法
-keepclasseswithmembernames class * {
    native <methods>;
}
# View构造方法
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
# Parcelable
-keep class * implements android.os.Parcelable {
  public static final android.os.Parcelable$Creator *;
}
# Serializable
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}

```

3. 执行./gradlew 脚本

```bash
./gradlew app:assembledebug
./gradlew app:assembleRelease
```

4. 在【appMoudle】->build->outputs->mappings中生成以下四个文件

   * `mapping.txt`: 混淆的前后关系
   * `seeds.txt`：列出了没有被混淆的类和成员
   * `usage.txt`：列出了源代码中被删除在apk中不存在的代码
   * `configuration.txt`:自定义和官方定义的所有proguard的代码

5. 使用Android stuido->build->Apk Analyse:来分析生成的dex 文件

   [官方文档]()



