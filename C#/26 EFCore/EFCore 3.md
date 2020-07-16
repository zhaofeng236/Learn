## 查询
前面定义了模型，下面了解查询的更多细节。
- 基本查询
- 在服务器和客户端上评价
- 原始SQL查询
- 编译过的查询有更好的性能
- 全局查询过滤器
- EF.Functions

### 基本查询
访问DbSet的上下文属性将返回指定表的所有实体的列表。
访问Books属性，就会从数据库中检索所有Book记录（代码 BooksSample/QuerySamples.cs）：

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Query;
using System.Linq;
using System.Threading.Tasks;

namespace EFCore_test
{
    public class QuerySamples
    {
        public static async Task QueryAllBooksAsync()
        {
            Console.WriteLine(nameof(QueryAllBooksAsync));
            using(var context=new BooksContext())
            {
                List<Book> books = await context.Books.ToListAsync();

                foreach(var b in books)
                {
                    Console.WriteLine(b);
                }
            }
        }
    }
}

```

有了异步API，也可以使用从ToAsyncEnumerable方法返回的IAsyncEnumerable接口，使用ForEachAsync方法而不是foreach循环：
```csharp
await context.Books.ToAsyncEnumerable()
  .ForEachAsync(b=>
  {
      Console.WriteLine(b)
  });

```

访问Books属性，会把下面的SQL语句发送到数据库：
```sql
select b.bookid ,b.IsDeleted,b.LastUpdated,b.Publisher,b.Title from books as b;
```

可以使用Find和FindAsync方法查询具有特定键的对象。如果没有找到记录，该方法就返回null：

```csharp
Book b=await context.Books.FindAsync(id);
if(b!=null)
{
    ///...
}
```
这就得到了一个带有TOP（1）和Where子句的Select Sql语句。

```sql
select top(1) e.BookId,e.IsDeleted,e.LastUpdated,e.Publisher,e.Title
from Books as e
where e.Bookid=@__get_Item_0
```

与使用Find方法不同，还可以使用同步的Single或singleOrDefault方法，或者使用异步变体SingleAsync或SingleOrDefaultAsync。

Single和SingleOrDefault的区别在于，Single在没有找到记录时抛出异常，而SingleOrDefault会在没有找到记录时返回null。这些方法还在找到多个记录时抛出一个异常。

下面的代码片段使用SingleOrDefaultAsync方法来请求书名：
```csharp
Book book=await context.Books.SingleOrDefaultAsync(b=>b.Title==title);
```

生成的SQL语句要求TOP（2）记录，它允许在找到两个记录时抛出异常：
```sql
select top(2)
///...
from Books as b
where b.Title=@__title_0;
```
Where方法允许基于条件进行简单过滤。还可以在Where表达式中使用Contains方法。Where方法没有可用的异步变体，因为Where方法使用了惰性求值。

可以使用foreach语句来迭代查询的所有结果。然而，foreach会触发查询的执行，阻塞线程，直到检索到结果。与使用foreach和Where方法的结果不同，可以使用ToListAsync立即触发执行，但要在任务中执行：
```csharp
List<Book> books=await context.Books
  .Where(b=>b.Title.Contains(title))
  .ToListAsync();
```

> $\color{red}注意:$ **在12章中介绍了LINQ方法和LINQ子句，也可以在EF Core中使用他们。请记住，LINQ to Objects和LINQ to EF Core的实现时不同的。**
> 在LINQ to EF Core中，使用表达式树可以在运行时使用完整的LINQ表达式创建SQL查询。
> 在LINQ to Objects中，大多数LINQ查询都是在Enumerable类中定义的。带有表达式树的LINQ在Queryable类中实现，对EF Core的许多增强在EntityFrameworkQueryableExtensions类中实现。


<br>
<hr>

### 客户端和服务器求值
不是查询的每个部分都可以转换为SQL语句，从而在服务器上运行。有些部分需要在客户端上运行。EF Core允许进行透明的客户端和服务器求值。如果查询不能解析，会自动在客户端上运行。这对于使用不同的提供程序有很大的又是。
例如：对于一个提供程序，可以在服务器上对查询进行完全的求值，使用不转换所有查询的另一个提供程序，程序仍然运行，但时有些部分现在在客户端上进行求值。

下面看一个n-n关系的示例。Book类型与Author类型通过一个关联实体关联。一本书可以由多名作者写，一个作者也可以写多本书。

$\color{red}想要深入学习，请自行了解！$

<br>
<hr>

### 原始SQL查询
EF Core2.0还允许定义原始查询，原始SQL查询返回实体对象并跟踪这些对象。只需要调用DbSet对象的FromSql方法，例如（BooksSample/QuerySamples.cs):

```csharp

