# Compose的使用

[入门教程](https://blog.csdn.net/shop_and_sleep/article/details/124569994?spm=1001.2014.3001.5501)

[官方入门教程](https://developer.android.google.cn/develop/ui/compose/setup?hl=zh-cn)

## 环境配置

## 语法基础

还是以Kotlin为主，因此必须掌握Kotlin语法

### @Preview

 方便预览，但是必须使用无参数的可组合函数

```kotlin
@Preview(showBackground = true)
@Composable
fun GreetingPreview() {
    KotlinDemoTheme {
        Greeting("Android")
    }
}
```

### @Composable

```kotlin
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Text(
            text = "Hello $name!",
            modifier = modifier
    )
}
```

## Modifier

用于修改组件行为和外观的不可变对象