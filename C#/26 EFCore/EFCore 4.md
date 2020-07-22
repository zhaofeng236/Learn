## 保存数据
### 用关系添加对象
MenuCard包含Menu对象。其中，实例化MenuCard和Menu对象，在制定双向关联，对于Menu，将MenuCard属性分配给MenuCard。调用MenuCards属性的方法Add，把MenuCard实例添加到上下文中。将对象添加到上下文时，默认情况下所有对象都添加到树种，并添加状态。不仅保存MenuCard，还保存Menu对象。在上下文调用SaveChanged会创建4个记录。(MenusSample/Propram.cs)

```csharp
    //==以下是Menu类==
    public class Menu
    {
        public int MenuId { get; set; }
        public string Text { get; set; }
        public decimal Price { get; set; }
        //
        public int MenuCardId { get; set; }
        public MenuCard MenuCard { get; set; }
        public override string ToString() => Text;
    }

    //==MenuCard类==
    public class MenuCard
    {
        public int MenuCardId { get; set; }
        public string Title { get; set; }
        public List<Menu> Menus { get; } = new List<Menu>();
        public override string ToString() => Title;
    }


    //==Menu配置文件==
    public class MenuConfiguration:IEntityTypeConfiguration<Menu>
    {
        public void Configure(EntityTypeBuilder<Menu> builder)
        {
            builder.ToTable("Menus")
                .HasKey(m => m.MenuId);

            builder.Property(m => m.MenuId)
                .ValueGeneratedOnAdd();

            builder.Property(m => m.Text)
                .HasMaxLength(120);

            builder.Property(m => m.Price)
                .HasColumnType("Money");

            builder.HasOne(m => m.MenuCard)
                .WithMany(m => m.Menus)
                .HasForeignKey(m => m.MenuCardId);
        }
    }

    //==MenuCard配置文件==
    public class MenuCardConfiguration:IEntityTypeConfiguration<MenuCard>
    {
        public void Configure(EntityTypeBuilder<MenuCard> builder)
        {
            builder.ToTable("MenuCards")
                .HasKey(c => c.MenuCardId);

            builder.Property(c => c.MenuCardId)
                .ValueGeneratedOnAdd();

            builder.Property(c => c.Title)
                .HasMaxLength(50);

            builder.HasMany(c => c.Menus)
                .WithOne(m => m.MenuCard);

            builder.Property<DateTime>("LastUpdated");
            builder.Property<bool>("IsDeleted");
        }
    }


    //==Menu上下文MenuContext==
    public class MenusContext:DbContext
    {
        private const string ConnectionString =
          @"server=localhost;" +
          @"database=MenuStore;" +
          @"trusted_connection=true;";

        public DbSet<Menu> Menus { get; set; }
        public DbSet<MenuCard> MenuCards { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            base.OnConfiguring(optionsBuilder);
            optionsBuilder.UseSqlServer(ConnectionString);
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);
            modelBuilder.HasDefaultSchema("mc");

            modelBuilder.ApplyConfiguration(new MenuCardConfiguration());
            modelBuilder.ApplyConfiguration(new MenuConfiguration());
        }
    } 


    //==program文件==
        private static void AddRecords()
        {
            Console.WriteLine(nameof(AddRecords));
            try
            {
                using(var context=new MenusContext())
                {
                    var soupCard = new MenuCard();
                    Menu[] soups =
                    {
                        new Menu
                        {
                            Text="红烧钳子",
                            Price=28m,
                            MenuCard=soupCard
                        },

                        new Menu
                        {
                            Text="白族生肉",
                            Price=40m,
                            MenuCard=soupCard
                        },

                        new Menu
                        {
                            Text="宾川海稍鱼",
                            Price=88m,
                            MenuCard=soupCard
                        },

                        new Menu
                        {
                            Text="丽江腊排骨",
                            Price=68m,
                            MenuCard=soupCard
                        },
                    };

                    soupCard.Title = "Soups";
                    soupCard.Menus.AddRange(soups);
                    context.MenuCards.Add(soupCard);

                    ShowState(context);
                    int records = context.SaveChanges();
                    Console.WriteLine($"{records} 条记录被插入!");
                    Console.WriteLine();
                }
            }catch(Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            Console.WriteLine();
        }
    
    //==显示状态==
    //==ChangeTracker的Entries方法返回变更跟踪器的对象==
        public static void ShowState(MenusContext context)
        {
            foreach(EntityEntry entry in context.ChangeTracker.Entries())
            {
                Console.WriteLine($"类型：{entry.Entity.GetType().Name}," +
                    $"状态:{entry.State},{entry.Entity}");
            }
        }


```

