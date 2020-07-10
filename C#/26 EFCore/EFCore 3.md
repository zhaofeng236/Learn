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
