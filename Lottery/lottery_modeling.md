# Lottery Modeling

## 用例
```sequence {theme="simple"}
Andrew->China: Says Hello
Note right of China: China thinks\nabout it
China-->Andrew: How are you?
Andrew->>China: I am good thanks!
```

```sequence{theme="simple"}
用户->彩票店:购买彩票
note left of 彩票店:fe
彩票店--> 彩票中心:下单
```


```dot
digraph G{
  账户->派生
  派生->
  派生->D
}
```


```dot
digraph g{
    rankdir=LR
    dot语言->{简介,语法,示例}
    dot语言[shape=box,fontcolor=red]
    简介[color=red]
    语法[color=green]
    示例[color=blue]

    简介->{开源免费，UML绘图，导出svg}
    语法->{"digraph","graph"}

    "digraph"->导向图[label=可以制作带方向的导图]
    "graph"->五向图[label=可以制作不带方向的导图]
}


```