给上下文添加4个对象后调用的方法ShowState显示了所有与上下文相关的对象的状态。

DbContext类有一个相关的ChangeTracker，使用ChangeTracker属性可以访问它。ChangeTracker的Entries方法返回变更跟踪器了解的所有对象。在foreach循环中，每个对象包括其状态都写入控制台。
因为这个状态，SaveChanges方法创建SQL Insert语句，把每个对象都写到数据库中。

### 对象的跟踪

上下文知道添加的对象。然而，上下文也需要了解变更。每个检索的对象就需要他在上下文中的状态。为了查看这个状态，下面创建两个不同的查询，但返回相同的对象。

下面的代码定义了两个不同的查询，每个查询都用菜单返回相同的对象，因为他们都存储在数据库中。
事实上，只有一个对象会物化，因为在第二个查询的结果中，返回的记录具有的主键值从上下文中引用的对象相同。验证在返回相同的对象时，变量m1和m2的引用是否具有相同的结果。

```csharp
        private static void ObjectTracking()
        {
            Console.WriteLine(nameof(ObjectTracking));
            using(var context=new MenusContext())
            {
                var m1=(from m in context.Menus
                        where m.Text.StartsWith("Con")
                        select m).FirstOrDefault();

                var m2 = (from m in context.Menus
                          where m.Text.Contains("(")
                          select m).FirstOrDefault();

                if (object.ReferenceEquals(m1, m2))
                {
                    Console.WriteLine("对象相同！");
                }
                else
                {
                    Console.WriteLine("对象不相同！");
                }
                ShowState(context);
            }
            Console.WriteLine();
        }
```
第一个LINQ查询得到一个带有LIKE比较的SQL SELECT语句，来比较以Con开头的字符串：
```sql
select top(1) m.MenuId,m.MenuCardId,m.Price,m.Text
from mc.Menus as m
where m.Text LIKE 'Con'+'%'

//第二个LINQ查询中，也需要咨询数据库。其中LIKE用于比较中间的 “（”

select top(1) m.MenuId,m.MenuCardId,m.Price,m.Text
from mc.Menus as m
where m.Text LIKE ('%'+'(')+ '%';
```

运行应用程序时，同一对象写入控制台，只有一个对象用ChangeTracker保存。
为了不跟踪在数据库中运行的查询的对象，可以通过DbSet调用AsNoTracking方法：
```csharp
var m1=(from m in context.Menus.AsNoTracking()
    where m.Text.StartsWith("Con")
    select m).FirstOrDefault();
```

可以把ChangeTracker的默认跟踪欣慰配置为QueryTrackingBehavior.NoTracking：

```csharp
using(var context=new MenusContext())
{
    context.ChangeTracker.QueryTrackingBehavior=
       QueryTrackingBehavior.NoTracking;
}



对于更全局的配置，跟踪行为也可以用SQL Server配置来配置。

protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    base.OnConfiguring(optionsBuilder);
    optionsBuilder.UseSqlServer(ConnectionString)
      .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
}
```
有了这样的配置，给数据库建立两个查询，物化两个对象，状态信息是空的。

> ==**注意：上下文只用于读取记录时，可以使用NotTracking配置，但无法修改。这减少了上下文的开销，因为不保存状态信息**==

