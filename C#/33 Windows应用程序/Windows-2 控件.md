## 控件
Windows应用程序的UI类的层次结构
- `DependencyObject`——这个类位于Windows Runtime XAML元素的层次结构顶层。派生自DependencyObject的每个类都可以有依赖属性。在本章的XAML介绍中，已经介绍了依赖属性。
- `UIElement`——这是带有试卷外观的元素基类。这个类提供了用户交互的功能，比如指针事件（PointerPressed、PointerMoved等），键处理事件（KeyDown、KeyUp），焦点事件（GotFocus、LostFocus），指针捕获（CapturePointer、PointerCanceled等），拖放（DragOver、Drop等）。这个类还提供了新的Lights属性，从构建好15063开始就可以通过light高亮显示。
- `FrameworkElement`——类FrameworkElement派生自UIElement，添加了更多的特性。从FrameworkElement派生的类可以参与布局系统。属性MinWidth、MinHeight、Height和Width由FrameworkElement类定义。FrameworkElement也定义了生命周期事件：Loaded、SizeChanged、Unloaded都是这些事件中的一部分。数据绑定特性是FrameworkElement类定义的另一组功能。这个类定义了DataContext、DataContextChanged、SetBinding和GetBindingExpression API。
- `Control`——Control类派生自FrameworkElement，是UI控件的基类，例如TextBox、Hub、DatePicker、SearchBox、UserControl等。控件通常有一个默认样式，其ControlTemplate分配给Template属性。Control类为基类UIElement定义的事件定义了可重写的On**方法。控件定义了TabIndex；用于前景和边界（Foreground、Background、BorderBrush、BorderThickness）的属性；启用它并使用键盘上的Tab键来访问它的属性（IsTabStop、TabIndex）；
- `ContentControl`——类ContentControl派生自Control，允许将任何内容作为该控件的子内容。ContentControl的例子由AppBar、Frame、ButtonBase、GroupItem和ToolTip控件。ContentControl定义了可以分配任何内容的Content属性、分配DataTemplate的ContentTemplate属性、动态分配数据模板的ContentTemplateSelector以及用于简单动画的ContentTransitions属性。
- `ItemsControl`——ContentControl只能有一个内容，而ItemsControl可以查看内容列表。ContentControl定义了要列出其子项的Content属性，而ItemsControl用Items属性实现了这个功能。ContentControl和ItemsControl都派生自基类Control。ItemsControl可以显示固定数量的项或通过列表绑定的项。派生自ItemControl的控件有ListView、GridView、ListBox、Pivot和Selector。
- `Panel`——另一个可以作为项容器的类是Panel类。这个类派生自基类FrameworkElement。Panel用于定位和排列子对象。从Panel派生的类的例子有Canvas、Grid、StackPanel、VariableSizedWrapGrid、VirtualizingPanel、ItemsStackPanel、ItemsWrapGrid以及RelativePanel。
- `RangeBase`——这个派生自Control类，是ProgressBar、ScrollBar和Slider的基类。RangeBase定义了当前值的Value属性、Minimum和Maximum属性，以及ValueChanged事件处理程序。
- `FlyoutBase`——这个类直接派生自DependencyObject，允许在其他元素上显示用户界面——换句话说，它们是随时可弹出的。


