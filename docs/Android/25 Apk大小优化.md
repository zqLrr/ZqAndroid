# 25 Apk大小优化

正常的Apk 应包含以下文件

* META-INF/ ：包含 CERT.SF 和 CERT.RSA 签名文件，以及 MANIFEST.MF 清单文件。

  用于保存应用程序的签名和校验信息，以保证程序的完整性。生成apk包时，系统会对包中的所有内容做一次校验,然后将结果保存在这里。设备在安装这一应用程序时，还会对内容在做一次校验，并和META-INF的值进行比对，以避免程序包被恶意篡改，这就是为什么只简单通过替换文件二次打包，程序不能正常安装的原因。

* assets/ ：包含应用的资源；应用可以使用 AssetManager 对象检索这些资源。

* res/：包含项目res文件夹下的资源，例如布局资源、字符串资源、图片资源等。

- lib/：包含特定于处理器软件层的已编译代码。此目录包含每种平台类型的子目录，如 armeabi 、

armeabi-v7a 、 arm64-v8a 、 x86 、 x86_64 和 mips 。

* AndroidManifest.xml ：包含核心 Android 清单文件。此文件列出了应用的名称、版本、访问权限和引用的库文件。该文件使用 Android 的二进制 XML 格式。
* classes.dex ：包含以 Dalvik/ART 虚拟机可理解的 DEX 文件格式编译的类。
* resources.arsc ：包含已编译的资源。此文件包含 res/ 文件夹的所有配置中的 XML 内容。打包工具会提取此 XML 内容，将其编译为二进制文件形式，并压缩内容。此内容包括语言字符串和样式，以及未直接包含在 resources.arsc 文件中的内容（例如布局文件和图片）的路径。
  



Lib:so  :拼多多少很多

Assert :应用的资源，打包不会被压缩，不会生成R.java文件

res: 拼多多只有几M

Dex：Java代码文件



Java代码优化：

插件化:次要功能需要使用的时候再动态加载下来  

shadow实现代理和反射

减少Apk体积的方式：

1.将图片转换成webp

2.去除多语言

3.so 只保留32位

4.移除和压缩无用资源

* Proguard

  配合minifyEnabled为true，通过shrinkResources压缩无用资源 xml会压缩到只剩头部

  开启混淆，删除未使用的结构

* Lint 检查无用资源并删除

5.减少第三方库

6.AndResGuard微信资源压缩方案  压缩资源路径和减小资源可读性

7.动态下发so库

# [Apk打包流程](https://mp.weixin.qq.com/s?__biz=MzA5MzI3NjE2MA==&mid=2650265470&idx=1&sn=b0a8a269958339a9e33b6777572c97bb&chksm=88632611bf14af079ce3ee1df90dc4e830aa3c106a12def378a5c957ef948f0a1802cd51d404&scene=27)

1.处理AIDL文件

2.处理资源文件 

res中的文件要生成R.java文件

3.编译源码和第三方库

生成.class文件后再生成dex文件供Java虚拟机使用

4.ApkBuilder 生成apk或aab

5.签名