### 更新对象
跟踪对象时，对象可以轻松地更新，首先，检索Menu对象。使用这个被跟踪的对象，修改价格，在把变更写入数据库。在所有的变更之间，将状态信息写入控制台。

```csharp
private static void UpdateRecords()
{
   Console.WriteLine(nameof(UpdateRecords));
   using(var context=new MenusContext())
   {
      Menu menu = context.Menus
        .Skip(1)
        .FirstOrDefault();
      ShowState(context);
      menu.Price += 20m;
      ShowState(context);
      int records = context.SaveChanges();
      Console.WriteLine($"{records} 条记录被更新！");
      ShowState(context);
   }
    Console.WriteLine();
}

out:
UpdateRecords
类型：Menu,状态:Unchanged,白族生肉
类型：Menu,状态:Modified,白族生肉
1 条记录被更新！
类型：Menu,状态:Unchanged,白族生肉
```
运行应用程序时，可以看到，加载记录后，对象的状态时Unchanged；
修改价格（属性）后，对象的状态是Modified；
保存完成后，对象的状态是Unchanged；

访问更改跟踪其中的条目时，默认情况下会自动检测到变更。要配置ChangeTracker的AutoDetectChangesEnabled属性。为了手动检查更改是否已经完成，调用DetectChanges方法。调用SaveChnagesAsync后，状态改回Unchanged。调用AcceptAllChnages方法可以手动完成这个操作。


### 更新为跟踪的对象
DB上下文通常非常短寿。使用EF Core与ASP.NET Core MVC，通过一个HTTP请求创建一个对象上下文，来检索对象。从客户端接收一个跟新时，对象必须在服务器上创建。这个对象与对象的上下文相关联。
为了在数据库中跟新它，对象需要与DB上下文相关联，修改状态，创建INSERT、UPDATE、DELETE语句。

这样的情景用下一个片段模拟。本地函数GetMenu返回一个脱离上下文的Menu对象；上下文在该本地函数的最后销毁。GetMenu方法由ChangeUntracked方法调用。这个方法修改不予任何上下文相关的Menu对象。改变后，Menu对象传递到方法UpdateUntracked，保存到数据库中。

```csharp
        private static void ChangeUntracked()
        {
            Console.WriteLine(nameof(ChangeUntracked));
            Menu GetMenu()
            {
                using(var context=new MenusContext())
                {
                    Menu menu = context.Menus
                        .Skip(0)
                        .FirstOrDefault();
                    return menu;
                }
            }

            Menu m = GetMenu();
            m.Price += 50m;
            UpdateUntracked(m);
        }

//UpdateUntracked方法接受已更新的对象，需要把它与上下文关联起来。对象与上下文关联起来的一个方法就时调用DbSet的Attach方法，并根据需要设置状态。Update方法用一个调用完成了这两个操作：关联对象，把状态设置为Modified。


        private static void UpdateUntracked(Menu m)
        {
            using(var context=new MenusContext())
            {
                ShowState(context);
                // EntityEntry<Menu> entry=context.Menus.Attach(m);
                // entry.State=EntityState.Modified;

                context.Menus.Update(m);

                ShowState(context);
                context.SaveChanges();
            }
        }
```
通过ChnageUntracked方法运行应用程序时，可以看到状态的修改。对象起初没有被跟踪，但时，因为显示地跟新了状态，所以可以看到Modified状态。



### 批处理
对象映射工具不支持所有场景。例如，如果城市的邮政编码更改为新代码，并且希望把所有客户的旧邮政编码更新为新代码，最好调用一个SQL UPDATE语句来更新所有记录。使用EF Core，为每个客户生成跟新语句。

