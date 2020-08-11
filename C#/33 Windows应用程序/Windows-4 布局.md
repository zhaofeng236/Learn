### 布局
前一节讨论的NavigationView控件是组织用户界面布局的一个重要控件。在许多新的Windows10应用程序中，可以看到这个控件用于主要布局。其他几个控件也定义布局。本节演示了Variable SizedWrapGrid在网格中安排自动包装的多个项，Relativepanel相对于彼此安排各项或相对于父项安排自相，自适应触发器根据窗口的大小重新排列布局。

#### StackPanel
如果要在只能包含一个元素的的空间中包含多个元素，最简单的方式是使用Stackpanel
。StackPanel是一个简单的面板，只能逐个显示元素。StackPanel的方向可以是水平或垂直的。

下面演示了一个StackPanel，其中包含了垂直放置的各个控件。在第一个ListBoxItem的列表框中，包含了一个横向排列的StackPanel。
```csharp
//StackPanelPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel Orientation="Vertical">
            <TextBox Text="TextBox"/>
            <CheckBox Content="CheckBox"/>
            <CheckBox Content="CheckBox"/>
            <ListBox>
                <ListBoxItem>
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Text="One A"/>
                        <TextBlock Text="One B"/>
                    </StackPanel>
                </ListBoxItem>
                <ListBoxItem Content="Two"/>
            </ListBox>
            <Button Content="button"/>
        </StackPanel>
    </Grid>


//StackPanelPage.xaml.cs
//这个是返回按钮
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
                Frame.CanGoBack ? AppViewBackButtonVisibility.Visible :
                AppViewBackButtonVisibility.Collapsed;
            base.OnNavigatedTo(e);
        }

//MainPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel Orientation="Vertical">
            <Button Content="SackPanel" Click="OnStackPanelPage"/>
        </StackPanel>
    </Grid>

//MainPage.xaml.cs
//返回按钮
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
              Frame.CanGoBack ? AppViewBackButtonVisibility.Visible :
              AppViewBackButtonVisibility.Collapsed;

            base.OnNavigatedTo(e);
        }


对应的事件
        private void OnStackPanelPage(object sender, RoutedEventArgs e) =>
            Frame.Navigate(typeof(StackPanelPage));

```
<hr>

#### Canvas
Canvas是一个允许显示指定控件位置的面板。它定义了相关的Left、Right、Top和Buttom属性，这些属性可以由子元素在面板中定位时使用。
定位子元素TextBlock、TextBox和Button
==注意：Canvas控件最适合用于图形元素的布局，例如第35章的Shape控件==
```csharp
//Canvas.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Canvas Background="LightBlue">
            <TextBlock Canvas.Top="30" Canvas.Left="20">
                Enter Here:
            </TextBlock>
            <TextBox Canvas.Top="30" Canvas.Left="120" Width="100"/>
            <Button Canvas.Top="70" Canvas.Left="120" Content="Click Me!" Padding="4"/>
        </Canvas>
    </Grid>


// Canvas.xaml.cs
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
              Frame.CanGoBack ? AppViewBackButtonVisibility.Visible :
              AppViewBackButtonVisibility.Collapsed;

            base.OnNavigatedTo(e);
        }


//MainPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel Orientation="Vertical">
            <Button Content="SackPanel" Click="OnStackPanelPage"/>
            <Button Content="Canvas" Click="OnCanvasPage"/>
        </StackPanel>
    </Grid>


//MainPage.xaml.cs
        private void OnCanvasPage(object sender, RoutedEventArgs e) =>
            Frame.Navigate(typeof(CanvasPage));
```


<hr>

#### Grid
Grid是一个重要的面板。使用Grid，可以在行和列中排列控件。
对于每一列，可以指定一个ColumnDefinition；
对于每一行，可以指定一个RowDefinition。

下面示例代码显示两列三行。在每一列和每一行中，都可以指定宽度和高度。ColumnDefinition有一个Width依赖属性，RowDefinition有一个Height依赖属性。可以以设备独立的像素为单位定义高度和宽度，或者把他们设置为Auto，根据内容来确定其大小。

Grid还允许使用“星型大小”，即根据具体情况指定大小，即根据可用的空间以及其他行和列的相对位置计算行和列的空间。在为列提供可用空间时，可以将Width属性设置为`*`。要使某一列的空间时另一个的两倍，应指定`2*`。下面的实例代码定义了两列三行，列使用“星型大小”，第一行的大小固定，第二行和第三行再次使用“星型大小”。在计算高度时，可用空间需要减去第一行的200像素，剩余的区域在第二行和第三行按照1.5:1来分配。

这个Grid包含几个Rectangle控件，他们使用不同的颜色是单元格的尺寸可见。因为这些控件的父控件是Grid，所以可以设置附加属性Column、ColumnSpan、Row和RowSpan。
```csharp
//GridPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="200"/>
            <RowDefinition Height="1.5*"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <Rectangle Fill="Blue"/>
        <Rectangle Grid.Row="0" Grid.Column="1" Fill="Red"/>
        <Rectangle Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2" Fill="Green"/>
        <Rectangle Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2" Fill="Yellow"/>
    </Grid>


//GridPage.xaml.cs
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
              Frame.CanGoBack ? AppViewBackButtonVisibility.Visible :
              AppViewBackButtonVisibility.Collapsed;

            base.OnNavigatedTo(e);
        }


//MainPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel Orientation="Vertical">
            <Button Content="Grid" Click="OnGridPage"/>
        </StackPanel>
    </Grid>


//MainPage.xaml.cs
        private void OnGridPage(object sender, RoutedEventArgs e) =>
            Frame.Navigate(typeof(GridPage));
```

