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
