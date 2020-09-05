## 代码生成

块语句本身的汇编生成是很简单的，只需要依次生成块中的语句即可，块语句真正影响的是变量。

正如测试例子中的某些程序，有可能有两个不同的变量，在两个不同的作用域中，存储在栈的两个不同的位置，但名称相同。下面是这样一个例子：

```
int foo = 3;
{
  int foo = 4;
}
```

因此，每当程序引用变量`foo`时，我们生成的代码就需要在栈上访问正确的`foo`，如果`foo`已经“死亡”，则会引发一个错误。本次试验的代码生成步骤就是管理和访问变量集合对应的数据结构，使我们总是能查找到正确的`foo`。为此，我们需要调整我们在变量定义、赋值、引用时的行为。

### 变量偏移的确定

回顾我们在 lab5 中的做法，变量偏移完全等于变量出现的次序。这在当前仍然是正确的，但比较浪费栈空间，因为变量都存在生命周期，有些变量不可能同时存在，也就可以共用同一块地址空间（这个问题在后续编译课程中有深入的讨论）。我们在这里不做过多的优化，但在 minidecaf 中，一旦离开一个作用域，该作用域中声明的变量会全部失效，这就可以引出一个简单而有效的优化：利用栈的形式存储变量，记录作用域（只纪作用域深度即可），碰到变量声明则压栈变量，一旦离开一个作用域则弹出该作用域中所有的变量，此时，变量的偏移可以设置为它在栈中的位置编号（想想为什么？）。

### 找到正确变量

在 lab5 中，因为只有一个作用域，不论赋值还是引用，我们直接遍历所有变量就可以找到目标变量，但是引入作用域后，找到正确变量就没那么简单了。

所谓正确的变量，指的是位于同一或者各级父作用域中，最接近变量引用的声明，也就是“最近”的变量声明。

在单遍的代码生成中，可以维护上一小节所描述的变量栈，直接在栈中从栈顶向栈底搜索即可。这样，总是会找到最近的变量声明，且已经“死亡”的变量会被弹出，不会找错。

在多遍的代码生成中，我们可以维护一个树状的结构来维护变量的声明以及作用域信息。提示：可以通过存储声明位置（行号、列号，这要求生成的 AST 中存储了这些信息）判断同一个作用域中的某一个声明位于引用前还是引用后（step7/declare_late.c）。