private async Task RawSqlQuery(string publisher)
{
    Console.WriteLine($"nameof(RawSqlQuery)");

    using(var context=new BooksContext())
    {
        IList<Book> books=await context.Books.FromSql(
            $"select *from Books where Publisher={publisher}"
        ).ToListAsync();
        
        foreach(var b in books)
        {
            Console.WriteLine($"{b.Title} {b.Publisher}")
        }
    }
}

```
> 分配给RawSql方法的SQL查询需要返回作为模型一部分的实体类型，需要返回模型所有属性的数据。
> 分配给FromSql方法的SQL字符串可能看起来像SQL注入，因为字符串被定义了。然而，事实并非如此。FromSql要求分配一个FormattableString类型。对于这个FormattableString，EF Core提取参数并创建SQL参数。


<hr>
<br>

### 已编译查询
对于需要反复执行的查询，可以创建一个只需要执行的已编译查询。可以使用`EF.CompileQuery `创建已编译的查询。

此方法提供了不同的泛型重载，可以在其中传递不同数量的参数。在下面代码中，创建一个查询来定义一个字符串参数。该方法需要一个委托参数，其中第一个参数是类型BooksContext，第二个参数是字符串——这里使用publisher。
定义已编译的查询后，可以使用它传递上下文和参数（代码：BooksSample/QuerySamples.cs)

```csharp

private void CompiledQuery()
{
    Func<BooksContext,string,IEnumerable<Book>> query=
      EF.CompileQuery<BooksContext,string,Book>((context,publisher)=>
        context.Books.Where(b=>b.Publisher==publisher));
    
    using(var context=new BooksContext())
    {
        IEnumerable<Book> books=query(context,"今日出版社");

        foreach(var b in books)
        {
            Console.WriteLine($"{b.Title} {b.Publisher}");
        }
    }

}

```

  可以为成员字段创建一个已编译的查询，以便在需要的时候使用它，并且可以根据需要，传递不同的上下文，调用查询。


### 全局查询过滤器

省略

### EF.Functions
EF Core允许自定义扩展方法可以由提供程序实现。为此，EF类定义了DbFunctions类型的Functions属性，它可以使用扩展方法进行扩展。Like方法是关系数据提供程序的这样一种扩展。

下面代码使用EF.Functions.Like，并提供包含参数titleSegment的表达式，增强了where方法的查询。
参数titleSegment嵌入在两个%字符内（代码：BooksSample/QuerySample.cs）。
```csharp

public static async Task UseEFCunctions(string titleSegment)
{
    using(var context=new BooksContext())
    {
        string likeExpression=$"%{titleSegment}%";

        IList<Book> books=await context.Books.Where(
            b=>EF.Functions.Like(b.Title,likeExpression)
        ).ToListAsync();

        foreach(var b in books)
        {
            Console.WriteLine($"{b.Title} {b.Publisher}")
        }
    }
}
```

运行应用程序时，包含EF.Functions.Like的Where方法转换为带有Like的SQL。


<hr>
<br>


## 关系

关系可以定义为一对一、一对多。对于多对多关系，在EF Core2.0中，需要在关系中指定一个中间类，从而将该关系分割为一对多关系和多对一关系。  

关系可以使用约定、注释和流利API来指定。  


### 使用约定的关系

定义关系的第一个方法是使用约定。示例Book和Chapter类型的例子。一本书可以多个章节；因此，这是一对多关系。Book类型还定义了与作者的关系。这里，作者由User类表示。稍后，使用注释定义关系时，会解释这个名称的原因。书与作者定义了一对一的关系。
书是由Book类定义。这个类有一个主键BookId，他是根据其名称创建的。关系由Chapters属性定义。
Capters属性为List<Chapter>类型；这就是Book类型定义一对多关系所需要的全部内容。

书与作者的关系由类User的Author属性指定。还可以定义该类型的AuthorId属性指定外键，该属性与User类中的键相同。如果没有定义，就会创建一个阴影属性。（RelationUsingConventions/Books.cs)

```csharp

