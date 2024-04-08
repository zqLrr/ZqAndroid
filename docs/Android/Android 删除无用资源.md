# Android 删除无用资源

## Res资源类型整理

| 资源          | type                | 目录                  | 说明                                                         |
| ------------- | ------------------- | --------------------- | ------------------------------------------------------------ |
| anim          | anim                | res/anim/.xml         | 补间动画                                                     |
| animator      | animator            | res/animator/.xml     | 属性动画                                                     |
| color         | color               | res/color/.xml        | [颜色状态列表](https://developer.android.com/guide/topics/resources/color-list-resource?hl=zh-cn) |
| drawable      | drawable            | res/drawable/         | [位图文件](https://developer.android.com/guide/topics/resources/drawable-resource?hl=zh-cn) |
| drawablecolor | drawable            | res/values/color.xml  | 使用drawable生成的color文件，是可绘制资源的一种，专门抽出是因为Reasource有单独处理 |
| mipmap        | mipmap              | res/mipmap            | 适用于不同启动器图标密度的可绘制对象文                       |
| layout        | layout              | res/layout/.xml       | 定义界面布局                                                 |
| menu          | menu                | res/menu/.xml         | 用于定义应用菜单的xml文件                                    |
| raw           | raw                 | res/raw               | 需以原始形式保存的任意文件 比如.mp4                          |
| array         | array               | res/value/arrays.xml  | 用于资源数组（[类型化数组](https://developer.android.com/guide/topics/resources/more-resources?hl=zh-cn#TypedArray)） |
| color         | color               | res/value/colors.xml  | 用于[颜色值](https://developer.android.com/guide/topics/resources/more-resources?hl=zh-cn#Color) |
| dimen         | dimen               | res/value/dimens.xml  | `dimens.xml` 用于[维度值](https://developer.android.com/guide/topics/resources/more-resources?hl=zh-cn#Dimension) |
| string        | string              | res/value/strings.xml | `strings.xml` 用于[字符串值](https://developer.android.com/guide/topics/resources/string-resource?hl=zh-cn) |
| style         | style<br/>styleable | res/value/styles.xml  | `styles.xml` 用于[样式](https://developer.android.com/guide/topics/resources/style-resource?hl=zh-cn) |
| xml           | xml                 | res/xml/.xml          | 可在运行时通过调用 `Resources.getXML()` 读取的任意 XML 文件。 |
| font          | font                | res/font              | 带有扩展名的字体文件（例如 TTF、OTF 或 TTC）                 |

## Asset资源

> 可以拿到原始的资源名称和资源目录

## 方案原理

### 所有资源的索引文件_R.txt：

```bash
/app/build/intermediates/runtime_symbol_list/debug/R.txt
```

格式为：数据类型 资源分类 资源名称 资源id

```xml
<!--举例-->
int anim abc_fade_in 0x7f010000
```

### *processRes Task* 的产物AP_ 文件:

> AP_ 文件 是processDResources 命令下的产物

解压后主要有以下三个文件：

1. `AndroidManifest.xml`:合并的AndroidManifest文件
2. `res/*` ：合并后的所有res文件
3. `resources.arsc`:resources.arsc是Android编译后生成的产物，主要是用来建立资源映射关系

> [详细介绍](https://juejin.cn/post/6844903911602683918)

merge.xml：

```bash
/intermediates/incremental/debug/mergeDebugResources/merger.xml
```

### 通过插桩hookgetResources().getIdentifier()

## 方案

### 方案流程

1.通过R.txt文件获取所有的资源

2.在AndroidManifest.xml查找引用的资源

3.web.jar 通过R.java文件和resources.arsc 查找到Java文件中引用的资源

* 若是layout, array,style等，会接着在xml找使用到的资源

4.1减去2，3步中使用到的资源，就是无用资源，生成app/build/unuse_res.txt文件中

5.根据merge.xml拿到各个aar中的res资源，判断res资源是否在无用资源中，最后得到需要删除的无用资源

6.生成无用资源删除报告

7.通过python脚本删除扫描出来的无用资源

8.通过插桩getResources().getIdentifier() ,来hook通过资源name字符串来获取id的情况，上线观察

### 方案使用方式

下载地址：https://gitlab.weibo.cn/zhangqian20/unres

CheckResUnuse2File.groovy

1.脚本使用方式：

需要groovy运行环境

* 首先先在本地切到dev分支编译微博源码

  ./gradlew clean app:assembleDebug

* 修改source_moudle 中添加打开的模块：如: Arrays.asList("allmodules:sdk", "allmodules:main")

* 运行CheckResUnuse2File.groovy

  ./groovy CheckResUnuse2File.groovy [微博工程目录]  [报告生成文件]

  ```java
  ./groovy CheckResUnuse2File.groovy Android/Weibo/Weibo_Project  GroovyTest/src/result.html
  ```

2.删除脚本使用方式

需要python运行环境

python3 -m delete_res_in_weibo.py--file_path /Users/zhangqian20/Project/JAVA/GroovyTest/public/unuseres
--res_path /Users/zhangqian20/Project/Android/Weibo/Weibo_Project
--delete true

3.上线运行

将删除的资源做一个兜底，然后上线验证是否有通过字符串拼接name获取id。

# 注意

1.getResources().getIdentifier()  拼接的id,不是name的全名——hook住



2.layout 中id资源并没有排出—— 编译时可以检查出来

3.Tool:text 中引用的资源未扫描到 ——不打进包里

4.Widget中的资源会放在assert的json资源下——不会引起崩溃

5.ci 自动化测试中需要使用的资源

6.Assert 中引用的资源，会放在json文件中



# 无用资源删除需要Hook的白名单





## photoalbum,extlib_ui,wboxgame误删资源：

drawable_weixin_brush_pic

在这个文件中:Weibo_Project/mediaproducer/assets/photoalbum/local_graffiti.json

