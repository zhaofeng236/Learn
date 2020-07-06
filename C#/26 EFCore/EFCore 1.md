# EF Core入门

本教程，将创建一个.net core控制台程序，该应用使用Entity Framework Core对SQLite数据库执行操作。

## 先决条件
.NET Core SDK

## 创建项目
`.NET Core CLI`
> dotnet new console -o EFGetStarted
> cd EFGetStarted

## 安装 Entity Framework Core
> .NET Core CLI
> dotnet add package Microsoft.EntityFrameworkCore
> dotnet add package Microsoft.EntityFrameworkCore.SqlServer

## 创建模型
定义构成模型的上下文类和实体类
```csharp
using System.Collections.Generic;
using Microsoft.EntityFrameworkCore;

//Blog类
public class Blog
{
    public int BlogId{get;set;}
    public string Url{get;set;}

    public List<Post> Posts {get;}=new List<Post>();
}


//Post类
public class Post
{
    public int PostId{get;set;}
    public string Title{get;set;}
    public string Content{get;set;}

    public int BlogId{get;set;}
    public Blog Blog{get;set;}
}




//上下文类
public class BloggingContext:DbContext
{
    public DbSet<Blog> Blogs{get;set;}
    public DbSet<Post> Posts{get;set;}

    protected override void OnConfiguring(DbContextOptionsBuilder options)=>
    options.UseSqlServer("Data Source=blogging.db")；
}
```

EF Core还可以从现有数据库对模型进行反向工程
未清除起见，有意简化了此应用程序。连接字符串不应该存储在生产应用程序代码中。可能还需要将每个C#类车分为其自己的文件。

## 创建数据库
> .NET Core CLI
> dotnet tool install --global dotnet-ef
> dotnet add package Microsoft.EntityFrameworkCore.Design
> dotnet ef migrations add InitialCreate
> dotnet ef database update

安装dotnet ef和设计包，这是对项目运行命令必须的。
`migrations` 命令为迁移搭建机架，以便为模型创建一组初始表。
`database update` 命令创建数据库并向其应用新的迁移。


## 创建、读取、更新和删除
打开Program.cs：
```csharp
using System;
using System.Linq;
public class Program
{
    public static void Main()
    {
        using(var db=new BloggingContext())
        {
            //创建
            Console.WriteLine("Inserting a new blog");
            db.Add(new Blog{Url="http://blogs.msdn.com"});
            db.SaveChanges();


            //Read
            Console.WriteLine("Querying for a blog");
            var blog=db.Blogs
                .OrderBy(b=>b.BlogId)
                .First();
            

            //update
            Console.WriteLine("Updating the blog and adding a post");
            blog.Url="https://devblogs.microsoft.com";
            blog.Posts.Add
            (
                new Post
                {
                    Title="Hello world",
                    Content="I wrote an app using EF Core"
                }
            );
            db.SaveChanges();


            //Delete
            Console.WriteLine("Delete the blog");
            db.Remove(blog);
            db.SaveChanges();
        }
    }
}

```

> 运行应用 dotnet run


<br>
<hr>
#  安装 Entity Framework Core

## 必备条件
- EF Core是一个.NET Standard 2.0库。因此，EF Core需要支持运行.NET Standard 2.0的实现。其他.NET Standard 2.0 库也可饮用EF Core。
- 例如，可使用EF Core开发面向.NET Core的应用。生成.NET Core应用需要.NET Core SDK。
- EF Core可以在Xamarin和.NET Native等其他.NET上实现。

## 获取 Entity Framework Core运行时
将EF Core添加到应用程序，请安装适用于要使用的数据提供程序的NuGet包。

如果要生成Asp.NET Core应用程序，不需要安装内存中和Sql Server提供程序。这些提供程序随EF Core运行时一起包含在当前版本的ASP.NET Core中。

.NET Core CLI
- 在操作系统的命令中：
  - `dotnet add package Microsoft.EntityFrameworkCore.SqlServer`
- 可以使用-v 修饰符在dotnet add package命令中致命特定的版本。
- VS包管理控制台
  - 工具->NuGet包管理器->包管理控制台
  - >powerShell :Install-Package Microsoft.EntityFrameworkCore.SqlServer。
  - 更新程序，使用 `Update-Package` 命令。
  - 若要指定特定版本，使用 -Version修饰符。


## 获取Entity Framework Core工具
可以安装工具来执行项目中与EF Core相关的任务，例如创建和应用数据库迁移，或基于现有数据库创建EF Core模型。

提供了两个工具集：
- .NET Core命令行接口（CLI）工具可以用于Windows、linux或macOS。这些命令以`dotnet ef`开头。
- 包管理器控制台（PMC）工具在Windows的VS中运行，命令以`Add-Migration、 Update-Database`。


### 获取.NET Core CLI工具
.NET Core CLI工具需要前面系统必备的.NET Core SDK。

`dotnet ef` 命令包含在当前版本的.NET Core SDK中，如要对特定命令启用该命令，必须安装 `Microsoft.EntityFrameworkCore.Design 包`。
> dotnet add package Microsoft.EntityFrameworkCore.Design

### 获取包管理器控制台工具
> PowerShell:Install-Package Microsoft.EntityFrameworkCore.Tools。



