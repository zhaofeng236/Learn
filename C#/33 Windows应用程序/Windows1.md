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
XAML不仅仅用于创建Windows应用程序，还用于Windows Presentation Foundation（WPF）和Xamarin的跨平台应用程序。

可以用XAML完成的工作都可以用C#实现，每个XAML元素都用一个类表示，因此可以从C#中访问。XAML通常用于描述对象及其属性，可以描述很深的层次结构。例如，Page包含一个Grid控件，Grid控件包含一个StackPanel和其他控件，StackPanel包含按钮和文本框控件。XAML便于描述这种层次结构，并通过XML特性或元素分配对象的属性。

XAML允许以声明的方式编写代码，而C#主要是一种命令式编程语言。XAML支持声明式定义。在命令式编程语言中，用C#定义一个for循环，编译器就使用中间语言（IL）代码创建一个for循环。在声明式编程语言中，声明应该做什么，而不是如何完成。

==注意：虽然C#不是纯粹的命令式编程语言，但使用LINQ时，也是在以声明方式编写语法，Entity Framework Core的LINQ提供程序将LINQ查询转换为SQL语句。==

XAML是一个XML语法，但它定义了XML的几个增强特性。XAML仍然是有效的XML，但是一些增强特性有特殊意义和特殊的功能，例如，在XML特性中使用花括号，对于XML这只是一个字符串，但对于XAML是一个标记扩展。

在有效使用XAML之前，需要了解这门语言的一些重要特性。本章介绍了如下XAML特性：
- 依赖属性：从外部看起来，依赖属性像正常属性。然而，他们需要更少的存储空间，实现了变更通知。
- 路由事件：从外部看起来，路由事件像正常的.NET事件。然而，通过添加和删除访问器来使用自定义事件的实现方式，就允许冒泡和隧道。
   - 事件从外部控件进入内部控件成为隧道;
   - 事件从内部控件进入外部控件成为冒泡。
- 附加属性：通过附加属性，可以给其他控件添加属性。例如，按钮控件没有属性用于把他自己定位在Grid控件的特定行和列上。在XAML中，看起来有这样一个属性。
- 标记扩展：编写XML特性需要的编码比编写XML元素少。然而，XML特性只能是字符串；使用XML元素可以编写更强大的语法。为了减少需要编写的代码量，标记扩展允许在特性中编写强大的语法。