### 框架派生的UI元素
有些元素不是真正的控件，但他们仍然是派生自FrameworkElement的类的UI元素。这些类不允许通过指定模板来定制外观。UI元素的类别使用这个基类：呈现器、媒体元素和文本显示元素。
<table>
  <th>类</th>
  <th>说明</th>

  <tr>
    <td>
      <p>Border
      <p>Viewbox
      <p>ContentPresenter
      <p>ItemsPresenter
    </td>
    <td>
      <p>呈现器不是交互的类，但是他们仍然提供视觉外观。
      <p>Border类定义了围绕单个控件的边缘(可以包含多个其他控件的Grid)
      <p>Viewbox能够拉伸和缩放子元素
      <p>ContentPresenter在ControlTemplate中使用。他定义控件的内容将显示在何处。
      <p>ItemsPresenter用于确定项在ItemsControl中的位置。
    </td>
  </tr>

  <tr>
    <td>
      <p>TextBlock
      <p>RichTextBlock
    </td>
    <!--fe-->
    <td>
    <p>TextBlock和RichTextBlock控件用于显示文本。使用这些控件不能输入文本；他们只是用来展示的。
    <p>TextBlock控件不仅允许分配简单的文本，还允许分配更复杂的文本元素，如段落和内联元素。
    <p>RichTextBlock也支持溢出。注意，RichTextBlock不支持使用RTF（富文本文件）。此时需要使用RichEditBox。
    <p>使用这些控件显示流文本。
    </td>
  </tr>
    <!--换行-->
  <tr>
    <td>
      <p>Ellipse
      <p>Polygon
      <p>Polyline
      <p>Path
      <p>Rectangle
    </td>
    <!--换列-->
    <td>
      Shape类派生自FrameworkElement。Shape本身是Ellipse、Polygon、Polyline、Path、Rectangle等的基类。
    这些类用于向向量绘制到屏幕上。
    </td>
  </tr>
      <!--换行-->
  <tr>
    <td>
      Panel
    </td>
    <!--换列-->
    <td>
    Panel类派生自FrameworkElement。Panel用于组织屏幕上的UI元素。派生自Panel类的不同面板将在本章布局面板中学习
    </td>
  </tr>
      <!--换行-->
  <tr>
    <td>
      CaptureElement
    </td>
    <!--换列-->
    <td>
    CaptureElement类用于呈现捕获设备（如摄像头或网络摄像机）的流。
    </td>
  </tr>
  <!--换行-->
  <tr>
    <td>
      InkCanvas
    </td>
    <!--换列-->
    <td>
    InkCanvas控件是用钢笔和墨水绘制的绘图区域。
    </td>
  </tr>
  <tr>
    <td>
      Image
    </td>
    <!--换列-->
    <td>
    Image控件用于显示图像。此控件支持显示如下个格式的图像：JPEG、PNG、BMP、GIF、TIFF、JPEG XR、ICO以及SVG。自1703版本开始就支持SVG（Scalable Vector Graphics，可缩放矢量图形）。从Windows1607版本开始支持GIF动画
    </td>
  </tr>
  <tr>
    <td>
      ParallaxView
    </td>
    <!--换列-->
    <td>
    ParallaxView是构建版本16299中一个新的控件，可以在滚动时产生视差效果
    </td>
  </tr>
</table>



### 呈现器
在PresentersPage中使用一些呈现器控件：Border和Viewbox。Border用于对两个TextBox元素分组。因为Border元素只能包含一个子元素，所以在Border元素中使用StackPanel。Border指定了Background、BorderBrush（刷子）和BorderTickness（厚度）。
两个Viewbox控件用于拉伸Button控件。第一个Viewbox使用Fill模式的延伸，将Button完全填充在Viewbox中，而第二个Viewbox使用Uniform模式的延伸。对于Uniform，要保持纵横比。
```csharp
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Border Background="LightSeaGreen" BorderBrush="DarkGreen" BorderThickness="12" Margin="12" Padding="8">
            <StackPanel Orientation="Vertical">        //方向=垂直
                <TextBox Header="Title" x:Name="Title" FontSize="34"/>
                <TextBox Header="Publisher" x:Name="Publisher" FontSize="34"/>
            </StackPanel>
        </Border>
        //使用Fill延伸
        <Viewbox Grid.Row="1" Stretch="Fill" StretchDirection="Both">
            <Button Margin="4" FontSize="14">
                Button with fill stretch
            </Button>
        </Viewbox>

        //使用Uniform延伸
        <Viewbox Grid.Row="2" Stretch="Uniform" StretchDirection="Both">
            <Button Margin="4" FontSize="14">
                Button with uniform stretch
            </Button>
        </Viewbox>

    </Grid>
```

显示了正在运行的应用程序的呈现器页面。在这里可以看到TextBox控件如何被包围的，按钮显示在两个不同的Viewbox配置中。
==注意：控件派生的类有一个隐式边界，可以使用BorderThickness和BorderBrush属性来定制它==

### 视差
ParallaxView提供了视差效应。有了视差效应，就可以在前景中显示一个列表框，在北京中显示一幅图画。背景中的图片比前景移动得慢，以获得立体效果，这成为视差效应。

示例应用程序ParallaxViewSample演示了这种效果，使用滑块可以增加或减少该效果。
示例应用程序中显示的是LunchMenu对象的一个简单模型。
```csharp
    public class LunchMenu
    {
        public int MenuId { get; set; }
        public string Text { get; set; }
        public string ImageUrl { get; set; }
    }
```

