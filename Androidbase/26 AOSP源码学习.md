# 26 AOSP源码学习

AOSP[入门](https://juejin.cn/post/7202634945171537977)

## 1.Project

在 App 的开发中，我们要去打渠道包，根据不同应用市场的要求打包出不同的 apk 包。

同样的道理，Android 的系统源码，经过简单的配置，可以打包出不同的系统镜像，用于不同的产品。例如小米 12s，小米12s pro，小米12s ultra 均源于骁龙8+平台。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a76b63d3ef7498cb52b95ef880cb528~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

在[build/target](build/target ) 或者device目录下可以看到AOSP预置的Project



# AOSP源码目录介绍

1. build：包含了构建 Android 系统的相关工具和脚本。
2. cts：包含了 Compatibility Test Suite (CTS) 的相关代码和测试用例，用于验证设备的兼容性。
3. dalvik：包含了 Dalvik 虚拟机的源代码，Dalvik 是 Android 早期使用的 Java 虚拟机。
4. development：包含了开发工具和库的源代码，如 adb（Android 调试桥）、Android Studio 插件等。
5. frameworks：是 Android 系统的核心框架层，包含了许多 Android 的 API、服务和组件的实现代码。
6. hardware：包含了硬件抽象层（Hardware Abstraction Layer，HAL）的实现代码，用于提供与硬件交互的接口。
7. libs：包含了一些通用的库和组件，如 SQLite 数据库、OpenGL ES 等。
8. packages：包含了 Android 系统的各种应用程序包，如浏览器、联系人、电话等。
9. system：包含了 Android 系统的核心组件和服务，如 Activity Manager、Package Manager、Window Manager 等。
10. tools：包含了 Android 开发和构建工具的源代码，如 ADB、Android SDK 工具、ProGuard 等。