### XAML标准
WPF、WUP和Xamarin对XAML元素使用不同的语法。例如，对于WPF和UWP，按钮由Content属性，而Xamarin的按钮有Text属性。在WPF和UWP中，可以使用StackPanel来排列多个元素。在Xamarin中，类似的控件是StackLayout。
XAML标准的实际状态请参[XAML标准](https://github.com/Microsoft/xaml-standard/)
`https://github.com/Microsoft/xaml-standard/`

### 将元素映射到类
在每个XAML元素的后面都有一个具有属性、事件、方法的类。如前面所述，可以使用C#代码或使用XAML创建UI元素。下面代码，定义了一个包含按钮控件的StackPanel。使用XML特性，按钮分配了Context属性和Click事件。Content属性只包含一个简单的字符串，而Click事件引用了方法OnButtonClick的地址。XML特性x:Name用于向按钮控件声明一个名称，该名称可以在XAML和C#代码隐藏文件中使用。
```xml
<StackPanel x:Name="stackPanel1">
  <Button Content="Click Me!" x:Name="button1" Click="OnButtonClick"/>
  <!--..-->
</StackPanel>
```
在页面的顶部，可以看到带有XML特性x:Class的Page元素。这定义了类的名称，在该类中，XAML编译器生成了部分代码。使用VS中代码隐藏文件，可以看到这个类中能修改的部分。
```xml
<Page
 x:Class="XAMLIntro.MainPage">
```
代码隐藏文件包含类MainPage的一部分（XAML编译器没有生成这个部分）。在构造函数中，调用方法InitializeComponent。InitializeComponent的实现由XAML编译器创建的。该方法加载XAML文件，并将其转换为XAML文件中的根元素指定的对象。OnButtonClick方法是之前在XAML代码中创建的按钮Click事件处理程序。这个实现打开了一个MessageDialog。

在 C#代码的Button类中创建一个新对象，并将其添加到现有的StackPanel中。下面修改了MainPage的构造函数，以创建一个新按钮，设置Content属性，并为Click事件分配一个Lambda表达式。最后，新创建的按钮添加到StackPanel的Children中。

```csharp
public MainPage()
{
    this.InitializeComponent();
    
    //创建新按钮
    var button2=new Button
    {
      Content="Created dynamically"
    };

    button2.Click+=async (sender,e)=>
      await new MessageDialog("button 2 clicked").ShowAsync();
    stackPanel1.Children.Add(button2);
}
```
XAML只是处理对象、属性和事件的另一种方式。

### 通过XAML使用定制的.NET类
要在XAML代码中使用自定义的.NET类，可以使用简单的POCO类，对类定义没有特殊要求。只需要将.NET名称空间添加到XAML声明中。为了演示这一点，下面设计一个具有FirstName和LastName属性的简单Person类。

```csharp
public class Person
{
    public string FirstName{get;set;}
    public stirng LastName{get;set;}
    public override string ToString()=>$"{FirstName} {LastName}";
}
```
在XAML中定义了一个名为datalib的XML名称空间别名，它映射到程序集DataLib中的.NET名称空间DataLib。有了这个别名，现在就可以把别名作为元素的前缀，来使用这个名称空间中的所有类。

在XAML代码中添加一个列表框，其中包含Person类型的项。使用XAML特性，可以设置属性FirstName和LastName的值。运行应用程序时，ToString方法的输出显示在列表框中。
```xml
<Page
    x:Class="WinTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:WinTest"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <StackPanel x:Name="stackPanel1">
        <Button Content="Click Me ! " x:Name="button1" Click="OnButtonClicked"/>
        <ListBox>
            <local:Person FirstName="赵" LastName=" 峰"/>
            <local:Person FirstName="雅琪 " LastName="赵赵"/>
            <local:Person FirstName="李 " LastName="维斯"/>
        </ListBox>
    </StackPanel>
</Page>
```

==注意：WPF和Xamarin在别名声明中使用clr-namespace而不是使用using。原因是，使用UWP的XAML即不基于.NET，也不局限于.NET。可以使用本机C++和XAML，因此clr公共语言运行库就不适合。==

### 将属性用作特性
类的属性用XML特性来设置。要在XAML中设置属性，只要属性的类型可以表示为字符串，或者可以把字符串转换为属性类型，就可以把属性设置为特性。
下面的代码用XML特性设置了Button元素的Content和Background属性。
`Button Content="Click me !" Background="LightGoldenrodYellow"/>`
上面代码中，因为Content属性的类型是object，所以可以接受字符串。Background属性的类型是Brush，字符串转换为派生自Brush的SolidColorBrush类型。


### 将属性用作元素
总是可以使用元素语法给属性提供值。Button类的Background属性可以用子元素Button.Background设置。下面代码用特性定义了Button，效果是相同的。
```xml
<Button>
  Click Me!
  <Button.Background>
    <SolidColorBrush Color="LightGoldenrodYellow"/>
  <Button.Background>
</Button>
```

```xml
        <Button x:Name="button3" Click="OnButtonClicked">
            登录
            <Button.Background>
                <LinearGradientBrush StartPoint="0.5,0.0" EndPoint="0.5,1.0">
                    <GradientStop Offset="0" Color="Blue"/>
                    <GradientStop Offset="0.2" Color="Orange"/>
                    <GradientStop Offset="0.4" Color="Red"/>
                    <GradientStop Offset="1" Color="DarkRed"/>
                </LinearGradientBrush>
            </Button.Background>
        </Button>
```
使用元素代替特性，可以把比较复杂的画笔应用于Background属性。


==注意：当设置示例中的内容时，Content特性和Button.Content元素都不用于编写内容；相反，内容会直接写入Button元素的子元素。==


### 依赖属性
XAML使用依赖属性完成数据绑定、动画、属性变更通知、样式化等。假设创建一个类，他又100个int类型的属性，这个类在一个表单上实例化了100此。需要多少内存？ 因为int的大小是4个字节，所以结果是4*100*100=40000字节。刚才看到的是一个XAML元素的属性？由于继承层次结构非常大，一个XAML元素定义了数百的属性。属性类型不是简单的int，二十更复杂的类型。这样的属性会消耗大量的内存。
然而，通常只改变其中一些属性的值，大部分的属性保持对所有示例都相同的默认值。这个难题可以用依赖属性解决。

使用依赖属性，对象内存不是分配给每个属性和实例。依赖属性系统管理一个包含所有属性的字典，只有值发生改变才分配内存。否则，默认值就在所有实例之间共享。

依赖属性也内置了对变更通知的支持。对于普通属性，需要为变更通知实现INotifyProperty Changed接口。这种变更机制属性内置。对于数据绑定，绑定到.NET属性源上的UI元素的属性必须是依赖属性。现在，详细讨论依赖属性。

从外部来看，依赖属性像是正常的.NET属性。但是，正常的.NET属性通常还定义了由该属性的get和set访问器访问的数据成员。
```csharp
private int _value;
public int Value
{
    get=>_value;
    set=>_value=value
}
```
依赖属性不是这样的。依赖属性通常也有get和set访问器。他们于普通属性是相同的。但在Get和Set访问器的实现代码中，调用了GetValue和SetValue方法。
GetValue和GetValue方法是基类DependencyObject的成员，依赖对象需要使用这个类——他们必须在DependencyObject的派生类中实现。

有了依赖属性，数据成员就放在由基类管理的内部集合中，仅在值发生变化时分配数据。对于没有变化的值，数据可以在不同的实例或基类之间共享。

GetValue和SetValue方法需要一个DependencyProperty参数。这个参数由类的一个静态成员定义，改静态成员与属性同名，并在该属性名的后面追加Property术语。对于Value属性，静态成员的名称是ValueProperty。DependencyProperty.Register是一个辅助方法，可以在依赖属性系统中注册属性。

下面的代码使用Register方法和4个参数定义了属性名、属性的类型和拥有者的类型（即MyDependencyObject类），使用PropertyMetadata指定了默认值。

```csharp
public class MyDependencyObject:DependencyObject
{
    public int Value
    {
        get=>(int)GetValue(ValueProperty);
        set=>SetValue(ValueProperty,value);
    }

    public static readonly DependencyProperty ValueProperty=
      DependencyProperty.Register("Value",typeof(int),
      typeof(MyDependencyObject),new PropertyMetadata(0));
}
```


### 创建依赖属性
示例定义三个依赖属性。MyDependencyObject类定义了依赖属性Value、Minimum和Maximum。所有这些属性都是用DependencyProperty.Register方法注册的依赖属性。GetValue和SetValue方法是基类DependencyObject的成员。对于Minimum和Maximum属性，定义了默认值，用DependencyProperty.Register方法设置该默认值时，可以把第4个参数设置为PropertyMetadata。使用带一个参数PropertyMetadata的构造函数，把Minimum属性设置为0，把Maximum属性设置为100。
```csharp
    public class MyDependencyObject:DependencyObject
    {
        public int Value
        {
            get => (int)GetValue(ValueProperty);
            set => SetValue(ValueProperty, value);
        }

        public static readonly DependencyProperty ValueProperty =
            DependencyProperty.Register("Value", typeof(int), typeof(MyDependencyObject),
                new PropertyMetadata(0, OnValueChanged));

        private static void OnValueChanged(DependencyObject d,DependencyPropertyChangedEventArgs e)
        {
            int oldValue = (int)e.OldValue;
            int newValue = (int)e.NewValue;
        }


        public int Minimum
        {
            get => (int)GetValue(MinimumProperty);
            set => SetValue(MinimumProperty, value);
        }

        public static readonly DependencyProperty MinimumProperty =
            DependencyProperty.Register(nameof(Minimum), typeof(int),
                typeof(MyDependencyObject), new PropertyMetadata(0));


        public int Maximum
        {
            get => (int)GetValue(MaximumProperty);
            set => SetValue(MaximumProperty, value);
        }

        public static readonly DependencyProperty MaximumProperty =
            DependencyProperty.Register(nameof(Maximum),
                typeof(int), typeof(MyDependencyObject), new PropertyMetadata(100));
    }
```
==注意：在get和set属性访问器的实现代码中，只能调用GetValue（）和SetValue方法。使用依赖属性，可以通过GetValue和SetValue方法从外部访问属性的值，UWP也是这样做的；因此，强类型化的属性访问器可能根部就不会被调用，包含他们仅为了方便为自定义代码中使用正常的属性语法==


### 值变更回调和事件
为了获得值变更的信息，依赖属性还支持值变更回调。在属性值发生变化时调用的Dependency-Property.Register方法中，可以添加一个DependencyPropertyChanged事件处理程序。

在示例代码中，把OnValueChanged处理程序方法赋予PropertyMetadata对象的PropertyChangedCallback属性。在OnValueChanged方法中，可以用DependencyPropertyChangedEventArgs参数访问属性的新旧值。
```csharp
public class MyDependencyObject:DependencyObject
{
    public int Value
    {
        get=>(int)GetValue(ValueProperty);
        set=>SetValue(ValueProperty,value);
    }

    public static readonly DependencyProperty ValueProperty=
      DependencyProperty.Register(nameof(Value),typeof(int),
      typeof(MyDependencyObject),
      new PropertyMetadata(0,OnValueChanged,CoerceValue));
    
    private static void OnValueChanged(DependencyObject obj,DependencyPropertyChangedEventArgs e)
    {
        int oldValue=(int)e.OldValue;
        int newValue=(int)e.NewValue;
        //....
    }
}
```


### 路由事件
第八章介绍了.NET事件模型。使用默认实现的事件，当触发事件时，将调用直接连接到事件的处理程序。使用UI技术时，对时间处理有不同的需求。在一些事件中，应该可以创建一个带有容器控件的处理程序，并对来自子控件的事件作出反应。这可以通过为.NET事件创建自定义实现代码来实现，例如第八章的add和remove访问器所示。

UWP提供了路由事件。示例应用程序定义的用户界面包含一个复选框，如果选中它，就停止路由；
一个按钮控件，其Tapped事件设置为OnTappedButton处理程序方法；
一个网格，其Tapped事件设置为OnTappedGrid处理程序。

Tapped事件是Universal Windows应用程序的一个路由事件。这个事件可以用鼠标、触摸屏和笔设备触发。（RoutedEvents/MainPage.xaml）
```csharp
    <Grid Tapped="OnTappedGrid">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto"/>
            <RowDefinition/>
        </Grid.RowDefinitions>

        <StackPanel Grid.Row="0" Orientation="Horizontal">
            <CheckBox x:Name="CheckStopRouting">Stop Routing</CheckBox>
            <Button Click="OnCleanStatus">Clean Status</Button>
        </StackPanel>

        <Button Grid.Row="1" Tapped="OnTappedButton">Tap me!</Button>
        <TextBlock Grid.Row="2" Margin="20" x:Name="textStatus"/>

    </Grid>
//------------------------------------------------------------------------
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
        }

        private void OnTappedButton(object sender,TappedRoutedEventArgs e)
        {
            ShowStatus(nameof(OnTappedButton), e);
            e.Handled = CheckStopRouting.IsChecked == true;
        }

        private void OnTappedGrid(object sender,TappedRoutedEventArgs e)
        {
            ShowStatus(nameof(OnTappedGrid), e);
            e.Handled = CheckStopRouting.IsChecked == true;
        }

        private void ShowStatus(string status,RoutedEventArgs e)
        {
            textStatus.Text += $"{status} {e.OriginalSource.GetType().Name}";
            textStatus.Text += "\r\n";
        }

        private void OnCleanStatus(object sender,RoutedEventArgs e)
        {
            textStatus.Text = string.Empty;
        }
    }
```
OnTappedXXX处理程序方法吧状态信息写入一个TextBlock，来显示处理程序方法和事件初始源的控件。

运行应用程序，在网格内部单击按钮的外部，就会看到处理的OnTappedGrid事件，并把Grid空间触发事件源：OnTappedGrid Grid。
单击按钮中间，会看到事件被路由。第一个调用的处理程序是OnTappedButton，其后是OnTappedGrid：
OnTappedButton TextBlock
OnTappedGrid TextBlock

同样有趣的是，事件源不是按钮，而是TextBlock。原因在于，这个按钮使用TextBlock设置样式，来包含按钮的文本。如果单击按钮内的其他位置，还可以看到Grid或ContentPresenter是原始事件源。Grid和ContentPresenter是创建按钮的其他空控件。

单击按钮之前，选中复选框CheckStopRouting，可以看到事件不在路由，因为事件参数的Handled属性被设置为True；
OnTappedButton TextBlock

在事件的Microsoft API文档中，可以在文档的备注部分看到事件类型是否被路由。在Universal Windows应用程序中，tapped、drag和drop、key up和key down、pointer、focus、manipulation事件是路由事件。



### 附加属性
依赖属性是可用于特定类型的属性。而通过附加属性，可以为其他类型定义属性。一些容器控件为其子空间定义了附加属性：例如，如果使用DockPanel控件，就可以为其子控件使用Dock属性。
Grid控件定义了Row和Column属性。
下面的代码说明了附加属性在XAML中的情况。Button类没有Grid.Dock属性，但它是从Grid控件附加的。
```csharp
    <Grid >
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Button Content="First" Grid.Row="0" Background="Yellow"/>
        <Button Content="Sencond" Grid.Row="1" Background="Blue"/>
    </Grid>


//========================================================================

    public class MyAttachedPropertyProvider:DependencyObject
    {
        public static readonly DependencyProperty MySampleProperty=
            DependencyProperty.RegisterAttached(
            "MySample",
            typeof(string),
            typeof(MyAttachedPropertyProvider),
            new PropertyMetadata(string.Empty));

        public static void SetMySample(UIElement element, string value) =>
            element.SetValue(MySampleProperty, value);

        public static string GetMyProperty(UIElement element) =>
            (string)element.GetValue(MySampleProperty);
    }
```

==注意：四五Grid.Row属性只能添加到Grid控件中的元素。实际上，附加属性也可以添加到任何元素上。但无法使用这个属性的值。Grid控件能够识别这个属性，并从其子元素读取它，已安排其子元素。它不从子元素的子元素读取==

在XAML代码中，附加属性现在可以附加到任何元素上。第二个button控件button2为自身附加了属性MyAttachedPropertyProvider.MySample，其值指定为42。
```csharp
    <Grid x:Name="grid1">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Button Content="button1" x:Name="button1" Grid.Row="0" Background="Yellow"/>
        <Button Content="button2" x:Name="button2" Grid.Row="1" Background="Blue" local:MyAttachedPropertyProvider.MySample="42"/>
        <ListBox Grid.Row="2" x:Name="list1"/>
    </Grid>
```