Books
-----------------------------------------------
using System.Collections.Generic;
namespace RelationUsingConventions
{
    public class Book
    {
        public int BookId{get;set;}
        public string Title{get;set;}
        public List<Chapter> Chapters {get;}=
          new List<Chapter>();

        public User Author {get;set;}
    }
}

```



章节由Chapter类定义。使用Book属性定义关系。一对多关系定义了乙方的集合（Book定义了Chapter对象的集合），和另一方的简单关系（Chapter定义了一个简单的属性Book）
使用这一章的Book属性，可以直接访问相关图书。对于这种类型，BookId属性指定Book的外键。正如Book类型所述，如果未将BookId指定为类的成员，则通过约定创建阴影属性。

```csharp
Chapter
-----------------------------------------------
public class Chapter
{
    public int ChapterId{get;set;}
    public int Number{get;set;}
    public string Title{get;set;}
    public int BookId{get;set;}
    public Book Book{get;set;}
}

```


User类定义了逐渐的UserId属性、关系的Name属性和AuthoredBooks属性。
```csharp
User
----------------------------------------------
public class User
{
    public int UserId{get;set;}
    public string Name{get;set;}
    public List<Book> AuthoredBooks{get;set;}
}

```

 > $\color{red}注意:$Chapter属性的另一种可能实现时定义List<Chapter>类型的读写属性，而不需要事先创建实力。

 上下文只需要使用DbSet<T>类型的属性为Book、Chapter和User类型指定属性。

 ```csharp
using Microsoft.EntityFrameworkCore;
namespace RelationUsingConventions
{

    public class BooksContext:DbContext
    {
        private const string ConnectionString =   @"server=localhost;" +
            @"database=BooksStore;" +
            @"trusted_connection=true;";
        
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            base.OnConfiguring(optionsBuilder);

            optionsBuilder.UseSqlServer(ConnectionString);
        }

        public DbSet<Book> Books{get;set;}
        public DbSet<Chapter> Chapters{get;set;}
        public DbSet<User> Users{get;set;}
    }
}

 ```


 在启动应用程序时，使用按照约定定义的映射创建数据库。

<hr>
<br>

 ### 显示加载相关数据
 如果查询书籍并希望显示相关属性（例如：相关章节和作者），则可以使用关系的显示加载。

 查询请求所有具有指定标题的图书，并且只需要一个记录。如果试图在启动查询之后访问所得图书的Chapters和Author属性，那么这些属性的值为null。关系不是隐式加载的。EF Core使用上下文的Entry方法来支持显示加载，该方法通过传递一个实体来返回EntityEntry对象。

 EntityEntry类定义了允许显示加载关系的Collection和Reference方法。
 - 对于一对多关系，可以使用Collection方法来指定集合。
 - 而一对一关系需要Reference方法来指定关系，使用Load方法进行显示加载。（RelationUsingConventions/Program.cs)


```csharp

private static void ExplicitLoading()
{
    using(var context=new BooksContext())
    {
        var book=context.Books.Where(b=>b.Title.StartsWith("乌合之众")).FirstOrDefault();

        if(book!=null)
        {
            Console.WriteLine(book.Title)
            context.Entry(book).Collection(b=>b.Chapters).Load();
            context.Entry(book).Reference(b=>Author).Load();

            Console.WriteLine(book.Author.Name);
            foreach(var chapter in book.Chapters)
            {
                Console.WriteLine($"{chapter.Number}.{chapter.Title}")
            }
        }
    }
}

```


实现Load方法的NavigationEntry类也实现了IsLoaded属性，可以在其中检查关系是否已经加载。在调用Load方法之前，不需要检查加载的关系；

在调用Load方法时，如果关系已经加载，就不会再次查询数据库。
当对图书的查询运行应用程序时，下面的Select语句将在Sql Server中执行。

EF Core除了显示加载相关数据，导致想SQL Server发送多个查询之外，还支持即时加载。
<hr>
<br>

### 即时加载相关数据
当执行查询时，可以通过调用Include方法并指定关系，来立即加载相关数据。下面代码片段包括成功应用Where表达式的书中张姐和作者（RelationUsingConventions/Program.cs）。

```csharp