ParallaxView控件将Image定义为子元素，也可以为背景创建其他形状。ParallaxView的Source属性绑定到ListView控件。ListView控件显示项的列表，并绑定到LunchMenu对象列表上。要使用ParallaxView定义该效果，需要设置HorizontalShift和VerticalShift属性。

ListView垂直滚动，因此下面讨论垂直移动。当VerticalShift设置为0时，没有视差效应。将该值设置为100意味着，ParallaxView配置为比ListView小100像素，因此ListView的滚动速度更快。

将HorizontalShift和VerticalShift属性绑定到一个Slider，因此可以在应用程序运行时配置该效果。
```csharp
<Page
    x:Class="WinTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:model="using:WinTest.Model"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" xmlns:local1="using:WinTest"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto" />
            <RowDefinition />
        </Grid.RowDefinitions>
        <StackPanel Orientation="Vertical">
            <Slider x:Name="HorizontalSlider" Header="Horizontal Shift" Minimum="0" Maximum="1000" Value="0" />
            <Slider x:Name="VerticalSlider" Header="Vertical Shift" Minimum="0" Maximum="1000" Value="0" />
        </StackPanel>

        <ParallaxView Source="{x:Bind MenuItems}" Grid.Row="1" 
                      HorizontalShift="{x:Bind HorizontalSlider.Value, Mode=OneWay}" 
                      VerticalShift="{x:Bind VerticalSlider.Value, Mode=OneWay}">
            <Image Source="https://kantinem101.blob.core.windows.net/menuimages/Hirschragout_1024"></Image>
        </ParallaxView>

        <!-- Horizontal - uncomment to use this -->
        <!--<ListView HorizontalAlignment="Center" x:Name="MenuItems" ItemsSource="{x:Bind Menus, Mode=OneWay}" SelectionMode="None" Grid.Row="1"
                  ScrollViewer.VerticalScrollBarVisibility="Disabled" ScrollViewer.VerticalScrollMode="Disabled"
                  ScrollViewer.HorizontalScrollBarVisibility="auto" ScrollViewer.HorizontalScrollMode="Enabled">
            <ListView.ItemsPanel>
                <ItemsPanelTemplate>
                    <ItemsStackPanel Orientation="Horizontal" />
                </ItemsPanelTemplate>
            </ListView.ItemsPanel>
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="model:LunchMenu">
                    <Grid Margin="12">
                        <Image Width="300" Source="{x:Bind ImageUrl, Mode=OneWay}" />
                        <TextBlock Margin="8" VerticalAlignment="Bottom" HorizontalTextAlignment="Center" Text="{x:Bind Text, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}" FontWeight="Bold" />
                    </Grid>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>-->

        <!-- Vertical - comment to use the horizontal version -->
        <ListView HorizontalAlignment="Center" x:Name="MenuItems" ItemsSource="{x:Bind Menus, Mode=OneWay}" SelectionMode="None"  Grid.Row="1">
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="model:LunchMenu">
                    <Grid Margin="12">
                        <Image Width="300" Source="{x:Bind ImageUrl, Mode=OneWay}" />
                        <TextBlock Margin="8" VerticalAlignment="Bottom" HorizontalTextAlignment="Center" Text="{x:Bind Text, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}" FontWeight="Bold" />
                    </Grid>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </Grid>
</Page>


//============================================================================================
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
        }

        private IEnumerable<LunchMenu> _menusList = new List<LunchMenu>()
        {
            new LunchMenu { MenuId = 1, Text = "Chicken Salad", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/Backhendelsalat"},
            new LunchMenu { MenuId = 2, Text = "Cordon Bleu", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/CordonBleu_250"},
            new LunchMenu { MenuId = 3, Text = "Wiener Schnitzel", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/WienerSchnitzel_250"},
            new LunchMenu { MenuId = 4, Text = "Lasagne", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/Lasagne_250"},
            new LunchMenu { MenuId = 5, Text = "Lentils with bacon and dumplings", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/Linsen_250"},
            new LunchMenu { MenuId = 6, Text = "Schweinslungenbraten", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/Schweinslungenbraten_250"},
            new LunchMenu { MenuId = 7, Text = "Spätzle", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/Spinatspaetzle_250"},
            new LunchMenu { MenuId = 8, Text = "Topfennockerl", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/Topfennockerl_250"},
            new LunchMenu { MenuId = 9, Text = "Fried trout with potatoes", ImageUrl = "https://kantinem101.blob.core.windows.net/menuimages/forelle_250"},
        };

        public IEnumerable<LunchMenu> Menus => _menusList;
    }
```
要在水平模式下查看视差效果，可以将ListView更改为水平滚动。为此，可以更改ListView的ItemsPanel，如下代码所示。在可下载的代码示例中，需要取消对这个ListView配置的注释，使他成为活动的。
```csharp
        <ListView HorizontalAlignment="Center" x:Name="MenuItems" ItemsSource="{x:Bind Menus, Mode=OneWay}" SelectionMode="None" Grid.Row="1"
                  ScrollViewer.VerticalScrollBarVisibility="Disabled" ScrollViewer.VerticalScrollMode="Disabled"
                  ScrollViewer.HorizontalScrollBarVisibility="auto" ScrollViewer.HorizontalScrollMode="Enabled">
            <ListView.ItemsPanel>
                <ItemsPanelTemplate>
                    <ItemsStackPanel Orientation="Horizontal" />
                </ItemsPanelTemplate>
            </ListView.ItemsPanel>
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="model:LunchMenu">
                    <Grid Margin="12">
                        <Image Width="300" Source="{x:Bind ImageUrl, Mode=OneWay}" />
                        <TextBlock Margin="8" VerticalAlignment="Bottom" HorizontalTextAlignment="Center" Text="{x:Bind Text, Mode=OneWay}" Style="{StaticResource SubtitleTextBlockStyle}" FontWeight="Bold" />
                    </Grid>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
```

