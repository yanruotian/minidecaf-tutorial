# 实验指导 step7：作用域和块语句
step7 我们要增加块语句的支持。

虽然块语句语义不难，就是把多个语句组成一个块，每个块都是一个作用域。
随之而来一个问题是：不同变量可以重名了。
重名的情况包括作用域内部声明覆盖（shadowing）外部声明，以及不相交的作用域之间的重名变量。
因此，变量名不能唯一标识变量了，同一个变量名 `a` 出现在代码不同地方可能标识完全不同的变量。
我们需要进行 **名称解析（name resolution）**，确定 AST 中出现的每个变量名分别对应那个变量。

语法上改动不大

<pre id='vimCodeElement'><code></code>
<div class="changed"><span class="SpecRuleStart">function</span>
<span class="SpecRuleIndicator">    :</span> <span class="SpecRule">type</span> <span class="SpecToken">Identifier</span> <span class="SpecToken">'('</span> <span class="SpecToken">')'</span> <span class="SpecRule">compound_statement</span>
</div>
<div class="changed"><span class="SpecRuleStart">compound_statement</span>
<span class="SpecRuleIndicator">    :</span> <span class="SpecToken">'{'</span> <span class="SpecRule">block_item</span><span class="SpecOperator">*</span> <span class="SpecToken">'}'</span>
</div>
<span class="SpecRuleStart">statement</span>
<span class="SpecRuleIndicator">    :</span> <span class="SpecToken">'return'</span> <span class="SpecRule">expression</span> <span class="SpecToken">';'</span>
<span class="SpecRuleIndicator">    |</span> <span class="SpecRule">expression</span><span class="SpecOperator">?</span> <span class="SpecToken">';'</span>
<span class="SpecRuleIndicator">    |</span> <span class="SpecToken">'if'</span> <span class="SpecToken">'('</span> <span class="SpecRule">expression</span> <span class="SpecToken">')'</span> <span class="SpecRule">statement</span> <span class="SpecOperator">(</span><span class="SpecToken">'else'</span> <span class="SpecRule">statement</span><span class="SpecOperator">)?</span>
<div class="changed"><span class="SpecRuleIndicator">    |</span> <span class="SpecRule">compound_statement</span>
</div>
</pre>

语义检查我们也要修改了，只有在同一个作用域里，变量才不能重复声明。
当然，如果变量在使用前还是必须先被声明。
