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
```csharp

        <StackPanel Orientation="Vertical">   //多选
            <CalendarView x:Name="CalendaryView1" CalendarViewDayItemChanging="OnDayItemChanging"
                          SelectionMode="Multiple" SelectedDatesChanged="OnDatesChanged"
                          Margin="12" MinDate="{x:Bind MinDate,Mode=OneTime}" HorizontalAlignment="Center"
                          FirstDayOfWeek="Monday"/>
        </StackPanel>


public DateTimeOffset MinDate{get;}=
   DateTimeOffset.Parse("1/1/1965",new CultureInfo("zh-CN"));

//MinDate属性设置为一个预定义的日期。用户不能使用日历提前一天到达。
//最小的一天设置为绑定属性MinDate。

//事件CalendarViewDayItemChanging和事件SelectedDatesChanged分配给事件处理程序。

```
在OnDayItemChanging事件处理程序中，应该将某些日期标记为special（特殊的）。当天之前的日期应该排除在选择之外，根据实际的预定情况，当天应该用彩线标记。

为了获得预订，将定义GetBookings方法，已返回示例数据。在真正的应用程序中，可以从Web API或数据库中获得数据。GetBookings方法只返回从现在开始若干天（2，3，5...）的预订。通过返回一个元组得到一天内的预订数量（1,4,3...）。
```csharp
        private IEnumerable<(DateTimeOffset day, int bookings)> GetBookings()
        {
            int[] bookingDays = { 2, 3, 5, 8, 12, 13, 18, 21, 23, 27 };
            int[] bookingsPerDay = { 1, 4, 3, 6, 4, 5, 1, 3, 1, 1 };

            for (int i = 0; i < 10; i++)
            {
                yield return (DateTimeOffset.Now.Date.AddDays(bookingDays[i]), bookingsPerDay[i]);
            }
        }

//bookingDays 当天之后的那几天
//bookingsPerDay  对应预订的数量
```

当显示CalendarView的项时，将调用OnDayItemChanging方法。每个显示的日期都调用此方法。方法OnDayItemChanging是使用本地函数实现的。该方法的主块包含一个switch语句，基于数据绑定阶段来进行切换。

CalendarView控件支持多个阶段，允许在不同的迭代中调整用户界面。
第一阶段很快：此阶段之后，已经可以向用户显示一些信息。接下来的每个阶段都是如此。在以后的阶段中，可以从Web API中检索信息，并更新这些信息。

在OnDayItemChanging的实现中：
- 第一阶段调用本地函数RegisterUpdateCallback来注册对OnDayItemChanging事件处理程序的下一个调用。
- 第二阶段，使用本地函数SetBlackoutDates将日期涂黑。
- 第三阶段检索预订

```csharp
        private void OnDayItemChanging(CalendarView sender, CalendarViewDayItemChangingEventArgs args)
        {
            switch (args.Phase)
            {
                case 0:
                    RegisterUpdateCallback();
                    break;
                case 1:
                    SetBlackoutDates();
                    break;
                case 2:
                    SetBookings();
                    break;
                default:
                    break;
            }
```

本地函数RegisterUpdateCallback只是调用CalendarViewDayItemChangingEventArgs参数的RegisterUpdateCallback，传递事件处理程序方法，因此再次调用此方法。

本地函数SetBlackoutDates涂黑今天之前的日期，以及所有的星期六和星期天。从args.Item属性返回的CalendarViewDayItem定义了IsBlackout属性。
```csharp
private void OnDayItemChanging(CalendarView sender,CalendarViewDayItemChangingEventArgs args)
{
  //..
  void RegisterUpdateCallback()=>
    args.RegisterUpdateCallback(OnDayItemChanging);
  //..
}


//--------------------------------------------------------------
private void OnDayItemChanging(CalendarView sender,CalendarViewDayItemChangingEventArgs args)
{
  //...
  void SetBlackoutDates()
  {
    if(args.Item.Date<DateTimeOffset.Now || args.Item.Date.DayOfWeek==
      DayOfWeek.Saturday || args.Item.Date.DayOfWeek==DayOfWeek.Sunday)
      {
        args.Item.IsBlackout=true;
      }
      RegisterUpdateCallback();
  }
  //...
}
```