<br>

### 控件派生的控件
直接从基类Control派生的控件属于这个类别。

<table>
  <th>控件</th>
  <th>说明</th>
  <tr>
    <td>
      TextBox
    </td>
    <td>
      此控件用于显示简单的、未格式化的文本。此控件可用于用户输入。Text属性包含用户输入。PlaceholderText允许向用户提供要在输入字段中输入的信息。通常输入文本的一些信息会显示在附近。这可以直接使用Header属性完成。 
    </td>
  </tr>

  <tr>
    <td>
      RichEditBox
    </td>
    <td>
      与TextBox控件相反，RichEditBox允许输入格式化的文本、超链接和图像。文本对象模型（Text，Object， Model，TOM）是在Document属性中使用的。第36章介绍了如何使用这个控件。可以使用MicrosoftWord创建能读入RichEditBox的RTF文件
    </td>
  </tr>

  <tr>
    <td>
      PasswordBox
    </td>
    <td>
      此控件用于输入密码。它具有密码输入的特定属性，比如PasswordChar定义在用户输入密码时显示的字符。可以使用Password属性检索输入的密码。此控件还具有与TextBox控件类似的Header和PlaceholderText属性。
    </td>
  </tr>

  <tr>
    <td>
      ProgressRing
    </td>
    <td>
      此控件只是操作正在进行。他显示为一个环形的“旋转器”。另一个显示正在进行的操作的空间是ProgressBar，但是这个控件属于范围控件。
    </td>
  </tr>

  <tr>
    <td>
      <p>DatePicker
      <p>CalendarDatePicker
      <p>CalendarView
    </td>
    <td>
      DatePicker和CalendarDatePicker控件允许用户选择日期。如果用户知道日期，则显示日历是没有用的，就可以使用DatePicker选择日期。CalendarDatePicker在内部使用CalendarView。如果日历应该一直可见，或者需要选择多个日期，就可以使用CalendarView。
      <p>请注意,还有一个DatePickerFlyout（派生自Flyout的控件），它允许用户在新打开的窗口中选择日期。
    </td>
  </tr>

  <tr>
    <td>
      TimePicker
    </td>
    <td>
      TimePicker允许用户输入时间。类似于DatePicker，通过TimePicker还可以使用TimePickerFlyout。  
    </td>
  </tr>

  <tr>
    <td>
      AppBarSeparator
    </td>
    <td>
      AppBarSeparator控件可以用作CommandBar中的分隔符 
    </td>
  </tr>

  <tr>
    <td>
      ColorPicker
    </td>
    <td>
      ColorPicker允许用户选择颜色
    </td>
  </tr>

  <tr>
    <td>
      <p>Hub
      <p>HubSection
    </td>
    <td>
      Hub控件允许在平移视图中对内容进行分组。该控件中的内容是在多个HubSection控件中定义的。Hub控件与许多应用程序一起使用，以“Hero ” 图像布局应用程序的主视图。  
    </td>
  </tr>

  <tr>
    <td>
      UserControl
    </td>
    <td>
      UserControl是可以用于重用的控件，并使用页面简化XAML代码。用户控件可以添加到页面中。
    </td>
  </tr>

  <tr>
    <td>
      Page
    </td>
    <td>
      Page类本身派生自UserControl，因此他也是UserControl。Page用于在Frame中导航。  
    </td>
  </tr>

  <tr>
    <td>
      PersonPicture
    </td>
    <td>
      PersonPicture是16929构建版本中的新控件。它用来显示一个人的头像。此控件与Contactmanager
      和Contact API一起使用。  
    </td>
  </tr>

  <tr>
    <td>
      RatingControl
    </td>
    <td>
      RatingControl是16929构建版本中的另一个新控件。使用此控件，用户可以输入星级评分。
    </td>
  </tr>

  <tr>
    <td>
      SemanticZoom
    </td>
    <td>
      SemanticZoom控件定义了两个视图：缩小视图和放大视图。这允许用户快速导航到大型数据集。例如，在缩小视图中只显示第一个字符。在放大视图中，用户用选定的字母定位数据对象。  
    </td>
  </tr>

  <tr>
    <td>
      SplitView
    </td>
    <td>
      SplitView控件有一个窗格和一个内容。窗格可以打开和关闭。当打开窗格时，内容部分位于窗格后面，也可以向右移动。打开的窗格可以是小的或宽的。SplitView在新的NavigationPane中使用
    </td>
  </tr>