private static ovid EagerLoading()
{
    using(var context=new BooksContext())
    {
        var book=context.Books
          .Include(b=>b.Chapters)
          .Include(b=>b.Author)
          .Where(b=>b.Title.StartsWith("乌合之众"))
          .FirstOrDefault();
        
        if(book!=null)
        {
            Console.WriteLine(ook.Title);

            Console.WriteLine(book.Author.Name);

            foreach(var chapter in book.Chapters)
            {
                Console.WriteLine($"{chapter.Number}.{chapter.Title}");
            }
        }
    }
}

```

使用Include，只需要执行一条SQL语句来访问Books表，并连接Chapters和Users表：

如果需要包含多个层次的关系，那么方法ThenInclude可以用于Include方法的结果。


<hr>
<br>

### 使用注释的关系
与使用约定不同，实体类型可以通过应用关系信息进行注释。下面向关系属性添加` ForeignKey `属性，来修改先前创建的Book类型，并指定表示外键的属性。在这里，Book不仅与该书的作者有关联，也与审稿人和项目编辑有关联。这些关系映射到User类型。外键属性定义为int类型吗？让他们变成可选项。使用枪支关系，EF Core创建级联删除；删除Book时，相关的作者、编辑和审稿人也被删除。（RelationUsingAnnotations/Book.cs）


```csharp

using System.Collections.Generic;
using System.ComponentModel.DataAnnotations.Schema;

public class Book
{
    public int BookId {get;set;}
    public string Title {get;set;}
    public List<Chapter> Chapters{get;}=new List<Chapter>();

    public int? AuthorId {get;set;}

    [ForeignKey(nameof(AuthorId))]
    public User Author {get;set;}
    public int? ReviewerId {get;set;}

    [ForeignKey(nameof(ReviewerId))]
    public User Reviewer {get;set;}

    public int? ProjectEditorId {get;set;}

    [ForeignKey(nameof(ProjectEditorId))]
    public User ProjectEditor {get;set;}
}

```


```csharp
User类与Book类型有多个关联
-----------------------------------------------
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations.Schema;

public class User
{
    public int UserId {get;set;}

    public string Name {get;set;}

    [InverseProperty("Author")]
    public List<Book> WritenBooks {get;set;}

    [InverseProperty("Reviewer")]
    public List<Book> ReviewedBooks {get;set;}

    [InverseProperty("ProjectEditor")]
    public List<Book> EditedBooks {get;set;}
}

```

WritenBooks属性列出了添加为author的用户的所有图书。类似，ReviewedBooks和EditedBooks属性与Book类型仅仅在Reviewer和ProjectEditor属性上相关联。

如果相同类型之间存在多个关系，则需要使用InverseProperty特性对属性进行注释。使用这个特性，指定关系另一端的相关属性。



<hr>
<br>

### 使用流利API的关系
指定关系最强大方法是使用流利API。

- 在流利API中，使用hasOne和WithOne方法定义一对一关系。
- 使用HasOne和WithMany方法定义一对多关系；
- 使用HasMany和WithOne定义多对一的关系。

模型类型不包括数据库模式上的任何注释。（RelationUsingFluentAPI/Book.cs)。

```csharp
Book类是一个简单的POCO类型，他定义了图书信息的属性，包括关系属性。
--------------------------------------------------------
public class Book
{
    public int BookId {get;set;}
    public string Title {get;set;}
    public List<Chaper> Chapters {get;}=new List<Chapter>();

    public User Author {get;set;}     //作者
    public User Reviewer {get;set;}   //审稿人
    public User Editor {get;set;}     //编辑
}

```

User类型的定义也是类似。除了具有Name属性外，User类型还定义了与Book类型的三种不同关系。

```csharp
User类型的定义也是类似。除了具有Name属性外，User类型还定义了与Book类型的三种不同关系。
---------------------------------------------------------
public class User
{
    public int UserId {get;set;}
    public string Name {get;set;}

    public List<Book> WritenBooks {get;set;}
    public List<Book> ReviewedBooks {get;set;}
    public List<Book> EditedBooks {get;set;}
}