最后，setBookings方法检索关于预订的信息。如果在预订中也发现了接收日期，会检索CalendarViewDayItem中找到的接收日期。如果是，则调用SetDensityColors，把红色或绿色的列表（取决于工作日）添加到日期中。最后，再次调用RegisterUpdateCallback本地函数；否则，只会在第三阶段调用该函数，显示第一天。
```csharp
        private void OnDayItemChanging(CalendarView sender, CalendarViewDayItemChangingEventArgs args)
        {
            switch (args.Phase)
            {
                case 0:
                    RegisterUpdateCallback();
                    break;
                case 1:
                    SetBlackoutDates();
                    break;
                case 2:
                    SetBookings();
                    break;
                default:
                    break;
            }

            void RegisterUpdateCallback() => args.RegisterUpdateCallback(OnDayItemChanging);

            void SetBlackoutDates()
            {
                if (args.Item.Date < DateTimeOffset.Now || args.Item.Date.DayOfWeek == DayOfWeek.Saturday || args.Item.Date.DayOfWeek == DayOfWeek.Sunday)
                {
                    args.Item.IsBlackout = true;
                }
                RegisterUpdateCallback();
            }

            void SetBookings()
            {
                var bookings = GetBookings().ToList();

                var booking = bookings.SingleOrDefault(b => b.day.Date == args.Item.Date.Date);
                if (booking.bookings > 0)
                {
                    var colors = new List<Color>();
                    for (int i = 0; i < booking.bookings; i++)
                    {
                        if (args.Item.Date.DayOfWeek == DayOfWeek.Saturday || args.Item.Date.DayOfWeek == DayOfWeek.Sunday)
                        {
                            colors.Add(Colors.Red);
                        }
                        else
                        {
                            colors.Add(Colors.Green);
                        }
                    }

                    args.Item.SetDensityColors(colors);
                }
                RegisterUpdateCallback();
            }
        }
```

当用户选择日期时，将调用OnDatesChanged方法。在这个方法中，所有选中的日期都将在CalendarViewSelectedDatesChangedEventArgs中接收。

选中的日期写入currentDatesSelected列表，取消选择的日期将再次从列表中删除。使用sring.Join，所有选中的日期都显示在MessageDialog中。
```csharp

        private List<DateTimeOffset> currentDatesSelected = new List<DateTimeOffset>();

        private async void OnDatesChanged(CalendarView sender, CalendarViewSelectedDatesChangedEventArgs args)
        {

            currentDatesSelected.AddRange(args.AddedDates);
            args.RemovedDates.ToList().ForEach(date => currentDatesSelected.Remove(date));

            string selectedDates = string.Join(", ", currentDatesSelected.Select(d => d.ToString("d")));

            await new MessageDialog($"dates selected: {selectedDates}").ShowAsync();
        }
```


<br>

当使用CalendarDatePicker时，没有像CalendarView那么多特性，但是他不会占用屏幕的空间，除非用户打开他来选择日期。CalendarDatePicker定义了DateChanged事件：只能选择一个日期。
```csharp
<CalendarDatePicker x:Name="CalendarDatePicker1" Grid.Row="0" Grid.Column"1" DateChanged="OnDateChanged" Margin="12">


//------------------------------------------------------------------
        private async void OnDateChanged(CalendarDatePicker sender, CalendarDatePickerDateChangedEventArgs args)
        {
            await new MessageDialog($"date changed to {args.NewDate}").ShowAsync();
        }
```


<br>

DatePicker的XAML代码非常相似。他只是不显示日历来选择日期，而是有一个完全不同的视图。
```csharp
// MainPage.xaml
<DatePicker DateChanged="OnDateChanged1" x:Name="DatePicker1" Grid.Row="1" Margin="12">

//--------------------------------------------------------------------
        private async void OnDateChanged1(object sender, DatePickerValueChangedEventArgs e)
        {
            await new MessageDialog($"date changed to {e.NewDate}").ShowAsync();
        }
```