</table>

<hr>

### 使用文本框
包含Control派生控件的第一个示例显示了几个TextBox控件。在TextBox类中，可以将InputScope属性指定为大量值列表的值，如EmailNameOrAddress、CurrencyAmountAndSymbol或Formula。如果应用程序在平板模式下使用，并带有屏幕键盘，键盘会根据输入字段的需要调整不同的布局并显示键。

示例代码的最后一个文本框是多行TextBox。为了让用户按下回车键，可以设置AcceptsReturn属性。同时，如果文本在一行中放不下，就设置TextWrapping属性，使得文本换行。文本框的高度设置为150.如果输入的文本在这个文本框中放不下，则使用附加属性ScrollViewer.VerticalScrollBarVisibility来显示滚动条。

```csharp
//TextPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel Margin="16">
            <TextBox Header="Email" InputScope="EmailNameOrAddress"></TextBox>
            <TextBox Header="Currency" InputScope="CurrencyAmountAndSymbol"></TextBox>
            <TextBox Header="Alpha Numeric" InputScope="AlphanumericFullWidth"></TextBox>
            <TextBox Header="Formula" InputScope="Formula"></TextBox>
            <TextBox Header="Month" InputScope="DateMonthNumber"></TextBox>
            <TextBox Header="multiline" AcceptsReturn="True" TextWrapping="wrap" 
                     Height="150" ScrollViewer.VerticalScrollBarVisibility="Auto"/>
        </StackPanel>
    </Grid>

//TextPage.xaml.cs
    public sealed partial class TextPage:Page
    {
        public TextPage()
        {
            this.InitializeComponent();
        }

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
                Frame.CanGoBack ? AppViewBackButtonVisibility.Visible :
                AppViewBackButtonVisibility.Collapsed;

            base.OnNavigatedTo(e);
        }
    }
====================================================================
//MainPgae.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel>
            <Button Content="Text " Click="{x:Bind OnText}"/>
            <Button Content="Presenters" Click="{x:Bind OnPresenters}"/>
        </StackPanel>
    </Grid>

//MainPage.xaml.cs
        private void OnText(object sender, RoutedEventArgs e) =>
            Frame.Navigate(typeof(TextPage));
====================================================================

```










### 选择日期
对于选择日期，可以使用多个选项。下面看看不同的选项，以及CalendarView控件的特殊特性。
CalendarView配置为允许选择多个日期。每周的第一个工作日设置为周一，最小的以添设置为绑定属性MinDate，事件CalendarViewDayItemChang和SelectedDatesChanged分配给事件处理程序。