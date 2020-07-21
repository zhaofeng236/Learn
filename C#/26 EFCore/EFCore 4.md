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