但是，EF Core对于通过一个SaveChnages调用发送一系列单独的SQL语句并没有那么糟糕。EF Core支持批处理。SaveChanges想SQL Server发送一个命令，其中仅用一条语句执行多个插入或更新操作。可以控制批处理的大小——例如，当配置SQL Server时，通过调用值为1的MaxBatchSize（）来禁用批处理。
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    base.OnConfigruing(optionsBuilder);
    OptionsBuilder.UseSqlServer(ConnectionString,options=>options.MaxBatchSize(1));
}
```

下面代码创建100个菜单，添加到上下文中，用SaveChanges方法将其写入数据库中。

```csharp
        private static void AddHundredRecords()
        {
            Console.WriteLine(nameof(AddHundredRecords));
            using (var context = new MenusContext())
            {
                var card = context.MenuCards.FirstOrDefault();
                if (card != null)
                {
                    var menus = Enumerable.Range(1, 20000).Select(x => new Menu
                    {
                        MenuCard = card,
                        Text = $"menu {x}",
                        Price = 9.9m
                    });
                    context.Menus.AddRange(menus);
                    
                    Stopwatch stopwatch = Stopwatch.StartNew();
                    int records = context.SaveChanges();
                    stopwatch.Stop();
                    Console.WriteLine($"{records} 条记录被插入，用时： {stopwatch.ElapsedMilliseconds} 毫秒");
                }
            }
            Console.WriteLine();
        }
```

启用批处理，运行应用程序时，EF Core提供程序创建一个Table Merge语句，其中使用一条语句写入100条记新菜单记录。将批处理大小更改为1100个Insert语句，发送到数据库。数据库在同一系统上运行时，启动批处理的时间间隔是56毫秒，而没有批处理的时间间隔是105毫秒。如果数据库在不同系统上运行，差异会更大。

<hr>
<br>

## 冲突的处理
如果访问同一个数据库的多个用户处理不同的记录，就没有冲突。所有用户都可以保存他们的数据，而不干扰其他用户编辑的数据。但是，如果多个用户处理同一条记录，就需要如何解决冲突。
最简单的一个方法就是最后一个用户获胜。最后保存数据的用户覆盖以前用户执行的变更。

EF Core还提供了一种方式，使第一个保存数据的用户获胜。采用这一选项，保存记录时，需要验证最初读取的数据是否仍在数据库中。如果是就继续保存数据，因为读写操作之间没有发生变化。然而，如果数据发生了变化，就需要解决冲突。


### 最后一个变更获胜
默认情况下，最后一个保存的变更获胜。为了查看对数据库的多个访问，扩展BooksSample应用程序。

为了简单模拟两个用户，方法ConflictHanding调用两次方法Prepareupdate
,对两个引用相同的记录的Book对象进行不同的改变，并调用Update方法两次。最后，把书的ID传递给CheckUpdate方法，它显示了数据库中书的实际状态。(BooksSample
/Program.cs)

```csharp

//PrepareUpdate方法打开一个BookContext，并在元组中返回上下文和图书。记住，该方法调用两次，返回与不同context对象相关的不同Book对象。

private static (BooksContext context,Book book) PrepareUpdate()
{
    var context=new BooksContext();
    Book book=await context.Books
      .Where(b=>b.Title==BookTitle)
      .FirstOrDefault();
    return (context,book);
}


private static void Update(BooksContext context,Book book,string user)
{
    int records=context.SaveChanges();
    Console.WriteLine($"{user}: {records} record updated from {user}");
}


private static void CheckUpdate(int id)
{
    using(var context=new BooksContext())
    {
        Book book=context.Books.Fiind(id);
        Console.WriteLine($"updated: {book.Title}");
    }
}


public static void ConfictHandling()
{
    var tuple1=await PrepareUpdate();
    tuple1.book.Title="updated from user 1";

    var tuple2=await PrepareUpdate();
    tuple2.book.Title="updated from user 2";

    Update(tuple1.context,tuple1.book,"user 1");

    UpdateAsync(tuple2.context,tuple2.book,"user 2");

    tuple1.context.Dispose();
    tuple2.context.Dispose();

    CheckUpdate(tuple1.book.BookId);
}

