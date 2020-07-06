# 使用依赖注入
EF Core 内置了对依赖注入的支持。使用EF Core和依赖注入容器也得到有力的支持。它不是定义连接并利用DbContext派生类来使用SQL Server，而是使用依赖注入框架来注入连接和SQL Server选项。

为了看到起操作，前面示例用BooksSampleWithDI示例项目进行修改。

```csharp
namespace EFCore_test.DB.DI
{
    public class BooksContextDI:DbContext
    {
        public BooksContextDI(DbContextOptions<BooksContextDI> options)
            : base(options) { }

        public DbSet<Book> Books { get; set; }
    }
}
```

<hr>
BooksService是利用BooksContextDI的新类。在这里，BooksContextDI通过构造函数注入功能来注入。方法AddBooksAsync和ReadBooks非常类似于前面的示例，但是他们使用BooksService类的上下文成员，而不是创建一个新的上下文。

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using Microsoft.EntityFrameworkCore;
using System.Threading.Tasks;

namespace EFCore_test.DB.DI
{
    public class BooksService
    {
        private readonly BooksContextDI _booksContextDI;

        public BooksService(BooksContextDI contextDI) => _booksContextDI = contextDI;

        //插入记录
        public async Task AddBooksAsync()
        {
            var b1 = new Book
            {
                Title = "九月就是雨天",
                Publisher = "电子工业出版本"
            };

            var b2 = new Book
            {
                Title = "你的一生失败",
                Publisher = "电飞机出版本"
            };

            var b3 = new Book
            {
                Title = "不要在等待不值得",
                Publisher = "哈尔滨出版本"
            };
            _booksContextDI.AddRange(b1, b2, b3);
            int records = await _booksContextDI.SaveChangesAsync();
            Console.WriteLine($"{records} 条记录被插入!");
        }



        //读取记录
        public async Task ReadBooksAsync()
        {
            List<Book> books = await _booksContextDI.Books.ToListAsync();

            foreach(var b in books)
            {
                Console.WriteLine($"{b.Title}\t\t{ b.Publisher}");
            }
            Console.WriteLine();
        }

    }
}
```


依赖注入框架的容器在InitializeServices方法中初始化。这里创建了ServiceCollection示例，在这个集合中添加BooksService类，并进行短暂的声明周期管理。这样，每次请求这个服务时，就实例化ServiceCollection。

为了注册Entity Framework和SQL Server，可以使用扩展方法AddEntityFrameworkSqlServer和AddDbContext。
AddDbContext方法需要一个Action委托作为参数，来接收DbContextOptionsBuilder参数。有了这个选项参数，上下文可以使用UseSqlServer扩展的方法来配置。这类似于Entity Framework注册SQL Server的功能。

```csharp
        private void InitializeServices()
        {
            const string ConnectionString = @"server=localhost;" +
                "database=BooksStore;" +
                "trusted_connection=true;";

            var services = new ServiceCollection();
            services.AddTransient<BooksService>()
                .AddEntityFrameworkSqlServer()
                .AddDbContext<BooksContextDI>(options =>
                options.UseSqlServer(ConnectionString));

            Container = services.BuildServiceProvider();
        }
```

服务的初始化以及使用BooksService在Main方法中完成。通过调用IServiceProvider的GetService方法，检索BooksService。

```csharp
        static async Task Main(string[] args)
        {
            Console.WriteLine("这个是EFCore 学习程序");
            var p = new Program();

            p.InitializeServices();
            var service = p.Container.GetService<BooksService>();
            await service.AddBooksAsync();
            await service.ReadBooksAsync();

            p.Container.Dispose();

            Console.ReadKey();
        }