<br>

选择日期的最后一个选项是Flyout。Flyout可以与其他控件一起使用。这里使用一个按钮控件，按钮的Flyout属性定义为使用DatePickerFlyout。
```csharp
        <Button Content="Select a Date" Grid.Row="1" Grid.Column="1" Margin="12">
            <Button.Flyout>
                <DatePickerFlyout x:Name="DatePickerFlyout1" DatePicked="OnDatePicked" />
            </Button.Flyout>
        </Button>
//---------------------------------------------------------------
        private async void OnDatePicked(DatePickerFlyout sender, DatePickedEventArgs args)
        {
            await new MessageDialog($"date changed to {args.NewDate}").ShowAsync();
        }
```



<br>
<hr>

### 范围控件
范围控件，如ScrollBar、ProgressBar和Slider都派生自同一个基类RangeBase。
<table>
  <th>控件</th>
  <th>说明</th>
  <tr>
    <td>
      ScrollBar   
    </td>
    <td>
      ScrollBar控件包含一个Thumb，用户可以从Thumb中选择一个值。例如，如果文档在屏幕中放不下，就可以使用滚动条。一些控件包含滚动条，如果内容过多，就显示滚动条。
    </td>
  </tr>
  <td>
      ProgressBar   
    </td>
    <td>
      使用ProgressBar控件，可以指示事件较长的操作的进度。
    </td>
  </tr>  
  <td>
      Slider
    </td>
    <td>
      使用Slider控件，用户可以移动Thumb，选择一个范围的值。
    </td>
  </tr>    
</table>


1、ProgressBar
示例应用程序显示了两个ProgressBar控件。将第二个控件的IsIndeterminate属性设置为true。如果不知道一个活动需要多长时间，最好使用这个属性。如果想知道操作需要多长时间，可以在ProgressBar中设置当前状态值，而不需要设置IsIndeterminate模式；默认值为False（ControlsSamples/Views/RangeControlsPage.xaml）；

```csharp
//mainPage.xaml
        <StackPanel>
            <Button Content="Range Controls" Click="{x:Bind OnRange}"/>
        </StackPanel>
//mainPage.xaml.cs----------------------------------------------+
private void OnRange() => Frame.Navigate(typeof(RangeControlsPage));


//--------------------------------------------------------------
//RangeControlsPage.xaml
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <ProgressBar x:Name="progressBar1" Grid.Row="0" Margin="12"/>
        <ProgressBar IsIndeterminate="true" Grid.Row="1" Margin="12"/>

//--------------------------------------------------------------
//RangeControlsPage.xaml.cs
        public RangeControlsPage()
        {
            this.InitializeComponent();
            ShowProgress();
        }


        private void ShowProgress()
        {
            var timer = new DispatcherTimer();
            timer.Interval = TimeSpan.FromSeconds(0.002);
            int i = 0;
            timer.Tick += (sender, e) =>
              {
                  progressBar1.Value = i++;
                  if (i >= 100)
                  {
                      i = 0;
                  }
              };
            timer.Start();
        }
```

当加载页面时，将调用ShowProgress方法。这里，第一个ProgressBar的当前值使用DispatcherTimer设置的。将DispatherTimer配置为每秒触发一次，ProgressBar的Value属性每秒都递增。

当运行程序时，可以看到两个ProgressBar控件处于活动状态。第一个是进度条，第二个显示水平漂浮的点。


### Slider
使用Slider控件，可以指定Minimum和Maximum值，并使用Value属性来分配当前值。代码示例使用一个文本框显示滑块的当前值。

```csharp
<Slider x:Name="slider" Minimum="10" Maximum="140" Value="60"
  Grid.Row="2" Margin="12"/>

<TextBox Header="Slider Value" IsReadOnly="True"
  Text="{x:Bind slider.Value,Mode=OneWay}" Grid.Row="3" Margin="12">
```