### 示例：
```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace EFCore_test
{
    [Table("Books")]
    public class Book
    {
        public int BookId { get; set; }

        [Required]
        [StringLength(50)]
        public string Title { get; set; }

        [StringLength(30)]
        public string Publisher { get; set; }
    }
}
```

<hr>

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using Microsoft.EntityFrameworkCore;

namespace EFCore_test
{
    public class BooksContext:DbContext
    {
        private const string ConnectionString = @"server=localhost;" +
            @"database=BooksStore;" +
            @"trusted_connection=true;";

        public DbSet<Book> Books { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            base.OnConfiguring(optionsBuilder);
            optionsBuilder.UseSqlServer(ConnectionString);
        }
    }
}

```


```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Infrastructure;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using System.Xml.Linq;

namespace EFCore_test
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("这个是EFCore 学习程序");
            var p = new Program();
            //await p.CreateTheDataBaseAsync();
            //await p.AddBookAsync("余量控制的思维", "机械工业出版社!");
            //await p.AddBooksAsync();
            //await p.ReadBooksAsync();

            Console.WriteLine("\n\n");
            await p.QueryBooksAsync();
            await p.UpdateBookAsync();
            await p.ReadBooksAsync();

            //await p.DeleteDatabaseAsync();

            Console.ReadKey();
        }


        //创建数据库和数据表
        private async Task CreateTheDataBaseAsync()
        {
            using(var context=new BooksContext())
            {
                bool created = await context.Database.EnsureCreatedAsync();
                string creationInfo = created ? "创建成功" : "已经存在";
                Console.WriteLine($"数据库 {creationInfo}");
            }
        }

        //添加一条记录
        private async Task AddBookAsync(string title,string publisher)
        {
            using(var context=new BooksContext())
            {
                var book = new Book { Title = title, Publisher = publisher };
                await context.Books.AddAsync(book);
                int records = await context.SaveChangesAsync();

                Console.WriteLine($"{records} 条记录被插入!");
            }
            Console.WriteLine();
        }

        //批量插入数据
        private async Task AddBooksAsync()
        {
            using (var context = new BooksContext())
            {
                var b1 = new Book { Title = "Professional C# 6 and .NET Core 1.0", Publisher = "Wrox Press" };
                var b2 = new Book { Title = "Professional C# 5 and .NET 4.5.1", Publisher = "Wrox Press" };
                var b3 = new Book { Title = "JavaScript for Kids", Publisher = "Wrox Press" };
                var b4 = new Book { Title = "Web Design with HTML and CSS", Publisher = "For Dummies" };
                await context.Books.AddRangeAsync(b1, b2, b3, b4);
                int records = await context.SaveChangesAsync();

                Console.WriteLine($"{records} 条记录被插入！");
            }
            Console.WriteLine();
        }


        //读取数据库
        private async Task ReadBooksAsync()
        {
            using(var context =new BooksContext())
            {
                List<Book> books = await context.Books.ToListAsync();

                foreach(var book in books)
                {
                    Console.WriteLine($"{book.Title}\t\t\t\t{book.Publisher}");
                }

                Console.WriteLine();
            }
        }


        //查询数据
        private async Task QueryBooksAsync()
        {
            using(var context=new BooksContext())
            {
                List<Book> books = await context.Books
                    .Where(b => b.Publisher == "Wrox Press")
                    .ToListAsync();

                //使用LINq query进行查询

                foreach(var b in books)
                {
                    Console.WriteLine($"{b.Title} {b.Publisher}");
                }
            }
            Console.WriteLine();
        }


        //更新数据表文件
        private async Task UpdateBookAsync()
        {
            using(var context=new BooksContext())
            {
                int record = 0;
                Book book = await context.Books
                    .Where(b => b.Publisher == "机械工业出版社!")
                    .FirstOrDefaultAsync();

                if (book != null)
                {
                    book.Title = "苍穹之上的小鸟！";
                    record = await context.SaveChangesAsync();
                }
                Console.WriteLine($"{record} 条记录被更新!");
            }
            Console.WriteLine();
        }


        //删除记录
        private async Task DeleteBooksAsync()
        {
            using(var context=new BooksContext())
            {
                List<Book> books = await context.Books.ToListAsync();
                context.Books.RemoveRange(books);
                int record = await context.SaveChangesAsync();
                Console.WriteLine($"{record} 条记录被删除！");
            }
        }


        //删除数据库
        private async Task DeleteDatabaseAsync()
        {
            Console.WriteLine("是否删除数据库？");
            string input = Console.ReadLine();
            if (input.ToLower() == "y")
            {
                using(var context=new BooksContext())
                {
                    bool deleted = await context.Database.EnsureDeletedAsync();
                    string deletionInfo = deleted ? "已经删除" : "没有被删除";
                    Console.WriteLine($"数据库{deletionInfo}");
                }
            }
        }
    }
}

```



## 使用依赖注入
EF Core 内置了对依赖注入的支持。使用EF Core和依赖注入容器也得到有力的支持。它不是定义连接并利用DbContext派生类来使用SQL Server，而是使用依赖注入框架来注入连接和SQL Server选项。

为了看到起操作，前面示例用BooksSampleWithDI示例项目进行修改。
