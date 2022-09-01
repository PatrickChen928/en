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