<br>
<hr>

### 内容控件
内容控件的Content属性允许添加任何单一的内容。不允许使用多个内容对象作为Content属性的直接子对象，但是可以添加StackPanel，它本身可以把多个控件作为子控件。

<table>
<catption aligment="center">内容控件</caption>
<th>控件</th>
<th>说明</th>
<tr>
  <td>
    ScrollViewer
  </td>
  <td>
    ScrollViewer是一个内容控件，可以包含单项，并提供水平和垂直的滚动条。还可以使用带有附加属性的ScrollViewer，如前面介绍的ParallaxViewSample所示。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    Frame
  </td>
  <td>
    Frame控件用于页面之间的导航
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    SelectorItem
    <p>ComboBoxItem
    <p>FlipViewItem
    <p>GridViewItem
    <p>ListBoxItem
    <p>ListViewItem
    <p>GroupItem
    <p>PivotItem
  </td>
  <td>
    这些控件是ContentControl对象，作为属于某ItemsControl的项。例如，ComboBox控件包含ComboBoxItem对象，ListBox控件包含ListBoxItem对象，Pivot控件包含PivotItem对象。GroupItem对象通常不直接只用；使用带有分组配置的ItemsControl派生控件时，会使用他们。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    ToolTip
  </td>
  <td>
    当用户悬停在信息上，显示工具提示时，ToolTip会弹出一个窗口。可以使用ToolTipService.ToolTip附件属性配置ToolTip。工具提示只能时文本；这是一个内容控件。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    CommandBar
  </td>
  <td>
    使用CommandBar，可以安排AppBarButton控件和属于命令元素的控件（如AppBarSeparator）。
    <p>CommandBar为这些控件提供了一些布局特性。在Windows8中，使用AppBar而不是CommandBar——这就是为什么按钮有这些名称的原因。
    <p>CommandBar派生自AppBar。但是，如果CommandBar中布局不能满足需求，也可以使用其他控件来布局命令。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    ContentDialog
  </td>
  <td>
    使用ContentDialog打开一个对话框。可以使用对话框所需的任何XAML控件自定义此控件。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    SwipeControl
  </td>
  <td>
    SwipeControl允许通过触摸交互执行上下文命令——例如，在用户向左或向右滑动时为某些项打开特定的操作。
  </td>
</tr>
</table>

<br>
<hr>

## 按钮
按钮组成了一个层次结构。ButtonBase类派生自ContentControl，，因此按钮有一个Content属性，可以包含任何单个内容。ButtonBase类还定义了Command属性；因此，所有按钮都可以有一个相关的命令。下面比较一下不同的按钮。
<table>
<th>控件</th>
<th>说明</th>
<!--下一行-->
<tr>
  <td>Button</td>
  <td>
    Button类时最常用的按钮。这个类派生自ButtonBase（其他按钮也一样）。ButtonBase时所有按钮的基类。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>HyperlinkButton</td>
  <td>
    HyperlinkButton显示为链接。可以在浏览器中打开Web页面、打开其他应用程序或导航到其他页面。（Hyper——超级）
  </td>
</tr>
<!--下一行-->
<tr>
  <td>RepeatButton</td>
  <td>
    RepeatButton是一个按钮，当用户按下按钮时，Click事件连续触发。使用常规按钮，Click事件只触发一次。（Repeat——重复）
  </td>
</tr>
<!--下一行-->
<tr>
  <td>AppBarButton</td>
  <td>
    AppBarButton用于激活应用程序中的命令。可以将该按钮添加到CommandBar中，并使用图标和标签来显示用户的信息。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    AppBarToggleButton（Toggle——切换）
    <p>CheckBox
    <p>RadioButton
  </td>
  <td>
    CheckBox、RadioButton和AppBarToggleButton派生自基类ToggleButton。TogleButton可以使用“bool？”表示三种状态：Checked、Unchecked和Indeterminate。AppBarToggleButton是CommandBar的切换按钮。
  </td>
