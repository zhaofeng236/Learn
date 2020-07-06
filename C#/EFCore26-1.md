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