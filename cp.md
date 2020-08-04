
### C#利用Guid实现真随机数
C#中的随机数可以利用Random类很简单的生成随机数。
```csharp
//生成随机数对象
Random random=new Random();
//大于a小于b的随机数int
int i=random.Next(a,b);
```
但是在不指定种子时，默认的种子时系统时间。这样的设置对于一般的随机数生成过程没有问题，但时对于一些快速、大量生成随机数的问题（尤其创建于利用循环批量生成随机数时），会出现由于运算速度太快导致在一个相同或者相近的系统时间内生成多个随机数的情况，此时生成的随机数都是相同的。

可以借助GUID（全局唯一标识符）来解决。
GUID（GUID，Globally unique Identifier）是一种由算法生成的二进制长度为128位的数字标识符。GUID主要用于在拥有多个节点、多台计算机的网络或系统中。在理想情况下，任何计算机和计算机集群都不会生成两个相同的GUID。所以随机生成两个相同的GUID的可能性非常小。GUID一词专指微软对UUID标准的实现。

GUID特点
- 可以完全由算法自动申城，不需要一个权威机构来管理
- GUID理论上能产生全球唯一的值，对于以后数据导入很方便。
```csharp
//生成字节数组
byte[] buffer=Guid.NewGuid().ToByteArray();
//利用BitConvert方法把字节数组转换为整数
int iRoot=BitConverter.ToInt32(buffer,0)
//以这个生成整数为种子数
Random random=new Random(iRoot)
```


```csharp
using System;
using System.Diagnostics;
using System.Threading;
using System.Security.Cryptography;

namespace Test
{
    class Program
    {
        static void Main(string[] args)
        {
            int[] array = new int[100000000];
            Stopwatch sw = new Stopwatch();
            sw.Start();

            //GetRandomBySleep(array);
           GetRandomByGuid(array);
           //GetRandomByRNGCryptoServiceProvider(array);








            sw.Stop();
            TimeSpan ts = sw.Elapsed;
            Console.WriteLine($"总共花费 {ts.TotalMilliseconds} 毫秒\n");

        }



        //使用RnGCryptoServiceProvider生成种子
        static int GetRandomSeed()
        {
            byte[] bytes = new byte[4];
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();

            rng.GetBytes(bytes);
            return BitConverter.ToInt32(bytes, 0);
        }

        //使用RNGCryptoServiceProvider产生真随机
        static void GetRandomByRNGCryptoServiceProvider(int[] array)
        {
            int len = array.Length;
            Random random = new Random(GetRandomSeed());
            for(int i = 0; i < len; i++)
            {
                array[i] = random.Next(1, 34);
            }
            //Print(array);
        }





        //使用GUID生成种子
        static int GetRandomSeedbyGuid()
        {
            return new Guid().GetHashCode();
        }

        //使用GUID产生的种子产生真随机数
        static void GetRandomByGuid(int[] array)
        {
            int len = array.Length;
            Random random = new Random(GetRandomSeedbyGuid());
            for(int i = 0; i < len; i++)
            {
                array[i] = random.Next(1,34);
            }
            
            //Print(array);
        }






        //使用Thread.Sleep()产生真随机数
        static void GetRandomBySleep(int[] array)
        {
            int len = array.Length;
            Random random = new Random();

            for(int i = 0; i < len; i++)
            {
                Thread.Sleep(1);
                array[i] = random.Next(1,34);
            }
            //Print(array);
        }



        static void Print(int[] array)
        {
            for(int i=0;i<array.Length; i++)
            {
                Console.Write(string.Format($"{array[i]}  "));
            }
            Console.WriteLine();
        }

    }
}

```