<hr>

#### VariableSizedWrapGrid
VariablesSizedWrapGrid是一个包装网格，如果网格可用的大小不够大，它会自动换到下一行或列。这个表格的第二个特征是允许项放在多行或多列中，这就是为什么他称为可变的原因。

代码片段创建一个VariableSizedWrappedGrid，其方向是Horizontal，行中最多有20项，行和列的大小是20。
```csharp
<VariableSizedWrapGrid x:Name="grid1" MaximumRowsOrColumns="20" ItemHeight="50" 
        ItemWidth="50" Orientation="Horizontal"/>
```

VariableSizedWrapGrid填充了30个随机大小的颜色的Rectangle和TextBlock元素。根据大小，可以在网格内使用1到3行或列。项的大小使用附加属性VariableSizedWrapGrid.ColumnSpan和VariableSizedWrapGrid.RowSpan设置。
```csharp
//VariableSizedWrapGrid.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <VariableSizedWrapGrid x:Name="grid1" MaximumRowsOrColumns="20" ItemHeight="50"
                      ItemWidth="50"  Orientation="Horizontal" />        
    </Grid>

//VariableSizedWrapGrid.xaml.cs

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            base.OnNavigatedTo(e);
            SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
              Frame.CanGoBack ? AppViewBackButtonVisibility.Visible :
              AppViewBackButtonVisibility.Collapsed;

            var r = new Random();
            Grid[] items =
                Enumerable.Range(0, 30).Select(i =>
                 {
                     byte[] colorBytes = new byte[3];
                     r.NextBytes(colorBytes);

                     var rect = new Rectangle
                     {
                         Height = r.Next(40, 150),
                         Width = r.Next(40, 150),
                         Fill = new SolidColorBrush(new Color
                         {
                             R = colorBytes[0],
                             G = colorBytes[1],
                             B = colorBytes[2],
                             A = 255
                         })
                     };
                     var textBlock = new TextBlock
                     {
                         Text = (i + 1).ToString(),
                         HorizontalAlignment = Windows.UI.Xaml.HorizontalAlignment.Center,
                         VerticalAlignment = Windows.UI.Xaml.VerticalAlignment.Center
                     };

                     var grid = new Grid();
                     grid.Children.Add(rect);
                     grid.Children.Add(textBlock);
                     return grid;
                 }).ToArray();

            foreach(var item in items)
            {
                grid1.Children.Add(item);
                Rectangle rect = item.Children.First() as Rectangle;

                if (rect.Width > 50)
                {
                    int columnSpan = ((int)rect.Width / 50) + 1;
                    VariableSizedWrapGrid.SetColumnSpan(item, columnSpan);
                    int rowSpan = ((int)rect.Height / 50) + 1;
                    VariableSizedWrapGrid.SetRowSpan(item, rowSpan);
                }
            }

        }


//MainPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel Orientation="Vertical">
            <Button Content="VariableSizedWrapGrid" Click="OnVariableSizedWrapGrid"/>
        </StackPanel>
    </Grid>


//MainPage.xaml.cs
        private void OnVariableSizedWrapGrid(object sender, RoutedEventArgs e) =>
            Frame.Navigate(typeof(VariableSizedWrapGridPage));

```


#### RelativePanel
RelativePanel是UWP的一个新面板，允许一额元素相对于另一个元素定位。如果使用的Grid控件定义了行和列，且需要插入一行，就必须修改插入行下面的所有元素。原因是所有行都按数字索引。使用RelativePanel就没有这个问题，它允许根据元素的相对关系放置他们。
==注意：与RelativePanel相比，Grid控件仍然有他的自动、星型和固定大小的优势==

下面的代码片段在RelativePanel内对其数个TextBlock和TextBox控件、一个按钮和一个矩形。TextBox元素定位在相应TextBlock的右边；按钮相对于面板的底部定位，矩形与第一个TextBlock的顶部对齐，与第一个TextBox的右边对齐。
```csharp
//RelativePanelPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <RelativePanel>
            <TextBlock x:Name="FirstNameLabel" Text="First Name:" Margin="8"/>
            <TextBox x:Name="FirstNameText" RelativePanel.RightOf="FirstNameLabel"
                     Margin="8" Width="150"/>
            
            <TextBlock x:Name="LastNameLabel" Text="Last Name:" 
                       RelativePanel.Below="FirstNameLabel" Margin="8"/>
            <TextBox x:Name="LastNameText" RelativePanel.RightOf="LastNameLabel"
                     Margin="8" RelativePanel.Below="FirstNameText" Width="150"/>
            
            <Button Content="Save" RelativePanel.AlignHorizontalCenterWith="LastNameText" 
                    RelativePanel.AlignBottomWithPanel="True" Margin="8"/>

            <Rectangle x:Name="Image" Fill="Violet" Width="150" Height="250"
                       RelativePanel.AlignTopWith="FirstNameLabel"
                       RelativePanel.RightOf="FirstNameText" Margin="8"/>
        </RelativePanel>
    </Grid>



//RelativePanelPage.xaml.cs
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
              Frame.CanGoBack ? AppViewBackButtonVisibility.Visible :
              AppViewBackButtonVisibility.Collapsed;

            base.OnNavigatedTo(e);
        }


//MainPage.xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel Orientation="Vertical">
            <Button Content="RelativePanel" Click="OnRelativePanelPage"/>
        </StackPanel>
    </Grid>


//MainPage.xaml.cs
        private void OnRelativePanelPage(object sender, RoutedEventArgs e) =>
            Frame.Navigate(typeof(RelativePanelPage));
```