```



```csharp
Chapter类与Book类有关联。然而，Chapter类与Book类不同，因为Chapter类还定义了一个属性来关联一个外键：BookId。
--------------------------------------------------------

public class Chapter
{
    public int ChapterId {get;set;}
    public int Number {get;set;}
    public string Title {get;set;}
    public int BookId {get;set;}
    public Book Book {get;set;}
}

```

模型类型之间的映射现在在BooksContext的OnModelCreating方法中定义。

- Book类与多个Chapter对象相关联；这是使用HasMany和WithOne定义的。
- Chapter类与一个Book对象相关联；这是使用HasOne和WithMany定义的。因为Chapter类中还有一个外键属性，所以使用HasForeignKey方法来指定这个外键。

```csharp

using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;
using System.Text;

public class BooksContext:DbContext
{
    private const string ConnectionString =
      @"server=localhost;" +
      @"database=BooksStore;" +
      @"trusted_connection=true;";
    
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        base.OnConfiguring(optionsBuilder);

        optionsBuilder.UseSqlServer(ConnectionString);
    }


    public DbSet<Book> Books {get;set;}
    public DbSet<Chapter> Chapters {get;set;}
    public DbSet<User> Users {get;set;}

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>().HasMany(b=>b.Chapters).WithOne(c=>c.Book);
        modelBuilder.Entity<Book>().HasOne(b=>b.Author).WithMany(a.WrittenBooks);
        modelBuilder.Entity<Book>().HasOne(b=>b.Reviewer).WithMany(r=>r.ReviewedBooks);
        modelBuilder.Entity<Book>().HasOne(b=>b.Editor).WithMany(e=>e.EditedBooks);


        modelBuilder.Entity<Chapter>().HasOne(c=>c.Book).WithMany(b=>b.Chapters).HasForeignKey(c=>c.BookId);


        modelBuilder.Entity<User>().HasMany(a=>a.WrittenBooks).WithOne(b=>b.Author);
        modelBuilder.Entity<User>().HasMany(r=>r.ReviewedBooks).WithOne(b=>b.Reviewer);
        modelBuilder.Entity<User>().HasMany(e=>e.EditedBooks).WithOne(b=>b.Editor);
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

            CreateDatabase();
            AddBooks();
            GetBooksByUsers();

            //DeleteDatabase();



            Console.ReadKey();
        }


        //创建数据库
        private static void CreateDatabase()
        {
            using(var context=new BooksContext())
            {
                context.Database.EnsureCreated();
            }
        }


        //删除数据库
        private static void DeleteDatabase()
        {
            Console.WriteLine("删除这个数据库吗？");
            string input = Console.ReadLine();
            if (input.ToLower() == "y")
            {
                using(var context=new BooksContext())
                {
                    context.Database.EnsureDeleted();
                }
            }
        }

        //添加数据
        private static void AddBooks()
        {
            Console.WriteLine(nameof(AddBooks));
            using (var context = new BooksContext())
            {
                var author = new User
                {
                    Name = "赵峰"
                };
                var reviewer = new User
                {
                    Name = "雅淇"
                };
                var editor = new User
                {
                    Name = "雅楠"
                };
                var b1 = new Book
                {
                    Title = "乌合之众",
                    Editor= editor,
                    Reviewer = reviewer,
                    Author = author
                };

                var c1 = new Chapter
                {
                    Title = "早上起床",
                    Number = 1,
                    Book = b1
                };
                var c2 = new Chapter
                {
                    Title = "中午吃饭",
                    Number = 2,
                    Book = b1
                };
                var c3 = new Chapter
                {
                    Title = "晚上睡觉",
                    Number = 28,
                    Book = b1
                };

                context.Books.Add(b1);
                context.Users.AddRange(author, editor, reviewer);
                context.Chapters.AddRange(c1, c2, c3);

                int records = context.SaveChanges();

                Console.WriteLine($"{records} 条记录被增加");
            }
            Console.WriteLine();
        }




        //显示数据
        private static void GetBooksByUsers()
        {
            using(var context=new BooksContext())
            {
                var users = context.Users.Include(u => u.WrittenBooks)
                    .Include(u => u.ReviewedBooks)
                    .Include(u => u.EditedBooks);

                foreach(var user in users)
                {
                    Console.WriteLine(user.Name);

                    foreach(var book in user.WrittenBooks)
                    {
                        Console.WriteLine($"\t 书名:{book.Title}");
                    }
                    
                    foreach(var book in user.ReviewedBooks)
                    {
                        Console.WriteLine($"\t 审稿人:{book.Title}");
                    }

                    foreach(var book in user.EditedBooks)
                    {
                        Console.WriteLine($"\t 编辑:{book.Title}");
                    }

                }
            }
        }


    }
}


