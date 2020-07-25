# 第三十三章 Windows应用程序

##### 本章要点：
- XAML概念
- 空间
- 已编译的数据绑定
- 导航
- 布局面板

##### 本章源代码
[本章源代码](https://github.com/ProfessionalCSharp/ProfessionalCSharp7)
> https://github.com/ProfessionalCSharp/ProfessionalCSharp7

##### 本章示例文件
- IntroXAML
- ControlsSample
- ParallaxViewSample
- PageLayouts
- NavigationSample
- LayoutPanels

## Windows应用程序简介
windows应用程序使用.NET Core，但是Web应用程序和ASP.NET Core有很大的区别。Windows应用程序只能在Windows平台上运行。这些应用程序不仅仅适用于桌面，也适用于Xbox，HoloLens和Raspberry PI。

### Windows运行库
Windows应用程序还利用了Windows运行库（WindowsRuntime，WinRt）。Windows运行库是使用C++和新一代Com对象创建的平台。因此，Windows运行不仅适用于.NET应用程序，也是用于C++he javaScript创建的应用程序。为了从这些不同的平台上访问Windows运行库，还创建了兼容层：语言投影。通过语言投影，Windows运行库提供的API看起来像.NET API。

语言投影将Windows运行库类型映射到.NET 类型上。例如，在文件Windows.Foundation，Foundation Contract.winmd中，名称空间Windows.Foundation.Collection包含IIterable和IIterator接口。这些接口看起来非常类似于.NET接口IEnumerable和IEnumerator。实际上，他们是语言投影自动映射的。

Windows运行库不包含任何集合。相反，集合是由使用Windows运行库的不同平台实现的，例如C++/javaScript和.NET。


#### HELLO Windows
==注意：要使用.NET Standard 2.0库，应用程序需要构建版本16299作为最小构建好==

#### 应用程序清单文件
可以使用项目属性更改构建目标和最小版本号。Windows应用程序还有另一个重要的打包配置。文件Package.appxmanifest可以使用Package Manifest Editor配置。

- Application设置中，可以配置应用程序的显示名称、默认语言、支持设备的旋转以及定期的自动块更新。

- Visual Assets选项卡中，可以配置应用程序的所有不同图标；给不同的磁贴大小，不同的设备分辨率、闪屏和Windows Store的包标识指定磁贴图像。

- Capabilities选项卡中的设置允许选择应用程序所需要的功能。默认选择的功能是Internet客户端功能。如果需要对服务器执行网络请求，则需要打开此功能。其他功能允许访问位置信息，访问麦克风和摄像头，访问文件系统上的不同文件夹，如音乐库、图片库或视频库。

- Declarations设置可以添加Windows需要了解的应用程序特性。例如，当共享一个应用程序的数据时，Windows显示接受共享数据的应用程序。为此，应用程序需要注册为Share Traget。该选项卡中包含将应用程序注册为共享目标，通过协议或文件类型扩展来激活，在应用程序服务之间进行通信，以及更多的申明设置。

- Content URLs选项卡允许在应用程序中进行深度链接。在这里，可以指定在应用程序中打开页面的URL。最后，使用Packaging选项卡，可以配置包名、版本和关于发布者的信息。


#### 应用程序启动
应用程序的入口点HelloWindows.APP在应用程序清单中定义，APP类派生自Application基类，包含处理Suspending时间的实现代码。
```csharp
sealed partial class App:Application
{
    public App()
    {
        this.InitializeComponent();
        this.Suspending +=OnSuspending;
    }
}
```

OnLaunched方法被重写。当用户启动应用程序时，将调用此方法。如应用程序清单所述，启动应用程序有不同的方式。应用程序可以直接由用户启动，也可以在用户从另一个应用程序共享数据时启动。对于此类场景，可以重写不同的激活方法。

当用户启动应用程序时，也存在不同的场景。可以重新启动应用程序，或者如果应用程序以前被挂起，则可以重新启动。当应用程序启动时，没有当前的Frame，就会创建一个新的Frame。Frame可以用于页面之间的导航。页面包含UI控件，Frame提供页面之间的盗汗。实例化Frame后，通过将Navigate方法调用到MainPage，进行导航。

```csharp
Frame rootFrame=Window.Current.Content as Frame;
if(rootFrame==null)
{
    rootFrame=new Frame();
    rootFrame.NavigationFailed+=OnNavigationFailed;
}
//....
```

#### 主页
下面向MainPage添加一个带有消息的按钮。用户界面使用XAML定义，这是一种用某种功能扩展XML的语言。对于按钮控件，ContentS设置为显示一个简单的字符串，Click事件分配给OnButtonClicked事件处理程序。属性HorizontalAlignment、VerticalAlignment和Margin用来使按钮沾满网格中除了按钮页边距之外的全部控件。（HelloWindows/MainPage.XAML）
```csharp
//文件 MainPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Button Content="你好，赵峰！" Click="OnButtonClicked" HorizontalAlignment="Stretch"  VerticalAlignment="Stretch" Margin="20"/>
    </Grid>
```
```csharp
//文件 MainPage.xaml.cs

        private async void OnButtonClicked(object sender, RoutedEventArgs e)
        {
            await new MessageDialog("Hello,Windows").ShowAsync();
        }
```

Windows应用程序不仅需要在运行之前构建，还需要部署。在Configuration Manager（Build|Configuration Manager）中设置Deploy配置时，就可以在Visual Studio构建时自动完成部署。

如果在构建时没有部署应用程序，就需要在构建后部署它，方法是在Solution Explorer中选择项目，在上下文菜单中使用Deploy选项：或者可以选择Build|Deploy Solution，使用VS部署解决方案中的所有项目。

<hr>
<br>

## XAML