</tr>
</table>


### 替换按钮的内容
按钮是一个内容控件，可以有任何内容。下面的示例包含Ellipse和TextBlock的按钮添加Grid控件。该按钮还定义了Click事件，以演示它的不同外观，但它的行为是相同的。

```csharp
//ButtonsPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>

        <Button Margin="12" Click="OnButtonClick">
            <Grid>
                <Ellipse Width="200" Height="90" Fill="red"/>
                <TextBlock HorizontalAlignment="Center" 
                           VerticalAlignment="Center" Text="Click Me!" FontSize="24"/>
            </Grid>
        </Button>

//Ellipse 椭圆
//Navigate 导航
//Content属性替换了前景色，但是按钮仍然具有默认的背景。
//要替换按钮的完整外观，并使按钮变成非矩形的形状，需要为按钮创建一个ControlTemplate。

        <HyperlinkButton NavigateUri="https://www.zhcw.com"
                         Content="C# Infos" Grid.Column="1" Style="{StaticResource TextBlockButtonStyle}" FontSize="24" />
    </Grid>
```
使用HyperlinkButton进行链接，可以轻松激活其他一个用程序。将NavigateUri属性设置为URL，单击按钮，会打开默认浏览器。

默认情况下，HyperlinkButton看起来像浏览器中的一个链接。使用HyperlinkButton可以设置NavigateUri或定义Click事件。但不能同时执行这两个操作。作为Click事件的操作，可以以编程方式导航到另一个页面。
不仅可以为NavigateUri属性分配`http://` 或 `https://` ，还可以使用 `ms-appx://` 激活其他应用程序。


### 项控件
与ContentControl相反，ItemsControl控件可以包含项的列表。通过ItemsControl可以使用Items属性来确定某些项，也可以使用数据绑定和ItemsSource属性来确定某些项。但不能同时使用这两中属性。

<table>
  <th>控件</th>
  <th>说明</th>
  <tr>
    <td>
      ItemsControl   
    </td>
    <td>
      ItemsControl时所有其他项控件的基类，也可以直接用于显示项的列表
    </td>
  </tr>
<!--下一行-->
<tr>
  <td>Pivot</td>
  <td>
    Pivot控件是为应用程序创建类似于表的行为的控件。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>AutoSuggestBox</td>
  <td>
    AutoSuggestBox替换了先前的SearchBox。使用AutoSuggestBox，用户可以输入文本，控件提供自动完成功能。这个控件的使用详见36章。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    ListBox
    <p>ComboBox
    <p>FlipView
  </td>
  <td>
    ListBox、ComboBox和FlipView是三个派生自基类Selector的控件。
    <p>Selector派生自ItemsControl，并添加SelectedItem和SelectedValue属性，以便从集合中选择某项。
    <p>ListBox显示了用户可以从中选择的列表。
    <p>ComboBox结合了一个文本框和一个下拉列表，允许选择列表，且使用更少的屏幕空间。
    <p>FlipView控件允许使用触摸交互来浏览项目列表，而只显示一项。
  </td>
</tr>
<!--下一行-->
<tr>
  <td>
    ListView
    <p>GridView
  </td>
  <td>
    ListView和GridView派生自基类ListViewBase，ListViewBase派生自Selector。
    因此这些是最强大的的选择器。
    ListViewBase提供了附加的拖放项、重新排序项、添加页眉和页脚，并允许选择多个项。ListView垂直显示项（但是也可以创建一个模板，水平显示列表）。
    <p>GridView用行和列显示数据项。
  </td>
</tr>
</table>


### Flyout控件
Flyout控件用于在其他UI元素（例如上下文菜单）之上打开窗口。所有的Flyout都派生自基类FlyoutBase。
FlyoutBase类定义了一个Placement属性，允许定义Flyout的位置。它可以在屏幕中居中，也可以围绕目标元素定位。

- Flyout控件——MenuFlyout控件用于显示菜单项的列表
- Flyout——Flyout控件可以包含一个能使用XAML元素自定义的项。