```

为了利用这个应用程序设置配置日志记录，可以通过AddLogging扩展方法吧ILoggerFactory接口添加到DI容器中。

```csharp
private void InitializeServices
{
    ...
    services.AddLogging();
}
```
添加日志记录。在ConfigureLogging方法中实现，从DI容器中检索ILoggerFactory。使用这个工厂，会添加控制台，写入日志信息。



<hr>
<br>

## 创建模型

### 创建关系
示例项目使用`MenuCard` 和`Menu` 类型定义了一对多的关系。`MenuCard`包含`Menu`对象的列表。这个关系由`List<Menu>`类型的Menu属性定义。


示例
```csharp
namespace EFCore_test.DB
{
    public class MenuCard
    {
        public int MenuCardId { get; set; }
        public string Title { get; set; }

        public List<Menu> Menus { get; } = new List<Menu>();

        public override string ToString() => Title;
    }
}
```


```csharp
namespace EFCore_test.DB
{
    public class Menu
    {
        public int MenuId { get; set; }
        public string Text { get; set; }
        public decimal Price { get; set; }
        
        //外键
        public int MenuCardId { get; set; }
        public MenuCard MenuCard { get; set; }
        public override string ToString() => Text;
    }
}
```
MenuCard包含Menu对象的列表。这个关系由`List<Menu>`类型的Menu属性定义。

也可以在另一个方向上访问关系，Menu可以使用MenuCard属性访问MenuCard。指定MenuCardId属性来定义一个外键关系。

数据库的映射是通过MenusContext类实现的。这个类的定义类似于前面的上下文类型；只包含两个属性，映射两个对象类型：Menus和MenuCards属性。

```csharp
    public class MenusContext:DbContext
    {
        private const string ConnectionString = @"localhost;" +
            @"database=MenuCards;trusted_Connection=True";

        public DbSet<Menu> Menus { get; set; }
        public DbSet<MenuCard> MenuCards { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            base.OnConfiguring(optionsBuilder);
            optionsBuilder.UseSqlServer(ConnectionString);
        }
    }
```

在创建代码中修改一些部分是有益的。例如，Text和Title列的尺寸可以减小nvarchar(max)中的值。另外，SQL Server定义了Money类型，可以用于Price列，模式名称可以在dbo中修改。Entity Framework提供了两个选项——数据注释和流利API——用于在代码中进行这些修改。

### 数据注释
要影响生成的数据库，一个方法是给实体类型添加数据注释。默认情况下，表的名称来自于上下文的属性。因此，要映射Menu类，应使用Menus表，因为映射Menu的DebSet属性名为Menus。

有了数据注释，可以使用Table特性来改变表格。要改变模式名称，Table特性定义Schema特性。为了给字符串类型指定另一个长度，可以使用MaxLength特性。
> 参见 MenusWithDataAnnotations/MenuCard.cs

```csharp
[Table("MenuCards",Schema="mc")]
public class MenuCard
{
    public int MenuCardId {get;set;}

    [MaxLength(120)]
    public string Title {get;set;}

    public List<Menu> Menus {get;set;}=new List<Menu>();
}
```

>$\color{red}{注意}：$ EF Core使用上下文中的Table和MaxLength特性。为了更改SQL类型，可以使用Column特性。参见 MenusWithDataAnnotations/Menu.cs。

```csharp
[Table("Menus",Schema="mc")]
public class Menu
{
    public int MenuId {get;set;}

    [MaxLength(50)]
    public string Text {get;set;}
    
    [Column(TypeName=Money)]
    public decimal Price {get;set;}

    public int MenuCardId {get;set;}

    public MenuCard MenuCard {get;set;}
}
```
>相应的SQL代码如下：
```sql
create table [mc].[MenuCards]
(
    MenuCardId int Identity (1,1) not null,
    Title nvarchar(120) null,
    constraint pk_menuCard primary key clustered (MenuCardId ASC)
);



create table [mc].[Menus]
(
    MenuId int identity (1,1) not null,
    MenuCardId int not null,
    Price money not null,
    Text nvarchar(50) null,
    constraint pk_Menu primary key clustered (MenuId ASC),
    constraint fk_Menu_MenuCard_MenuCardId foreign key (MenuCardId) 
    References [mc].[MenuCards] (MenuCardId) on delete Cascade)
