# 实验指导 step9：函数
step9 开始，我们要支持多函数了。

1. 我们需要支持函数的声明和定义：
    <pre id='vimCodeElement'><code></code><div class="changed">
    <div class="changed"><span class="SpecRuleStart">program</span>
    <span class="SpecRuleIndicator">    :</span> <span class="SpecRule">function</span><span class="SpecOperator">*</span>

    <span class="SpecRuleStart">function</span>
    <span class="SpecRuleIndicator">    :</span> <span class="SpecRule">type</span> <span class="SpecToken">Identifier</span> <span class="SpecToken">'('</span> <span class="SpecRule">parameter_list</span> <span class="SpecToken">')'</span> <span class="SpecOperator">(</span><span class="SpecRule">compound_statement</span> <span class="SpecOperator">|</span> <span class="SpecToken">';'</span><span class="SpecOperator">)</span>
    </div>
    <div class="changed"><span class="SpecRuleStart">parameter_list</span>
    <span class="SpecRuleIndicator">    :</span> <span class="SpecOperator">(</span><span class="SpecRule">type</span> <span class="SpecToken">Identifier</span> <span class="SpecOperator">(</span><span class="SpecToken">','</span> <span class="SpecRule">type</span> <span class="SpecToken">Identifier</span><span class="SpecOperator">)*)?</span>
    </div>
    </pre>

2. 我们还需要支持函数调用：
    <pre id='vimCodeElement'><code></code><div class="changed">
    <div class="changed"><span class="SpecRuleStart">expression_list</span>
    <span class="SpecRuleIndicator">    :</span> <span class="SpecOperator">(</span><span class="SpecRule">expression</span> <span class="SpecOperator">(</span><span class="SpecToken">','</span> <span class="SpecRule">expression</span><span class="SpecOperator">)*)?</span>
    </div>
    <div class="changed"><span class="SpecRuleStart">unary</span>
    <span class="SpecRuleIndicator">    :</span> <span class="SpecRule">postfix</span>
    <span class="SpecRuleIndicator">    |</span> <span class="SpecOperator">(</span><span class="SpecToken">'-'</span><span class="SpecOperator">|</span><span class="SpecToken">'~'</span><span class="SpecOperator">|</span><span class="SpecToken">'!'</span><span class="SpecOperator">)</span> <span class="SpecRule">unary</span>

    <span class="SpecRuleStart">postfix</span>
    <span class="SpecRuleIndicator">    :</span> <span class="SpecRule">primary</span>
    <span class="SpecRuleIndicator">    |</span> <span class="SpecToken">Identifier</span> <span class="SpecToken">'('</span> <span class="SpecRule">expression_list</span> <span class="SpecToken">')'</span>
    </div>
    </pre>

语义检查部分，我们需要检查函数的重复定义、检查调用函数的实参（argment）和形参（parameter）的个数类型一致。
我们不支持 void 返回值，直接忽略 int 返回值即可。
