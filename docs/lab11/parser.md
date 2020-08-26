# 语法分析

这个阶段对原有的语法的一个重大的修改是之前所有语法中接受关键字`int`的地方现在都改为接受`ty`，包括函数的返回值，变量定义的类型。`ty`定义为：

```
ty
    : 'int'
    | ty '*'
    ;
```

也就是形如`int`，`int *`，`int * *`...这样的标记列表。不管你用的是什么语法分析的方式，应该都能够很容易地编写识别`ty`的语法分析函数，并且让原来那些接受`int`的地方改为接受`ty`。

另一个变化在于表达式中`factor`可以有更多构造的形式了，包括两种：

```
factor
    : ...
    | '&' factor
    | '*' factor
    ;
```

`factor`是整个表达式解析过程中最底层的元素，从优先级的角度来讲也就是`factor`相关的运算符的优先级最高，例如`&a + b`应该被解析成`(&a) + b`而不是`&(a + b)`。

语法分析可能放过并没有合法语义的标记列表，例如`&1`依据语法定义是完全合法的，但是没有实际意义。这不是语法分析阶段需要考虑的问题，它可以输出这种不合法的AST，待后续的检查来汇报错误。