```


应用迁移并创建数据库后，可以在Title、Text和Price列上看到表的新名称和模式名称，以及改变的数据类型。


### 流利API
影响所创建表的另一种方法是通过DbContext派生类的OnModelCreating方法使用流利API。使用它的优点是，实体类型可以很简单，不需要添加任何特性，流利API也提供了比应用特性更多的选择。

下面版本显示BooksContext类的OnModelCreating方法重写版本。接收为参数的ModelBuilder类提供了一些方法，定义了一些扩展方法。HasDefaultSchema是一个扩展方法，把默认模式应用于模型，现在用于所有类型。Entity方法返回一个EntityTypeBuilder，允许自定义实体，如把他映射到特定的表名，定义键和索引：

```csharp

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);
    modelBuilder.HasDefaultSchema("mc");
    
    modelBuilder.Entity<MenuCard>()
      .ToTable("MenuCards")
      .HasKey(c =>c.MenuCardId);
    //...

    modelBuilder.Entity<Menu>()
      .ToTable("Menus")
      .HasKey(m=>m.MenuId);
}

```

EntityTypeBuilder定义了一个Property方法来配置属性。Property方法返回一个PropertyBuilder，它允许用最大长度值、需要的设置和SQL类型配置属性，指定是否应该自动生成值。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<MenuCard>()
      .Property(c=> c.MenuCardId)
      .ValueGeneratedOnAdd();

    modelBuilder.Entity<MenuCard>()
      .Property(c=> c.Title)
      .HasMaxLength(50);
      //...


//定义另一个实体
    modelBuilder.Entity<Menu>()
      .Property(m =>m.MenuId)
      .ValueGeneratedOnAdd();

    modelBuilder.Entity<Menu>()
      .Property(m =>m.Text)
      .HasMaxLength(120);

    modelBuilder.Entity<Menu>()
      .Property(m =>m.Price)
      .HasColumnType("Money");
      //..  
}
```

要定义一对多映射，EntityTypeBuilder定义了映射方法。方法`HasMany`  与 `WithOne` 结合。用一个菜单卡定义了很多菜单的映射。
HasMany需要与WithOne链接起来。

方法HasOne需要和WithMany或WithOne链接起来。
- 链接HasOne与WithMany，会定义一对多关系；
- 链接HasOne与WithOne定义一对一关系：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<MenuCard>()
      .HasMany(c => c.Menus)
      .WithOne(m => m.MenuCard);
    
    modelBuilder.Entity<Menu>()
      .HasOne(m => m.MenuCard)
      .WithMany(c => c.Menus)
      .HasForeignKey(m=>m.MenuCardId);
}
```

<hr>

> 完整的MenusContext如下：

```csharp
using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;
using System.Text;

namespace MenusSample
{
    public class MenusContext : DbContext
    {
        private const string ConnectionString = @"server=(localdb)\MSSQLLocalDb;" +
            "Database=MenuCards;Trusted_Connection=True";
        public DbSet<Menu> Menus { get; set; }
        public DbSet<MenuCard> MenuCards { get; set; }
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            base.OnConfiguring(optionsBuilder);
            optionsBuilder.UseSqlServer(ConnectionString);
           // optionsBuilder.UseSqlServer(ConnectionString, options => options.MaxBatchSize(1));
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);
            modelBuilder.HasDefaultSchema("mc");

            modelBuilder.ApplyConfiguration(new MenuCardConfiguration());
            modelBuilder.ApplyConfiguration(new MenuConfiguration());
            //modelBuilder.Entity<MenuCard>()
            //    .ToTable("MenuCards")
            //    .HasKey(c => c.MenuCardId);
            //modelBuilder.Entity<MenuCard>()
            //    .Property(c => c.MenuCardId)
            //    .ValueGeneratedOnAdd();
            //modelBuilder.Entity<MenuCard>()
            //    .Property(c => c.Title)
            //    .HasMaxLength(50);