```


<hr>
<br>

### 根据约定的每个层次结构的表
EF Core还支持每个层次结构中标（TablePerHierarchy，TPH）关系类型。使用这种关系，形成层次结构的多个模型类用于映射到单个表。这种关系可以使用约定和流利API来指定。

下面开始使用约定和形成层次结构的类型Payment、CashPayment、CreditcardPayment。Payment是一个基类；CashPayment和CreditcardPayment均派生自它。

在实现代码中，Payment类定义了主键，其中包括PaymentId属性、所需的Name和Amount属性。Amount属性映射到数据库中的一个列类型Money（TPHWithConventions/Payment.cs）。

pyment: 支付
creditcard Payment：信用卡支付
cash Payment: 现金支付

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace TPHWithConventions
{
    public class Payment
    {
        public int PaymentId { get; set; }

        [Required]
        public string Name { get; set; }
        [Column(TypeName = "Money")]
        public decimal Amount { get; set; }
    }
}


```

CreditcardPayment类派生自Payment，还添加了CreditcardNumber属性。

```csharp
public class CreditCardPayment:Payment
{
    public string CreditcardNumber {get;set;}
}

```


CashPayment类派生自Payment，但不生命任何其他成员.
```csharp
public class CashPayment:Payment
{

}
```


EF Core的上下文类BankContext为层次结构中的每个类定义了DbSet属性。


```csharp
using Microsoft.EntityFrameworkCore;

public class BankContext:DbContext
{
    private const string ConnectionString =
      @"server=localhost;" +
      @"database=BooksStore;" +
      @"trusted_connection=true;";

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        base.OnConfiguring(optionsBuilder);

        optionsBuilder.UseSqlServer(ConnectionString);
    }      

    public DbSet<Payment> Payment {get;set;}
    public DbSet<CreditcardPayment> CreditcardPayments {get;set;}
    public DbSet<CashPayment> CashPayments {get;set;}
}

```


定义了两个CashPayment和一个CreditcardPayment支付。

```csharp
public class Program
{
    static void Main(string[] args)
    {
        CreateDatabase();
        AddSampleData();
        QuerySample();
        DeleteDatabase();
    }


    private static void AddSampleData()
    {
        using(var context=new BankContext())
        {
            var t1=new CashPayment{Name="zhaofeng" ,Amount=0.5m}
            var t2=new CashPayment{Name="ya na" ,Amount=2000m}

            context.CashPayments.Add(t1);
            context.CashPayments.Add(t2);

            var t3=new CreditcardPayment{Name="ya qi" ,Amount=300m
             ,CreditcardNumber="987654321"}
             context.CreditcardPayments.Add(t3);

             context.SaveChanges();
        }
    }
}

//当运行应用程序来创建数据库时，只创建了一个表Payments。这个表定义了一个Discriminator列，将记录从表映射到相应的模型类型。
//只要查询层次结构中的特定类型，可以使用OfType扩展方法。在下面的代码中可以看到一个查询，该查询只返回Creditcardpayment类型的支付。

    private static void QuerySample()
    {
        using(var context=new BankContext())
        {
            var creditcardPayments=context.Payments.OfType<CredditcardPayment>();

            foreach(var payment in creditcardPayments)
            {
                Console.WriteLine($"{payment.Name},{payment.Amount}")
            }
        }
    }

//使用OfType ，EF Core创建一个带有Where子句的查询，该子句只区分值为CreditcardPayment的记录：
//这个场景，还可以调用上下文属性CreditcardPayments，得到相同的查询。

```

<hr>
<br>

### 使用流利API的每个层次结构中的表
使用流利API，定义层次结构可以有更多的控制。这样，Payment类就从注释中剥离出来，它现在时一个抽象类型。

