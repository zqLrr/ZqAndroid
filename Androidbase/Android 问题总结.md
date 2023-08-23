# Android 问题总结

## Android resource linking failed 

### 错误原因

要解决错误要先明白错误的原因是什么。从这个错误的描述可以知道这个错误是因为资源链接错误。最明显的原因就是你的xml确实有问题，错误直接指向你出错的地方，这种情况非常简单，修改错误的地方就行。如下面的错误，直接指向特定的xml，而且这个文件是我写的，xml里面直接红线错误，直接修改就行。


但出现这个错误的大部分情况都是不知道是什么原因，这是这个错误最让人头疼的地方。例如下面的错误。下面的信息有两点是有用的，一是路径是本地gradle（不是AS的里面.gradle）的缓存路径./gradle/caches。至于transforms-2这个目录可以不太关心，有时可能报别的目录。还有一个有用的信息是core-1.7.0。这个包是androix的核心包，这个信息是非常有用的，错误是发生在androidx上面，这已经帮我们大大缩小范围。

使用代码获取详细信息：

上图的信息可能不够全面，而且不好复制粘贴错误，可以用下面的代码获取详细信息：

 ./gradlew build --stacktrace  

可以分析错误信息获取有用的信息。

### 1.通常（通用）解决方案：删除cache

这个错误有可能由于缓存的原因造成奇怪的错误。下面的错误信息是不固定的，报的路径是.gradle/caches/transforms-2，报哪个路径就删哪个。把AS的build和AS的.gradle同时删除，然后invaild and restart。有可能就可以解决错误。

如果这种方式没有效果，那么就要考虑下面具体的问题。

### 2.具体一：SDK问题

compileSdkVersion和buildToolsVersion的大版本要保持一致，例如compileSdkVersion是28，那么buildToolsVersion一定要是28.xx.xx。不一致会报这个错误。

compileSdkVersion 28
buildToolsVersion "28.0.3"


### 3.具体二：appcompat导致的问题（版本问题）

例如下面这个错误。由于库的版本问题导致的错误。

虽然我们可以分析出core-1.7.0是androidx的核心库。但还是不知道具体是什么原因。这是我实际遇到的，最后发现是appcompat的版本问题导致的。原来使用的是1.4.1版本，这个版本太高了，换成1.2.0就可以了。constraintlayout虽然不是这个问题的原因，但是这个版本太高会导致预览没有效果。

implementation 'androidx.appcompat:appcompat:1.2.0'
implementation 'androidx.constraintlayout:constraintlayout:2.0.4'

版本回调技巧：
先回调大版本再调小版本。例如2.0.0直接改为1.0.0，再1.1.0，1.2.0这样调，如果是版本问题，很快就可以解决。

### 4.具体三：任意库（实际无关）导致的问题

解决的思路都是类似的。以下面的错误为例子。出问题的库是material-1.6.0，在gradle里面引用了这个库，但我在代码里面没有用过这个库相关的类。这个错误实际上是莫名其妙的，因为我之前用的好好的，重新打开AS就莫名其妙出问题了。如果你没有使用这个库的类，解决办法就是把material-1.6.0这个库在gradle里面去除引用。别的错误处理都是类似的，关键是根据出问题的库缩小处理范围。

.gradle/caches/transforms-2/files-2.1/56dee953d5284ac3fce000007c35f379/material-1.6.0/res/values-v31/values-v31.xml:3:5-
但是去除引用虽然解决问题了，我就想用这个库该怎么办呢？实际上还是版本问题。把1.6.0改成1.2.0版本就可以解决了。

总结
除了compileSdkVersion和buildToolsVersion的版本问题外，绝大部分原因都是引用库的版本问题，根据出问题的库回调版本就可以