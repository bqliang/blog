
![](imgs/Compose%E8%87%AA%E5%AE%9A%E4%B9%89%E4%B8%BB%E9%A2%98/Compose%E8%87%AA%E5%AE%9A%E4%B9%89%E4%B8%BB%E9%A2%98.png)

## 1. 示例介绍 

[本文示例源代码](https://github.com/RugerMcCarthy/BloomCompose)

我们通过 #AndroidDevChallange 挑战赛第三周题目作为示例来看看在实际项目中如何进行应用。在不同主题方案下背景颜色、文字颜色与图片资源都是不同的。值得注意的是对于所有文本也可以通过主题完成字体样式的配置，所要实现的目标效果如下图所示。

![](imgs/Compose%E8%87%AA%E5%AE%9A%E4%B9%89%E4%B8%BB%E9%A2%98/Compose%E8%87%AA%E5%AE%9A%E4%B9%89%E4%B8%BB%E9%A2%98demo%E6%95%88%E6%9E%9C.png)

## 2. 配置颜色样式

首先，我们来学习如何配置颜色样式，仅需要根据主题的不同生成其对应的 colors 即可。根据项目需求，我们进行以下的配置。

```kotlin
private val BloomLightColorPaltte = lightColors(
    primary = pink100,
    secondary = pink900,
    background = white,
    surface = white850,
    onPrimary = gray,
    onSecondary = white,
    onBackground = gray,
    onSurface = gray,
)

private val BloomDarkColorPaltte = darkColors(
    primary = green900,
    secondary = green300,
    background = gray,
    surface = white150,
    onPrimary = white,
    onSecondary = gray,
    onBackground = white,
    onSurface = white850
)

@Composable
fun BloomTheme(theme: BloomTheme = BloomTheme.LIGHT, content: @Composable() () -> Unit) {
    CompositionLocalProvider(
        LocalWelcomeAssets provides if (theme == BloomTheme.DARK) WelcomeAssets.DarkWelcomeAssets else WelcomeAssets.LightWelcomeAssets,
    ) {
        MaterialTheme(
            colors = if (theme == BloomTheme.DARK) BloomDarkColorPaltte else BloomLightColorPaltte,
            typography = Typography,
            shapes = shapes,
            content = content
        )
    }
}
```

在我们的视图所需要 Color 的地方配置即可。

```kotlin
Text(
    text = "Beautiful home garden solutions",
    textAlign = TextAlign.Center,
    color = MaterialTheme.colors.onPrimary // I'm here
)
```

## 3. 配置字体样式

我们接着来学习如何配置字体样式。MaterialTheme 方法的第二个参数 typography 表示的就是所配置的字体，只是这个 Typography 是 Android Studio 默认帮你配制的。

```kotlin
MaterialTheme(
  	colors = colors,
  	typography = Typography,
  	shapes = Shapes,
  	content = content
)
```

如果是新建的项目，Android Studio 会在 ui.theme 包下生成 Type.kt，其中包含了 Typography 的实现，名为 Typography 的变量间接调用 Typography 类构造函数。

```kotlin
val Typography = Typography(
    body1 = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp
    )
)
```

再回到 MaterialTheme 实现，可以发现 typography 提供给 LocalTypography 这个 CompositionLocal 实例了，那么我们在项目中如何使用这个特殊字体也不需要额外的介绍了，这与 colors 是完全一样的。

```kotlin
@Composable
fun MaterialTheme(
    colors: Colors = MaterialTheme.colors,
    typography: Typography = MaterialTheme.typography,
    shapes: Shapes = MaterialTheme.shapes,
    content: @Composable () -> Unit
) {
    val rememberedColors = remember {
        colors.copy()
    }.apply { updateColorsFrom(colors) }
    val rippleIndication = rememberRipple()
    val selectionColors = rememberTextSelectionColors(rememberedColors)
    CompositionLocalProvider(
        LocalColors provides rememberedColors,
        LocalContentAlpha provides ContentAlpha.high,
        LocalIndication provides rippleIndication,
        LocalRippleTheme provides MaterialRippleTheme,
        LocalShapes provides shapes,
        LocalTextSelectionColors provides selectionColors,
        LocalTypography provides typography // I'm here~
    ) {
        ProvideTextStyle(value = typography.body1, content = content)
    }
}
```

既然懂得了原理，我们仅需要根据项目实际需求配置字体样式即可，既然 Android Studio 帮助生成 Type.kt，说明是官方希望我们将字体样式配置在这个文件中的。这是一种规范，但也可不遵守。

值得注意的是由于每种字体都会有不同的粗细风格，我们在字体样式配置时需要指明字体种类与粗细风格。

```kotlin
val nunitoSansFamily = FontFamily(
    Font(R.font.nunitosans_light, FontWeight.Light),
    Font(R.font.nunitosans_semibold, FontWeight.SemiBold),
    Font(R.font.nunitosans_bold, FontWeight.Bold)
)

val bloomTypography = Typography(
    h1 = TextStyle(
        fontSize = 18.sp,
        fontFamily = nunitoSansFamily,
        fontWeight = FontWeight.Bold
    ),
    h2 = TextStyle(
        fontSize = 14.sp,
        letterSpacing = 0.15.sp,
        fontFamily = nunitoSansFamily,
        fontWeight = FontWeight.Bold
    ),
    ....
)
```

使用的话就很简单了，我们仅需将字体样式传入 MaterialTheme 即可。

```kotlin
@Composable
fun BloomTheme(theme: BloomTheme = BloomTheme.LIGHT, content: @Composable() () -> Unit) {
    MaterialTheme(
         colors = if (theme == BloomTheme.DARK) BloomDarkColorPaltte else BloomLightColorPaltte,
         typography = bloomTypoGraphy,
         shapes = shapes,
         content = content
    )
}
```

在我们的视图组件中使用 style 参数进行配置即可。

```kotlin
Text(
    text = "Beautiful home garden solutions",
    textAlign = TextAlign.Center,
    style = MaterialTheme.typography.subtitle1, // I'm here
    color = MaterialTheme.colors.onPrimary
)
```

## 4. 配置自定义资源

有时我们可能需要根据主题的不同使用不同的多媒体资源，例如图片、视频、音频等等。通过查阅 MaterialTheme 参数列表我们没有发现可以进行配置的参数。难道 Jetpack Compose 不具备这样的能力？答案当然是否定的，Android 团队已经充分考虑了各种场景，只是针对于这种需求而言，我们需要进行额外的定制扩展。就是通过定制 CompositionLocal 方式来实现图片资源的扩展，根据主题的不同选用其对应的多媒体资源。

```kotlin
open class WelcomeAssets private constructor(
    var background: Int,
    var illos: Int,
    var logo: Int
) {
    object LightWelcomeAssets : WelcomeAssets(
        background = R.drawable.ic_light_welcome_bg,
        illos = R.drawable.ic_light_welcome_illos,
        logo = R.drawable.ic_light_logo
    )

    object DarkWelcomeAssets : WelcomeAssets(
        background = R.drawable.ic_dark_welcome_bg,
        illos = R.drawable.ic_dark_welcome_illos,
        logo = R.drawable.ic_dark_logo
    )
}

internal var LocalWelcomeAssets = staticCompositionLocalOf {
  	WelcomeAssets.LightWelcomeAssets as WelcomeAssets
}
```

于此同时，我们还希望能够在视图中仍通过 MaterialTheme 来访问我们的图片资源，那么则可以通过 Kotlin 扩展属性的特性进行实现(扩展属性是没有幕后字段的，只能委托其他实例)。值得注意的是，CompositionLocal 只能在 composable(带有 Composable 注解的 lambda)中使用，所以我们需要为这个属性获取添加 @Composable 与 @ReadOnlyComposable 注解。

```kotlin
val MaterialTheme.welcomeAssets
    @Composable
    @ReadOnlyComposable
    get() = LocalWelcomeAssets.current
```

这样我们在视图中就可以仍然通过 MaterialTheme 来获取扩展的图片资源了。

```kotlin
Image(
     painter = rememberVectorPainter(image = ImageVector.vectorResource(id = MaterialTheme.welcomeAssets.background)),
     contentDescription = "weclome_bg",
     modifier = Modifier.fillMaxSize()
)
```

既然了解了图片的主题配置，其他多媒体资源的主题配置是完全相同的，请充分发挥你的想象力。