```csharp

//抽象类 Payment
---------------------------------------------
public abstract class Payment
{
    public int PaymentId{get;set;}

    public string Name {get;set;}
    public decimal Amount{get;set;}
}


//CashPayment 现金支付
---------------------------------------------
public class CashPayment:Payment{}


//CreditcardPayment 信用卡支付
public class CreditcardPayment:Payment
{
    public string CreditcardNumber {get;set;}
}

```

鉴别器的新名称时Type。这应该时Payments表中的一列，但不应该显示在Payment类型中。应该使用字符串Cash和Creditcard来区分模型类型。对于所有字符串，定义了ColumnNames和ColumnValues类。

```csharp
using Microsoft.EntityFrameworkCore;

//鉴别器名称type
public static class ColumnNames
{
    public const string Type=nameof(Type);
}


-------------------------------------------
// 字段
public static class ColumnValues
{
    public const string Cash=nameof(Cash);
    public const string Creditcard=nameof(Creditcard);
}


------------------------------------------

public class BankContext:DbContext
{
     private const string ConnectionString =   
       @"server=localhost;" +
       @"database=BooksStore;" +
       @"trusted_connection=true;";
    
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        base.OnConfiguring(optionsBuilder);

        optionsBuilder.UseSqlServer(ConnectionString);
    }


    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Payment>().Property(p=>p.Name).IsRequired();
        modelBuilder.Entity<Payment>().Property(p=>p.Amount).HasColumnType("Money");
        modelBuilder.Entity<Payment>().Property<string>(ColumnNames.Type)
        modelBuilder.Entity<Payment>().HasDiscriminator<string>(ColumnNames.Type)
          .HasValue<CashPayment>(ColumnValues.Cash)
          .HasValue<CreditcardPayment>(ColumnValues.Creditcard);
    }


    public DbSet<Payment> Payments{get;set;}
}


```

这次，上下文为所有不同的Payment类型定义了一个属性Payments。当然，也可以有专门的属性，但是在前面的示例中，这是必须的。

对于Name属性和Amount属性的Money类型，需要的模式信息现在是在方法OnModelCreating中指定，而不是使用注释指定。

使用HasDiscriminator方法指定TPH层次结构。鉴别器的名称是Type，它也指定为一个阴影属性。派生类型的差异用HasValue方法指定。HasValue是DiscriminatorBuilder的一个方法，它是从HasDiscriminator方法返回的。


创建的数据库与以前类似，只是表Payments现在定义了Type列，而不是Discriminator列，就像创建模型时指定的那样。询问信用卡号码的新查询会筛选Type列。
```sql
select p.paymentId,p.Amount,p.Name,p.Type,p.CreditcardNumber
from Payments as p
Where p.Type=N'Creditcard'

```



<hr>
<br>

### 表的拆分
通过表的拆分，可以将数据库表拆分成多个实体类型。使用表拆分特性，属于同一个表的每个类都需要一个一对一关系，并定义自己的主键。但是，因为他们共享同一个表，所以也共享相同的主键。

下面时一个Menu类的示例，它表示关于午餐菜单的信息，MenuDetails包含厨房的信息。Menu类为菜单定义了一些属性，包括Details属性。Details属性将关系映射到MenuDetails类。（TableSplitting/Menu.cs）

```csharp

public class Menu
{
    public int MenuId{get;set;}
    public string Title{get;set;}
    public string Subtitle{get;set;}
    public decimal Price{get;set;}
    public MenuDetails Details{get;set;}
}
```


MenuDetails类看起来会映射到它自己的表（带有主键），并映射到具有Menu属性的Menu类。
```csharp
public class MenuDetails
{
    public int MenuDetailsId{get;set;}
    public string KitchenInfo{get;set;}
    public int MenusSold{get;set;}
    public Menu Menu{get;set;}
}
```

上下文中，Menus和MenuDetails是两个DbSet属性。在OnModelCreating方法中，Menu类使用HasOne和WithOne配置为与MenuDetails的一对一关系。现在应该注意ToTable方法的调用。

默认情况下，类Menu和MenuDetails将映射到两个不同的表。这里，传递给ToTable方法的参数指定了相同的表明。

Menu和MenuDetails都映射到相同的表Menu。这就造成了表分割的差异。


