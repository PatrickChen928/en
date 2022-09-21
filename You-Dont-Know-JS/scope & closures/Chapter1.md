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
- **Parsing**: taking a stream (array) of tokens and turning it into a tree of nested elements, which collectively represent the grammatical structure of the program. This tree is called an "Abstract Syntax Tree"(AST).
The tree for `var a = 2;` might start with a top-level node called VaribaleDeclaration,  with a child node called Identifier(whose value is a), and anthor child node called AssignmentExpression which itself has a child called NumbericLiteral (whose value is 2).
- **代码生成： 这个处理将抽象语法树转换为可执行的代码。这一部分将根据语言，它的目标平台等因素有很大的不同。所以，与其深陷细节，我们不如笼统地说，有一种方法将我们上面描述的 `var a = 2;` 的抽象语法树转换为机器指令，来实际上 创建 一个称为 a 的变量（包括分配内存等等），然后在 a 中存入一个值。注意： 引擎如何管理系统资源的细节远比我们要挖掘的东西深刻，所以我们将理所当然地认为引擎有能力按其需要创建和存储变量。**
- **Code-Generation**: the process of taking an AST and turning it into executable code.  This part varies greatly depending on the language, the platform it's targeting, etc. So, rather than get mired in details, we will just handwave and say that there is a way to take our above described AST for `var a = 2;` and turn it into a set of machine instructions to actually create a variable called a (including reserving memory, etc.), and then store a value into a. **Notice:** the details of how the engine manages system resources are deeper than what we will dig, so we will just take it for granted that the engine is able to create and store varibales as needed.

**和大多数其他语言的编译器一样，JavaScript 引擎要比这区区三步复杂太多了。例如，在解析和代码生成的处理中，一定会存在优化执行效率的步骤，包括压缩冗余元素，等等。**  
The JavaScript engine is vastly more complex than just those three steps, as are most other language compilers. For instance, in the process of parsing and code-generation, there are certainly steps to optimize the performance of the execution, including collapsing redundant elements, etc.

**所以，我在此描绘的只是大框架。但是我想你很快就会明白为什么我们涵盖的这些细节是重要的，虽然是在很高的层次上。**  
So, I'm painting only with broad strokes here. But I think you will see shortly why these details that we do cover,even at a high level, are relevant.

**其一，JavaScript 引擎没有（像其他语言的编译器那样）大把的时间去优化，因为 JavaScript 的编译和其他语言不同，不是提前发生在一个构建的步骤中。**  
For one thing, JavaScript engines don't get the luxury (like other language compilers) of having plenty of time to optimize, because JavaScript compilation dosen't happen in a build step ahead of time, as with other languages.

**对 JavaScript 来说，在许多情况下，编译发生在代码被执行前的仅仅几微秒之内（或更少！）。为了确保最快的性能，JS 引擎将使用所有的招数（比如 JIT，它可以懒编译甚至是热编译，等等），而这远超出了我们关于“作用域”的讨论。**  
For JavaScript, the compilation that occurs happens, in many cases, mere microseconds(or less!) before the code is executed. To ensure the fastest performance,  JS engines use all kinds of tricks (like JITs, which  lazy compile and even for hot re-compile, etc.), which are well beyond the "scope" of our discussion here.

**为了简单起见，我们可以说，任何 JavaScript 代码段在它执行之前（通常是 刚好 在它执行之前！）都必须被编译。所以，JS 编译器将把程序 `var a = 2; `拿过来，并首先编译它，然后准备运行它，通常是立即的。**  
Let's just say, for simplicity's sake, that any snippet of JavaScript has to be compiled (usually right before!) before it's executed. So, JS compiler will take the program `var a = 2;` and compile it first, and then be ready to execute it, usually right away.

## 理解作用域 (Understanding Scope)
**我们将采用的学习作用域的方法，是将这个处理过程想象为一场对话。但是，谁 在进行这场对话呢？**  
The way we will approach learning about scope is to think of the process in terms of a conversation. But, who is having the conversation?

### 演员 (The Cast)
**让我们见一见处理程序` var a = 2; `时进行互动的演员吧，这样我们就能理解稍后将要听到的它们的对话：**  
Let us meet the cast of characters that interact to process the program ` var a = 2; `, so we understand their conversations that we will listen in on shortly:

1. **引擎：负责从始至终的编译和执行我们的 JavaScript 程序。**  
Engine: responsible for start-to-finish compilation and exectuion of our JavaScript program.
2. **编译器：引擎 的朋友之一；处理所有的解析和代码生成的重活儿（见前一节）。**  
Compiler: one of Engine's friends; handles all the dirty work of parsing and code-generation (see previous section).
3. **作用域：引擎 的另一个朋友；收集并维护一张所有被声明的标识符（变量）的列表，并对当前执行中的代码如何访问这些变量强制实施一组严格的规则。**  
Scope: anohter friend of Engine; collects and maintains a look-up list of all the declared identifiers (variables), and enforces a strict set of rules as to how these are accessible to currently executing code.

**为了 全面理解 JavaScript 是如何工作的，你需要开始像 引擎（和它的朋友们）那样 思考，问它们问的问题，并像它们一样回答。**  
For you to fully understand how JavaScript works, you need to begin to think like Engine (and friends), ask the questions they ask, and answer those questions the same.

### 反复 (Back & Forth)
**当你看到程序 var a = 2; 时，你很可能认为它是一个语句。但这不是我们的新朋友 引擎 所看到的。事实上，引擎 看到两个不同的语句，一个是 编译器 将在编译期间处理的，一个是 引擎 将在执行期间处理的。**  
When you see the program `var a = 2;`, you most likely think of that as one statement. But that is not how our new friend Engine sees it. In fact, Engine sees two `distinct` statements, one which Compiler will handle during compilation, and one which Engine will handle during execution.

**那么，让我们来分析 引擎 和它的朋友们将如何处理程序 `var a = 2;`。**  
So, let's `break down` how Engine and friends will approach the program `var a = 2;`.

**编译器 将对这个程序做的第一件事情，是进行词法分析来将它分解为一系列 token，然后这些 token 被解析为一棵树。但是当 编译器 到了代码生成阶段时，它会以一种与我们可能想象的不同的方式来对待这段程序。**  
The first thing Compiler will do with this program is perform lexing to break it down into tokens, which it will then parse into a tree. But when Compiler gets to code-generation, it will treat this program `somewhat` differently than perhaps `assumed`.

**一个合理的假设是，编译器 将产生的代码可以用这种假想代码概括：“为一个变量分配内存，将它标记为 a，然后将值 2 贴在这个变量里”。不幸的是，这不是十分准确。**  
A reasonable assumption would be that Compiler will produce code that could be `summed up` by this `pseudo`-code: " `Allocate` memory for a variable, label it `a`, then stick the value 2 into that variable". Unfortunately, that's not quite `accurate`.

**编译器 将会这样处理：**  
Compiler will instead `proceed` as: 

1. **遇到 `var a`，编译器 让 作用域 去查看对于这个特定的作用域集合，变量 a 是否已经存在了。如果是，编译器 就忽略这个声明并继续前进。否则，编译器 就让 作用域 去为这个作用域集合声明一个称为 a 的新变量。**  
Encountering `var a`, Compiler asks Scope to see if a variable a already exists for that `particular` scope collection. If so, Compiler ignores this declaration and moves on. Ohterwise, Compiler asks Scope to declare a new variable called a for that scope collection.