```

Update方法接收打开的BooksContext与更新的Book对象，把这本书保存到数据库中。记住，该方法调用两次。

### 第一个更改获胜。



<hr>
<br>

## 上下文池
在EF Core2.0中，可以把上下文放在池中，已提供性能。连接已经放在池中好长时间了。应在需要连接之前打开他们，使用之后立即关闭他们。在EF Core中，这个行为已经在框架中实现了。
关闭连接时，对数据库服务器的连接并没有真正关闭，而是把连接返回连接池，以便在打开下一个连接时重用。连接池用连接字符串配置。

DB上下文的用法规则与连接类似。他们也应在需要之前创建，在使用之后立即关闭（销毁）。其开销没有我们想象的那么大。模型不是在调用每个新上下文时初始化，而是重用模型。

在Entity Framework和XML文件映射中，创建上下文的开销比目前的EF Core大许多。但是，如果上下文比较大，创建它的开销仍可能比较可观。此时需要上下文池来提高性能。

为了使用上下文池，必须使用依赖注入。要激活上下文池，只需要把EF Core注册从AddDbContext改为AddDbContextPool。这样，注入的上下文（BooksContext）就可以从上下文池中检索出来。

```csharp
var services=new ServiceCollection();

services.AddTransient<BooksController>();
services.AddTransient<BooksService>();
services.AddEntityFrameworkSqlServer();

serivces.AddDbContextPool<BooksContext>(options=>
  options.UseSqlServer(ConnectionString));

