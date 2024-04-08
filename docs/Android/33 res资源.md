# Android 资源

## 主题和样式资源：

### 系统主题

写法：@android:style/Theme.Black.NoTitleBar.Fullscreen

也可简写成：android:style/Theme.Black.NoTitleBar.Fullscreen

​						android:Theme.Black.NoTitleBar.Fullscreen

尽量使用官方推荐写法

### Material Design 主题应用

写法：

Theme.Appcompat.XXX

来源：androidx.appcompat库中

### 主题继承关系

可以使用parent

```xml
<resources>
    <style name="GreenText" parent="TextAppearance.AppCompat">
        <item name="android:textColor">#00FF00</item>
    </style>
</resources>
```

也可以使用.继承,而非Parent属性，

```xml
<style name="GreenText.Large">
    <item name="android:textSize">22dp</item>
</style>
```



Theme R.styleable 来查询有哪些属性有效

Style  R.attr来查询哪些属性有效
