# What is scope

- [ZH link](https://github.com/getify/You-Dont-Know-JS/blob/1ed-zh-CN/scope%20%26%20closures/ch1.md)
- [EN link](https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/scope%20%26%20closures/ch1.md)

**几乎所有语言的最基础模型之一就是在变量中存储值，并且在稍后取出或修改这些值的能力。事实上，在变量中存储值和取出值的能力，给程序赋予了 状态。**  
One of the most fundamental paradigms of nearly all programming languages is the ability to store values in variables, and later retrieve or modify those values. In fact, the ability to store values and pull values out of variables is what gives a program state.

**如果没有这样的概念，一个程序虽然可以执行一些任务，但是它们将会受到极大的限制而且不会非常有趣。**  
Without such a concept, a program could perform some tasks, but they would be extremely limited and not terribly interesting.

**但是在我们的程序中纳入变量，引出了我们现在将要解决的最有趣的问题：这些变量 存活 在哪里？换句话说，它们被存储在哪儿？而且，最重要的是，我们的程序如何在需要它们的时候找到它们？**  
But the inclusion of variables into our program begets the most interesting questions we will now address: where do those variables live? In other words, where are they stored? And, most importantly, how does our program find them when it needs them.

**回答这些问题需要一组明确定义的规则，它定义如何在某些位置存储变量，以及如何在稍后找到这些变量。我们称这组规则为：作用域。**  
These questions speak to the need for a well-defined set of rules for storing variables in some location, and for finding those variables at a later time. We will call that set of rules: scope.

**但是，这些 作用域 规则是在哪里、如何被设置的？**  
But, where and how do these Scope rules get set?

## Compiler Theory

**根据你与各种编程语言打交道的水平不同，这也许是不证自明的，或者这也许令人吃惊，尽管 JavaScript 一般被划分到“动态”或者“解释型”语言的范畴，但是其实它是一个编译型语言。它 不是 像许多传统意义上的编译型语言那样预先被编译好，编译的结果也不能在各种不同的分布式系统间移植。**  
It may be self-evident, or it may be surprising, depending on your level of interaction with various languages, but despite the fact that JavaScript falls under the general category of "dynamic" or "interpreted" languages,it is in fact a compiled language. It is not compiled well in advance, as are many traditionally-compiled languages, nor are the results of compilation portable among various distributed systems.

**但是无论如何，JavaScript 引擎在实施许多与传统的语言编译器相同的步骤，虽然是以一种我们不易察觉的更精巧的方式。**
But, nevertheless,  the JavaScript engine performs many of the same steps, albeit in more sophisticated ways than we may commonly be aware, of any traditional language-compiler.

**在传统的编译型语言处理中，一块儿源代码，你的程序，在它被执行 之前 通常将会经历三个步骤，大致被称为“编译”：**  
In a traditional compiled-language process, a chunk of source code, your program, will undergo typically three steps before it is executed, roughly called "compilation":

- **分词/词法分析(Tokenizing/Lexing)**: 这个过程会将由字符组成的字符串分解成(对编程语言来说)有意义的代码块，这些代 码块被称为词法单元(token)。例如，考虑程序var a = 2;。这段程序通常会被分解成 为下面这些词法单元:var、a、=、2 、;。空格是否会被当作词法单元，取决于空格在 这门语言中是否具有意义。 
- **Tokenizing/Lexing**: breaking up a string of characters into meaningful (to the language) chunks, called tokens. For instance, consider the program: `var a = 2;`. This program would likely be broken up into the following tokens: `var、a、=、2、;`. Wihtespace may or may not be persisted as a token, depending on whether it's meaningful or not.
- **解析**： **将一个 token 的流（数组）转换为一个嵌套元素的树，它综合地表示了程序的语法结构。这棵树称为“抽象语法树”（AST —— Abstract Syntax Tree）。**
**`var a = 2; `的树也许开始于称为 VariableDeclaration（变量声明）顶层节点，带有一个称为 Identifier（标识符）的子节点（它的值为 a），和另一个称为 AssignmentExpression（赋值表达式）的子节点，而这个子节点本身带有一个称为 NumericLiteral（数字字面量）的子节点（它的值为2）。**
- **Parsing**: taking a stream (array) of tokens and turning it into a tree of nested elements, whitch collectively represent the grammatical structure of the program. This tree is called an "Abstract Syntax Tree"(AST).
The tree for `var a = 2;` might start with a top-level node called VaribaleDeclaration,  with a child node called Identifier(whose value is a), and anthor child node called AssignmentExpression which itself has a child called NumbericLiteral (whose value is 2).