services.AddLogging();
Container=services.BuildServiceProvider();
```


### EF Core的常规“CRUD”及状态的变化
#### 状态说明补充
1. DeTached：游离的状态，与数据库没有什么交涉，比如新new一个实体，状态就是Detached。
2. Added：增加的状态。
3. Deleted：删除的状态。
4. Modified：修改的状态；
5. Unchanged：与数据库内容相比，未发生变化时的状态，从数据库中查询出来的对象就是Unchanged状态。

查询出来的实体，AsNoTracking一下，就编程Detached游离状态了。
```csharp
using(DbContext db=new EFDB01Context())
{
    var d1=db.Set<T_UserInfor>().where(u=>u.id="01").FirstOrDefault();
    Consle.WriteLine(db.Entry(d1).State);  //Unchanged
    
    var d2=db.Set<T_UserInfor>().AsNoTracking()
      .Where (u=>u.id=="01").FirstOrDefault();
      Console.WriteLine(db.Entry(d2).State);  //Detached
}
```

### 增加
1. 原理：修改实体到Added状态，调用SaveChanges时，生成Insert语句。
2. 几种增加情况
   - 新建实体，然后db.Set<T>().Add(u1);或者直接db.Add()方法，状态变化为：`Detached->Added->Unchanged`。
   - 新建实体，直接修改状态EntityState.Added,状态变化为：`Detached->Added->Unchanged`

3. 批量增加
调用AddRange方法，参数可以db.AddRange(u1,u2)；或者db.AddRange(list);批量增加最大的好处是生产一条sql语句，性能相对较高。

4. 指定自增健的插入
通过ExecuteSqlCommand("SET IDENTITY_INSERT [T_RoleInfor] ON");先关闭自增，然后插入数据，再通过ExecuteSqlCommand("SET IDENTITY_INSERT [T_RoleInfor] OFF");开启自增。

### 修改
数据库中有主键所对应的记录，修改实体到Modified状态，调用SaveChanges时，生成Update语句。

1. 更新已经跟踪的实体（即从数据库中查询出来的），当修改值和数据库中不同时，状态变化为：`Unchanged->Modified_>Unchanged`，直接执行SaveChanges方法执行修改。当修改值和数据库中原值相同时，状态不发生变化：`Unchanged->Unchanged->Unchanged`，即使调用SaveChanges也不执行任何Sql操作。
2. 新建一个实体，这个实体必须有主键（且数据库中存在），未包含的属性则当做空值来跟新，当修改值和原值不同时，状态变化：Detached->Modified->Unchanged。



<hr>
<br>

## EF Core性能优化
### 使用DbContext池
在Core MVC中，如果使用AddDbContextPool方法，那么在控制器请求DbContext实例时，我们会首先会检查池中有误可用的实例。请求处理完成后，实例的任何状态都将被重置，并且实例本身会返回池中。从概念上讲，此方法类似于ADO.NET连接池的运行原理，并具有节约DbContext实例初始化成本的优势。
```csharp
services.AddDbContextPool<BloggingContext>(options=>options.UseSqlServer(connectionString));
```

### 优先使用异步方法
EF Core中提供了很多形状如***Async的异步方法，推荐使用这些方法提高吞吐量和性能，减少不必要的延时等待。

### 多活动结果集连接服用
多活动结果集（MARS）是一项允许对单个连接执行多个批处理的功能。在以前的版本中，单个连接只能执行一个批处理。使用MARS执行多个批处理并不意味着同时执行操作。在连接字符串中添加`MultipleActiveResultSets=True`即可启用MARS特性。

### 批处理语句
EF Core向数据库中增加n条记录，会组合成一次请求访问数据库。
==注意：操作数据条数<=3时，不会批处理，还是分多次请求，只有大于3才会批处理==
> 可以手动设置批处理的条数，MaxBatchSize。
```csharp
optionsBuilder.UseSqlServer("Server=localhost;Database=Test;User ID=sa;Password=123;",b=>b.MaxBatchSize(100));
```

### 关闭状态跟踪
跟踪行为决定了EF Core是否将有关实体实例的快照信息保留在其跟踪器中。如果已跟踪某个实体，则该实体中检测到的任何更改都会在SaveChanges（）期间永久保存到数据库。当决定只查询数据，不更改数据时，非跟踪查询十分有用，非跟踪查询的执行会跟快，因为无需为查询实体设置快照跟踪信息。
如果不需要跟新从数据库中检索的实体，则应优先使用非跟踪查询。
- 单体查询关闭：`AsNoTracking()`
- 整个上下文关闭:`context.ChangeTracker.QueryTrackingBehavior=QueryTrackingBehavior.NoTracking;`

### 关闭同步状态
当从数据库进行查询数据时，上下文边捕获了每个实体属性的快照（数据库值，原始值，当前值），当调用SaveChanges时，在内部会自动调用DetectChanges方法，此方法将扫描上下文中的所有实体，并比较当前属性值和存储在快照中的原始属性值，如果被找到的属性值发生了变化，此时EF将会与数据库进行交互，进行数据更新。会导致自动调用DetectChanges方法：Find、Local、Remove、Add、Update、Attach、SaveChanges和Entry等。但是，自动同步状态会频繁调用，可手动关闭以上方法的自动同步，当数据都修改好后，一次性手动同步。
- 关闭：`context.ChangeTracker.AutoDetectChangesEnabled=false;`
- 开启：`context.ChangeTracker.DetectChanges()`

### 使用 EF.Functions.**进行查询
1. 使用EF.Functions.Like进行模糊查询要比StartsWith、Contains和EndsWith方法生成的SQL语句性能更优。
Contains语句，生成的sql语句：
`var data3=dbContext.T_UserInfor.Where(u=>u.userName.Contains("p")).ToList();`
用的是charindex。

EF.Functions.Like语句生成的SQL
```csharp
var data1=dbContext.T_UserInfor.Where(u=>EF.Functions.Like(u.userName,"%p%")).ToList();
//或者
var data2=(from p in dbContext.T_UserInfor
  where EF.Functions.Like(p.userName,"%p%")
  select p).ToList();
```

还有EF.Functions.DateDiffDay(DateDiffHour、DateDiffMonth)，求天、小时、月之间的数量。