            ////modelBuilder.Entity<MenuCard>()
            ////    .HasMany(c => c.Menus)
            ////    .WithOne(m => m.MenuCard);

            //modelBuilder.Entity<Menu>()
            //    .ToTable("Menus")
            //    .HasKey(m => m.MenuId);

            //modelBuilder.Entity<Menu>()
            //    .Property(m => m.MenuId)
            //    .ValueGeneratedOnAdd();

            //modelBuilder.Entity<Menu>()
            //    .Property(m => m.Text)
            //    .HasMaxLength(120);

            //modelBuilder.Entity<Menu>()
            //    .Property(m => m.Price)
            //    .HasColumnType("Money");

            //modelBuilder.Entity<Menu>()
            //    .HasOne(m => m.MenuCard)
            //    .WithMany(m => m.Menus)
            //    .HasForeignKey(m => m.MenuCardId);
        }
    }
}

```


<hr>

### 自包含类型的配置
拥有一个更复杂的DbContext后，OnModelCreating方法可能会变得很长。
EF Core2.0提供了一个新选项，用于为每个类型定义配置类。

要创建一个配置类，类需要使用方法Configure实现接口IEntityTypeConfiguration。为MenuCard类型创建MenuCardConfiguration，可以简化配置，如下代码片段（MenusSample/MenuCardConfiguration.cs）：

```csharp
public class MenuCardConfiguration:ieNTITYtYPEcONFIGURATION<mENUcARD>
{
    public void Configure(EntityTypeBuilder<MenuCard> builder)
    {
        builder.ToTable("MenuCards")
          .HasKey(c=>c.MenuCardId);
        
        builder.Property(c=>c.MenuCardId)
          .ValueGeneratedOnAdd();
        
        builder.Property(c=>c.Title)
          .HasMaxLength(50);
        
        builder.HasMany(c=>c.Menus)
          .WithOne(m=>m.MenuCard);
    }
}
```

类Menu的配置在MenuConfiguration类中定义。
EntityTypeBuilder使用的方法与前面使用的方法相同。代码更简单，因为实体类型不需要被选中，而有了IEntityTypeConfiguration，就已经指定了实体类型：

```csharp
public class MenuConfiguration:IEntityTypeConfiguration<Menu>
{
    public void Configure(EntityTypeBuilder<Menu> builder)
    {
        builer.ToTable("Menus")
          .HasKey(m=>m.MenuId);
        
        builder.Property(m=>m.MenuId)
          .ValueGeneratedOnAdd();
        
        builder.Property(m=>m.Text)
          .HasMaxLength(120);

        builder.Property(m=>m.Price)
          .HasColumnType("Money");
        
        builder.HasOne(m=>m.MenuCard)
          .WithMany(m=>m.Menus)
          .HasForeignKey(m=>m.MenuCardId);
    }
}
```

MenusContext类的OnModelCreating方法可以简化。要应用IEntityTypeConfiguration类型的配置，需要调用ModelBuilder的ApplyConfiguration方法（代码文件：MenusSample/MenusContext.cs）：
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);
    modelBuilder.HasDefaultSchema("mc");

    modelBuilder.ApplyConfiguration(new MenuCardConfiguration());
    modelBuilder.ApplyConfiguration(new MenuConfiguration());
}

```



### 在数据库中搭建模型
除了从模型中创建数据库之外，也可以从数据库中创建模型。
必须在项目中添加NuGet包：
> Microsoft.EntityFrameworkCore.Design.
- 在DotnetCliToolReference元素中添加Microsoft.EntityFrameworkCore.Tools.Dotntet。
- Microsoft.EntityFrameworkCore.Design包只需要用于项目本身，包需要引用这个包的其他项目，所以可以指定PrivateAssets特性。
(项目文件：ScaffoldSample/ScaffoldSample.Csproj)
