```
1、只要“||”前面为false,不管“||”后面是true还是false，都返回“||”后面的值。

2、只要“||”前面为true,不管“||”后面是true还是false，都返回“||”前面的值。
```

```
1、只要“&&”前面是false，无论“&&”后面是true还是false，结果都将返“&&”前面的值;

2、只要“&&”前面是true，无论“&&”后面是true还是false，结果都将返“&&”后面的值;
```

var Yahoo = Yahoo \|\| {};这种是非常广泛应用的。 获得初值的方式是不是很优雅？比if。。。。else…好很多，比？：也好不少。

callback && callback\(\)