```csharp
using Microsoft.EntityFrameworkCore;

namespace TableSplitting
{
    public static class SchemaNames
    {
        public const string Menus = nameof(Menus);
    }

    public class MenusContext : DbContext
    {
        private const string ConnectionString = @"server=(localdb)\MSSQLLocalDb;" +
            "Database=Menus;Trusted_Connection=True";

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            base.OnConfiguring(optionsBuilder);

            optionsBuilder.UseSqlServer(ConnectionString);
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Menu>().HasOne<MenuDetails>(m => m.Details).WithOne(d => d.Menu).HasForeignKey<MenuDetails>(d => d.MenuDetailsId);
            modelBuilder.Entity<Menu>().ToTable(SchemaNames.Menus);
            modelBuilder.Entity<MenuDetails>().ToTable(SchemaNames.Menus);
            modelBuilder.Entity<Menu>().Property(m => m.Price).HasColumnType("money");
        }

        public DbSet<Menu> Menus { get; set; }
        public DbSet<MenuDetails> MenuDetails { get; set; }
    }
}

```
通过SQL可以看到，Menu表包含Menu和MenuDetails类的列，以及只用于Menu类的主键。

<hr>
<br>

### 拥有的实体
将表分割为多个实体类型的另一种方法是使用“拥有实体”的特性。
拥有的实体不需要主键；他们可以是在正常实体中拥有的类型。拥有实体的实体类可以映射到单个表——使用表拆分特性——也可以映射到不同的表。当使用不同的表时，他们共享相同的主键。

示例展示了两种场景：使用拥有的实体和单个表，并将其映射到另一个表。

下面代码显示了主要的实体类型Person。这是带有主键PersonId的拥有实体的所有者。该类型包含两个地址：
PrivateAddress和CompanyAddres（代码：OwnedEntities/Person.cs)。

```csharp

public class Person
{
    public int PersonId{get;set;}
    public string Name{get;set;}
    public Address PrivateAddress{get;set;}
    public Address CompanyAddress{get;set;}
}

```

Address是一个拥有的实体，该类型没有它自己的主键。该类型有两个字符串属性，以及一个类型为Location的关系Location。Location是另一个拥有的实体。
```csharp
public class Address
{
    public string LineOne{get;set;}
    public string LineTwo{get;set;}
    public Location Location{get;set;}
}
```

Location只包含Country和City属性，作为一个拥有的实体，它没有定义一个键。
```csharp
public class Location
{
    public string Country{get;set;}
    public string City{get;set;}
}
```

最有趣的部分出现在上下文中，其中在OnModelCreating方法中定义了拥有的实体。为了给Person类定制模型，OwnsOne的第一次调用指定Person实体拥有从CompanyAddress属性（这是一种Address类型）医用的实体。
对OwnsOne的第二个调用现在使用第一个OwnsOne调用（一个ReferenceOwnershipBuilder）的返回类型调用Owns One。

这样，Address就定义为拥有一个Location。对于第二个调用，使用OwnsOne的另一个重载，允许进行一些限制。显示此自定义后，指定City和Country属性的列名于默认名称不同。
CompanyAddress定制的结果是，用于CompanyAddress和Location的列都包含在People表中，为City和Country属性提供定制列名。使用下一个OwnsOne调用的定制定义了PrivateAddress属性的所有权。这一次，Address类型映射到另一个表：
映射到名为Addr的表。这个表包含了Location类中的列，且带有默认列名。

```csharp
using Microsoft.EntityFrameworkCore;

namespace OwnedEntities
{
    public class OwnedEntitiesContext : DbContext
    {
        private const string ConnectionString = @"server=(localdb)\MSSQLLocalDb;" +
            "Database=OwnedEntities;Trusted_Connection=True";

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            base.OnConfiguring(optionsBuilder);
           
            optionsBuilder.UseSqlServer(ConnectionString);
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Person>()
                .OwnsOne(p => p.CompanyAddress)
                .OwnsOne(a => a.Location, builder =>
                {
                    builder.Property(p => p.City).HasColumnName("BusinessCity");
                    builder.Property(p => p.Country).HasColumnName("BusinessCountry");
                });
            modelBuilder.Entity<Person>().OwnsOne(p => p.PrivateAddress).ToTable("Addr").OwnsOne(a => a.Location);
        }

        public DbSet<Person> People { get; set; }
    }
}

```