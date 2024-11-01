
# index.html
# sui move book

[ ](print.html "Print this book")

# [sui move book](#sui-move-book)

[ ](basic/index.html "Next chapter")

[ ](basic/index.html "Next chapter")

# index.html
# sui move book

[ ](../print.html "Print this book")

[ ](../../index.html "Previous chapter") [ ](../basic/ch01.hello.html "Next chapter")

[ ](../../index.html "Previous chapter") [ ](../basic/ch01.hello.html "Next chapter")

# ch01.hello.html
# sui move book

[ ](../print.html "Print this book")

# [01.初识](#01初识)

本章目录

  * 1️⃣ 区块链基础
  * 2️⃣ Hello World
  * 3️⃣ 合约代码中的三种注释
  * 4️⃣ 合约结构介绍

## [1️⃣ 区块链基础](#1-区块链基础)

Move 是运行在区块链上开发智能合约的语言，我们不需要关心所在区块链底层逻辑，我们只有学会使用 Move 进行智能合约的编码。简单了解以下的区块链概念：

  * 事务
  * 交易
  * 地址
  * 区块
  * 存储/内存/栈

### [1.事务](#1事务)

事务意味着你想做的事情，要么一点没做，要么全部完成。具有原子性，不存在修改一半的情况。

比如从 A 地址向 B 地址转账 100 元，那么数据库里 A 减 100 元，B 加 100 元。如果因为某些原因导致 A 已经减了 100 元，但是 B 加 100 元中间出现了异常。因为事务的原子性，发生失败后 A/B 地址都不会发生任何修改。这种场景在合约中经常发生，会经常看到 _[out of gas](https://etherscan.io/tx/0xda8c0b80d8e240a83c8f6b067c4656babeb13e8e0ece4fd4292aa06252f1285c)_ 异常，这是因为 gas 被耗尽。此时合约中做的所有修改都会被回滚。

gas:合约的手续费；是作为用户为当前交易支付的手续费，每一笔交易都会收取 gas 费，目的是限制交易需要做的工作量，需要做的事情越多，所花费的 gas 也就越多；gas 会按照特定规则进行逐渐消耗，如果执行完成后还有剩余，gas 会在当前交易内原路返回到交易发起者的地址中。

### [2.交易](#2交易)

交易可以看作一个地址发送到另外一个地址的消息，可能包含一个二进制数据和Token。

  * 如果目标地址含有代码，则此代码会被执行，并以 payload 作为入参。
  * 如果目标地址是零地址，此交易将创建一个新合约。 
    * 这时候用来创建合约的 payload 会被**转为 Move 字节码** 并**执行** ，执行的输出作为合约代码永久存在区块链上。
    * 所以如果创建一个合约，并不需要向链上发送实际的合约代码，只需发送能够产生合约代码的代码就可以。

区块链中的交易遵守事务的特性。交易总是由发送人（创建交易的地址）进行签名。区块链底层会确保只有持有该地址密钥才能发起交易。正因为这个特性，所以才能为区块链上特定状态的修改增加保护机制。

比如在合约中指定某一个方法只有"管理员"账号可以用，我们只需要验证调用者是否为管理员地址就可以了，至于地址权限的保护事情并不需要关心，只要是该账号发起的交易，就认为是管理员在操作。安全方面我们需要考虑的是，如果某一个地址被盗了怎么样，通常这些是业务逻辑决定，比如多签钱包的业务。

### [3.地址](#3地址)

地址很多时候也被称为账户

  * 外部地址：由**公钥-私钥对** 控制 
    * 常用的助记词，keystore 文件等只是方便用户储存，底层还是会转成私钥。
    * 一般是钱包应用创建的地址。公钥就是`0xABC`的这种Token收款地址，私钥可能是助记词生成，可能是 keystore 文件生成，也可能是用户直接保存的。
  * 合约地址：由地址一起存储的代码控制。

无论外部地址，还是合约地址，对于 EVM 来说，都是一样的。每个地址都有一个键值对形式的持久化存储。其中 key 和 value 都是 256 位，我们称为存储。此外每个地址都会有一个以太币的余额，合约地址也是如此；余额会因为发送包含以太币的交易而改变。

## [2️⃣ Hello World](#2-hello-world)

Move 合约类似于Rust编程语言。后面会逐步展开介绍，国际惯例，使用当前语言的 Hello World 作为第一个例子。

### [1.例子代码](#1例子代码)

```

    
    
    module ch01::hello {
      use sui::tx_context::{TxContext, sender};
      use sui::transfer;
      use std::string;
      use std::string::String;
      public struct HelloMove has key,store{
        hello:String
      }
      fun init(ctx:&mut TxContext){
        let hello = string::utf8(b"Hello Move");
        transfer::public_transfer(HelloMove{hello},sender(ctx));
      }
    }
    
    
```

### [2.Hello World 例子分析](#2hello-world-例子分析)

## [3️⃣ 合约代码中的两种注释](#3-合约代码中的两种注释)

Move 支持 2 种注释方式；

  * 单行注释
  * 块注释

_**重要提示 截止目前move 目前不支持中文注释，只支持英文注释**_

### [1.单行注释](#1单行注释)

格式: `// Single-line comments`

```

    
    
    // this is a hello world
    
```

```

    
    
      // Single-line comments
      let hello = string::utf8(b"Hello Move");
    
```

如上，`//` 后面的内容都会被编译器忽略，为了可读性，一般会在`//`后面加一个空格。

### [2.块注释](#2块注释)

格式如下，在 `/*` 与 `*/` 之间的内容，都被编译器忽略

```

    
    
      /*
       Multiline comments
      */
    
```

为了可读性，一般块注释的行首都加 `*` 和空格，如下

```

    
    
      /**
       * This is a block comment
       * This is a block comment
       */
    
```

#### [⓵ 变量](#⓵-变量)

合约内的 `hello` 叫做变量。关于变量的更多信息，会在后续文章说明

#### [⓶ 函数](#⓶-函数)

函数是代码的可执行单元，是一组逻辑的集合。关于变量的更多信息，会在后续 [函数]那一章详细介绍

### [4.use 导入声明](#4use-导入声明)

功能：从其他文件内倒入需要的函数和结构体。

#### [⓵ 导入方式](#⓵-导入方式)

[ ](../basic/index.html "Previous chapter") [ ](../basic/ch02.type-of-data.html "Next chapter")

[ ](../basic/index.html "Previous chapter") [ ](../basic/ch02.type-of-data.html "Next chapter")

# ch02.type-of-data.html
# sui move book

[ ](../print.html "Print this book")

## [基本类型](#基本类型)

Move 的基本数据类型包括: 整型 (u8, u32,u64, u128,u258)、布尔型 boolean 和地址 address。

Move 不支持字符串和浮点数。

```

    
    
    module ch02::int {
      fun main() {
        // define empty variable, set value later
        let a: u8;
        a = 10;
        let a = 1u32;
        // define variable, set type
        let a: u64 = 10;
        // finally simple assignment
        let a = 10;
        // simple assignment with defined value type
        let a = 10u64;
        // in function calls or expressions you can use ints as constant values
        if (a < 10) {};
        // or like this, with type
        if (a < 10u64) {}; // usually you don't need to specify type
        let b = 1u256;
        // or like this, with type
        if (b < 10u256) {}; // usually you don't need to specify type
      }
    }
    
    
```

` let a = 10` 默认不手动标记类型的整型是 `u64` 类型，也就是等同于 `let a:u64 = 10` 或者 `let a = 10u64`

# [布尔型](#布尔型)

布尔类型就像编程语言那样，包含false和true两个值。

```

    
    
    module book::boolean {
      fun main() {
        // these are all the ways to do it
        let b : bool; b = true;
        let b : bool = true;
        let b = true;
        let b = false; // here's an example with false
      }
    }
    
```

# [地址](#地址)

地址是区块链中交易发送者的标识符，转账和导入模块这些基本操作都离不开地址。

```

    
    
    module book::addr {
      fun main() {
        let addr: address; // type identifier
        addr = @ch02;
      }
    }
    
```

[ ](../basic/ch01.hello.html "Previous chapter") [ ](../basic/ch03.module.html "Next chapter")

[ ](../basic/ch01.hello.html "Previous chapter") [ ](../basic/ch03.module.html "Next chapter")

# ch03.module.html
# sui move book

[ ](../print.html "Print this book")

# [模块](#模块)

模块是发布在特定地址下的打包在一起的一组函数和结构体。

> 模块在发布者的地址下发布。标准库在 0x1 地址下发布。

> 发布模块时，不会执行任何函数。要使用模块就得使用脚本。

模块以`module`关键字开头，后面跟随地址::模块名称和大括号，大括号中放置模块内容。

> 模块名推荐使用小写

```

    
    
    module book::math {
      public fun sum(a: u64, b: u64): u64 {
        a + b
      }
    }
    
```

> 模块是发布代码供他人访问的唯一方法。新的类型和 Resource 也只能在模块中定义。默认情况下，模块将在发布者的地址下进行编译和发布

_如示例所示：最佳实践是保持模块行不缩进_

## [导入](#导入)

Move 在默认上下文中只能使用基本类型，也就是整型、布尔型和地址，可以执行的有意义或有用的操作也就是操作这些基本类型，或者基于基本类型定义新的类型。

除此之外还可以导入已发布的模块（或标准库）。

### [直接导入](#直接导入)

可以直接在代码中按其地址使用模块：

```

    
    
    module book::m {
      fun main(a: u8) {
        std::debug::print(&a);
      }
    }
    
```

在此示例中，我们从地址0x1（标准库）导入了 debug 模块，并使用了它的 print 方法。

### [关键字 use](#关键字-use)

要使代码更简洁（注意，0x1 是特殊的地址，实际地址是很长的），可以使用关键字use：

```

    
    
    use <address>::<ModuleName>;
    
```

这里 `<address>` 是模块发布object的地址，`<ModuleName>` 是模块的名字。非常简单，例如，我们可以像下面这样从 `0x1` 地址导入 `vector` 模块。

```

    
    
    use 0x1::vector;
    
```

### [访问模块的内容](#访问模块的内容)

要访问导入的模块的方法（或类型），需要使用::符号。非常简单，模块中定义的所有公开成员都可以通过双冒号进行访问。

```

    
    
    module book::m_use {
      use std::debug::print;
      fun main(a: u8) {
        print(&a);
      }
    }
    
```

### [在模块中导入](#在模块中导入)

在模块中导入模块必须在 `module {}` 块内进行:

```

    
    
    module book::math {
      use std::vector;
      // you are free to import any number of modules
      public fun empty_vec(): vector<u64> {
        let v = vector::empty<u64>();
        v
      }
    }
    
```

### [成员导入](#成员导入)

导入语句还可以进一步被扩展，可以直接导入模块的成员:

```

    
    
    module book::m_use2 {
      // single member import
      use sui::tx_context::TxContext;
      use sui::tx_context::sender;
      // multi member import (mind braces)
      use std::vector::{
        empty,
        push_back
      };
      fun main(ctx: &mut TxContext) {
        // use functions without module access
        let vec = empty<u8>();
        push_back(&mut vec, 10);
        let _ = sender(ctx);
      }
    }
    
```

### [使用 `Self` 来同时导入模块和模块成员](#使用-self-来同时导入模块和模块成员)

导入语句还可以进一步扩展，通过使用 `Self` 来同时导入模块和模块成员，这里 `Self` 代表模块自己。

```

    
    
    module book::m_self {
      use 0x1::vector::{
      Self, // Self == Imported module
      empty
      };
      fun main() {
        // `empty` imported as `empty`
        let vec = empty<u8>();
        // Self means vector
        vector::push_back(&mut vec, 10);
      }
    }
    
```

### [使用 use as](#使用-use-as)

当两个或多个模块具有相同的名称时，可以使用关键字as更改导入的模块的名称，这样可以在解决命名冲突的同时缩短代码长度。

语法：

```

    
    
    use <address>::<ModuleName> as <Alias>;
    
```

在模块中：

```

    
    
    module ch04::m_as {
      use 0x1::vector as v;
      // v now means vector
      fun main() {
        v::empty<u64>();
      }
    }
    
```

脚本中的例子：

```

    
    
    module ch04::m_as1 {
      use 0x1::vector::{
      Self as v,
      empty as empty_vec
      };
      fun main() {
        // `empty` imported as `empty_vec`
        let vec = empty_vec<u8>();
        // Self as V = vector
        v::push_back(&mut vec, 10);
      }
    }
    
```

[ ](../basic/ch02.type-of-data.html "Previous chapter") [ ](../basic/ch04.function.html "Next chapter")

[ ](../basic/ch02.type-of-data.html "Previous chapter") [ ](../basic/ch04.function.html "Next chapter")

# ch04.function.html
# sui move book

[ ](../print.html "Print this book")

# [函数](#函数)

Move 中代码的执行是通过调用函数实现的。函数以 fun 关键字开头，后跟函数名称、扩在括号中的参数，以及扩在花括号中的函数体。

```

    
    
    module book::f01 {
      fun function_name(arg1: u64, arg2: bool): u64 {
        // function body
        10
      }
    }
    
```

我们在前面的章节中已经看到过函数，现在我们来学习如何使用函数。

> 注意：Move 函数使用snake_case命名规则，也就是小写字母以及下划线作为单词分隔符。

## [模块中的函数](#模块中的函数)

脚本中能使用的函数功能是相对有限的，函数的全部潜能只有在模块中才能展现。让我们再看一遍什么是模块：模块是一组函数和结构体（我们将在下一章中介绍结构体），它可以封装一项或多项功能。

这部分内容中，我们将创建一个简单的 Math 模块，它将为用户提供一组基本的数学函数和一些辅助方法。当然这里面大部分操作无需使用模块即可完成，但我们的目标是通过这个例子来理解函数。

```

    
    
    module book::math {
      fun zero(): u8 {
        0
      }
    }
    
```

第一步：我们定义一个 math 模块，它有一个函数：zero()，该函数返回 u8 类型的值 0。还记得我们之前介绍过的表达式吗？0 之后没有分号，因为它是函数的返回值。是的，就像块表达式一样，函数与块非常相似。

### [函数参数](#函数参数)

关于参数其实大家都已经很清楚了，但是我们还是稍微啰嗦一下，函数可以根据需要接受任意多个参数（传递给函数的值）。就像 Move 中的其他任何变量一样，每个参数都有两个属性：参数名，也就是参数在函数体内的名称，以及参数类型。

像作用域中定义的任何其他变量一样，函数参数仅存在于函数体内。当函数块结束时，参数也会消亡。

```

    
    
    module book::math {
      public fun sum(a: u64, b: u64): u64 {
        a + b
      }
      fun zero(): u8 {
        0
      }
    }
    
```

大家发现有什么不一样了么？math 模块新增了 sum(a,b) 函数，该函数将两个 u64 值相加并作为 u64 结果返回。

关于参数的一些语法规则：

  1. 参数必须具有类型，并且必须用逗号分隔
  2. 函数返回值放在括号后，并且必须在冒号后面

下面我们如何在其他模块中使用此函数呢？通过"导入"！

```

    
    
    module book::math_use {
      use book::math::sum;
      fun main(){
        let a = sum(1,2);
      }
    }
    
```

### [关键字 `return`](#关键字-return)

关键字 return 允许函数结束执行并返回结果。它可以与 if 条件一起使用，这样可以根据条件返回不同结果。

```

    
    
    module book::m {
      public fun conditional_return(a: u8): bool {
        if (a == 10) {
          return true // semi is not put!
        };
        if (a < 10) {
          true
        } else {
          false
        }
      }
    }
    
```

### [多个返回值](#多个返回值)

在前面的示例中，我们尝试了没有返回值或返回单个值的函数。但是，要想返回任何类型的多个值应该怎么办呢？好的，让我们继续往下看！

要指定多个返回值，需要使用括号：

```

    
    
    module book::math {
      // ...
      public fun max(a: u8, b: u8): (u8, bool) {
        if (a > b) {
          (a, false)
        } else if (a < b) {
          (b, false)
        } else {
          (a, true)
        }
      }
    }
    
```

该函数有两个参数：a 和 b，并返回两个值：第一个是两个输入参数中的较大的值，第二个是布尔类型，表示输入的参数是否相等。请仔细看一下语法，我们没有指定单个返回值，而是添加了括号并在其中列出了返回值类型。

现在让我们看看如何在另一个模块中使用该函数的返回值。

```

    
    
    module book::math_use {
      use book::math::sum;
      use book::math::max;
      fun use_max(){
        let (a,b)= max(1u8,2u8);
      }
    }
    
```

上面例子中，我们解构了一个二元组，用函数 max 的返回值创建了两个新变量。返回值的顺序保持不变，变量 max 用来存储 u8 类型的最大值，而 is_equal 用来存储 bool 类型。

返回值数量并没有限制，你可以根据需要决定元组的元素个数。下一章，我们还会介绍返回复杂数据的另一种方法，那就是结构体。

### [函数可见性](#函数可见性)

定义模块时，你可能希望其他开发人员可以访问某些函数，而某些函数则保持隐藏状态。这正是函数可见性修饰符发挥作用的时候。

默认情况下，模块中定义的每个函数都是私有的，无法在其它模块或脚本中访问。可能你已经注意到了，我们在 Math 模块中定义的某些函数前有关键字 public：

```

    
    
    module book::math {
      public fun sum(a: u64, b: u64): u64 {
        a + b
      }
      fun zero(): u8 {
        0
      }
    }
    
```

例子中math模块被其它模块导入后，sum() 函数可以从外部访问，但是 zero() 不能被访问，因为默认情况下它是私有的。

> 关键字 public 将更改函数的默认可见性并使其公开，即可以从外部访问。

基本上，如果不将 sum() 函数设为 public，从外部访问是不可能的：

```

    
    
    module book::math_use {
      use book::math::sum;
      fun use_sum(){
        let a = sum(1,2);
      }
    }
    
```

### [访问私有函数](#访问私有函数)

如果根本无法访问，那么私有函数就没有任何意义了。调用 public 函数的同时，可以用私有函数来执行一些内部工作。

> 私有函数只能在定义它们的模块中访问。

那么如何访问同一模块中的函数？通过像导入一样简单地调用此函数！

```

    
    
    module book::math {
      public fun is_zero(a: u8): bool {
        a == zero()
      }
      fun zero(): u8 {
        0
      }
    }
    
```

一个模块中定义的任何函数都可以被同一模块中的任何函数访问，无论它们的可见性修饰符是什么。这样，私有函数仍然可以在内部调用，而且不会暴露某些私有操作到模块外。

### [package 方法](#package-方法)

package 只能在同一个包内调用

```

    
    
    module book::friends {
      public(package) fun a_less_10(a: u8): bool {
        if(a < 10u) return true;
        false
      }
    }
    
```

### [本地方法](#本地方法)

有一种特殊的函数叫做"本地方法"。本地方法实现的功能超出了 Move 的能力，它可以提供了额外的功能。本地方法由 VM 本身定义，并且在不同的VM实现中可能会有所不同。这意味着它们没有用 Move 语法实现，没有函数体，直接以分号结尾。关键字 native 用于标记本地函数，它和函数可见性修饰符不冲突，native 和 public 可以同时使用。

这是 Diem 标准库中的示例。

```

    
    
    module book::m {
      native public fun borrow_address(s: &signer): &address;
      // ... some other functions ...
    }
    
```

[ ](../basic/ch03.module.html "Previous chapter") [ ](../basic/ch05.operator.html "Next chapter")

[ ](../basic/ch03.module.html "Previous chapter") [ ](../basic/ch05.operator.html "Next chapter")

# ch05.operator.html
# sui move book

[ ](../print.html "Print this book")

### [运算符 as](#运算符-as)

> as 在move 中有两个用法

  * 给包取别名

```

    
    
    module book::m_as1 {
      use 0x1::vector::{
        Self as v,
        empty as empty_vec
      };
      fun main() {
        // `empty` imported as `empty_vec`
        let vec = empty_vec<u8>();
        // Self as V = vector
        v::push_back(&mut vec, 10);
      }
    }
    
```

  * 整型类型转换 语法 (整型A as 整型 B) 当需要比较值的大小或者当函数需要输入不同大小的整型参数时，你可以使用as运算符将一种整型转换成另外一种整型 注意就是括号是一定不能省的

```

    
    
    module book::op_as {
      fun main(){
        let _a:u64 = (10u8 as u64);
        let _b:u8 = (_a as u8);
      }
    }
    
```

### [+ - * / 加 减 乘 除](#------加-减-乘-除)

  * 加法运算符 +
  * 减法运算符 -
  * 乘法运算符 *
  * 除法运算符 /

```

    
    
    module book::op_arith {
      fun main(){
        let _add_op = 1 + 1;
        let _mut_op = 1*1;
        let _minu_op = 100 -1;
        let _div_op = 100/1;
      }
    }
    
```

> 注意事项 因为Move没有负数做减法一定要检查是否产生负数 做加法乘法注意 溢出报错 Move没有浮点数 ，除法小心精度丢失问题 得益于Move的安全设计，溢出和负数不会让合约产生安全问题，因为程序会终止运行，但是程序终止会给用户带来不好的体验，代码不是很好调试，建议还是做好溢出边界判断

### [其他运算付](#其他运算付)

[ ](../basic/ch04.function.html "Previous chapter") [ ](../basic/ch06.const.html "Next chapter")

[ ](../basic/ch04.function.html "Previous chapter") [ ](../basic/ch06.const.html "Next chapter")

# ch06.const.html
# sui move book

[ ](../print.html "Print this book")

# [常量](#常量)

Move 支持`模块`级常量。常量一旦定义，就无法更改，所以可以使用常量为特定模块或脚本定义一些不变量，例如角色、标识符等。

常量可以定义为基本类型（比如整数，布尔值和地址），也可以定义为数组。我们可以通过名称访问常量，但是要注意，常量对于定义它们的模块来说是本地可见的。

> 我们无法从模块外部访问模块内部定义的常量

```

    
    
    module book::consts {
      use std::debug;
      const RECEIVER: address = 0x999;
      const ErrO1: u64 = 1000k
      fun main(account: &signer) {
        debug::print<address>(&RECEIVER);
        let _ = RECEIVER;
        let _ = ErrO1;
      }
    }
    
```

一些用法:

```

    
    
    module book::cosnt_m {
      const MAX : u64 = 100;
      // however you can pass constant outside using a function
      public fun get_max(): u64 {
        MAX
      }
      // or using
      public fun is_max(num: u64): bool {
        num == MAX
      }
    }
    
```

使用常量时应该注意:

  1. 一旦定义，常量是不可更改的。
  2. 常量在模块是本地可见的，不能在外部使用。
  3. 可以将常量定义为一个表达式（带有花括号），但是此表达式的语法非常有限。

[ ](../basic/ch05.operator.html "Previous chapter") [ ](../basic/ch07.expression-and-scope.html "Next chapter")

[ ](../basic/ch05.operator.html "Previous chapter") [ ](../basic/ch07.expression-and-scope.html "Next chapter")

# ch07.expression-and-scope.html
# sui move book

[ ](../print.html "Print this book")

# [表达式和作用域](#表达式和作用域)

在编程语言中，表达式是具有返回值的代码单元。有返回值的函数调用是一个表达式，它有返回值；整型常数也是一个表达式，它返回整数；其它表达式依此类推。

> 表达式必须用分号";"隔开

### [空表达式](#空表达式)

类似于 Rust，Move 中的空表达式用空括号表示：

```

    
    
    module book::exp_scope {
      fun empty() {
        () // this is an empty expression
      }
    }
    
```

### [文字（Literal）表达式](#文字literal表达式)

下面的代码，每行包含一个以分号结尾的表达式。最后一行包含三个表达式，由分号隔开。

```

    
    
    module book::exp_scope {
      fun main() {
        10;
        10 + 5;
        true;
        true != false;
        0x1;
        1; 2; 3;
      }
    }
    
```

现在我们已经知道了最简单的表达式。但是为什么我们需要它们？以及如何使用它们？这就需要介绍 let 关键字了。

### [变量和`let`关键字](#变量和let关键字)

关键字 let 用来将表达式的值存储在变量中，以便于将其传递到其它地方。我们曾经在基本类型章节中使用过 let，它用来创建一个新变量，该变量要么为空（未定义），要么为某表达式的值。

```

    
    
    module book::exp_scope {
      fun main() {
        let a;
        let b = true;
        let c = 10;
        let d = 0x1;
        a = c;
      }
    }
    
```

> 关键字 let 会在 _当前作用域_ 内创建新变量，并可以选择 _初始化_ 此变量。该表达式的语法是：let  : ;或let  = 。

创建和初始化变量后，就可以使用变量名来 _修改_ 或 _访问_ 它所代表的值了。在上面的示例中，变量 a 在函数末尾被初始化，并被 _分配_ 了一个值 c。

> 等号"="是赋值运算符。它将右侧表达式赋值给左侧变量。示例：a = 10 表示将整数10赋值给变量a。

### [整型运算符](#整型运算符)

Move具有多种用于修改整数值的运算符：

运算符| 操作| 类型  
---|---|---  
+| sum| uint| LHS加上RHS  
-| sub| uint| 从LHS减去RHS  
/| div| uint| 用LHS除以RHS  
*| mul| uint| LHS乘以RHS  
%| mod| uint| LHS除以RHS的余数  
<<| lshift| uint| LHS左移RHS位  
>>| rshift| uint| LHS右移RHS位  
&| and| uint| 按位与  
^| xor| uint| 按位异或  
\| or| uint| 按位或  
  
_LHS - 左侧表达式, RHS - 右侧表达式; uint: u8, u64, u128._

### [下划线 "_" 表示未被使用](#下划线-_-表示未被使用)

Move 中每个变量都必须被使用，否则代码编译不会通过, 因此我们不能初始化一个变量却不去使用它。但是你可以用下划线来告诉编译器，这个变量是故意不被使用的。

例如，下面的脚本在编译时会报错：

```

    
    
    module book::exp_scope {
      fun main() {
        let a = 1;
      }
    }
    
```

报错:

```

    
    
      ┌── /scripts/script.move:3:13 ───
      │
     33 │     let a = 1;
      │       ^ Unused assignment or binding for local 'a'. Consider removing or replacing it with '_'
      │
    
```

编译器给出明确提示：用下划线来代替变量名。

```

    
    
    module book::exp_scope {
      fun main() {
        let _ = 1;
      }
    }
    
```

### [屏蔽](#屏蔽)

Move 允许两次定义同一个的变量，第一个变量将会被屏蔽。但有一个要求：我们仍然需要"使用"被屏蔽的变量。

```

    
    
    module book::exp_scope {
      fun main() {
        let a = 1;
        let a = 2;
        let _ = a;
      }
    }
    
```

在上面的示例中，我们仅使用了第二个a。第一个a实际上未使用，因为a在下一行被重新定义了。所以，我们可以通过下面的修改使得这段代码正常运行。

```

    
    
    module book::exp_scope {
      fun main() {
        let a = 1;
        let a = a + 2;
        let _ = a;
      }
    }
    
```

## [块表达式](#块表达式)

块表达式用花括号"_{}_ "表示。块可以包含其它表达式（和其它代码块）。函数体在某种意义上也是一个代码块。

```

    
    
    module book::exp_scope {
      fun block() {
        { };
        { { }; };
        true;
        {
          true;
          { 10; };
        };
        { { { 10; }; }; };
      }
    }
    
```

### [作用域](#作用域)

如 [Wikipedia](https://en.wikipedia.org/wiki/Scope_\(computer_science\)) 中所述，作用域是绑定生效的代码区域。换句话说，变量存在于作用域中。Move 作用域是由花括号扩起来的代码块，它本质上是一个块。

> 定义一个代码块，实际上是定义一个作用域。

```

    
    
    module book::exp_scope {
      fun scope_sample() {
        // this is a function scope
        {
          // this is a block scope inside function scope
          {
            // and this is a scope inside scope
            // inside functions scope... etc
          };
        };
        {
          // this is another block inside function scope
        };
      }
    }
    
```

从该示例可以看出，作用域是由代码块（或函数）定义的。它们可以嵌套，并且可以定义多个作用域，数量没有限制。

### [变量的生命周期和可见性](#变量的生命周期和可见性)

我们前面已经介绍过关键字 let 的作用，它可以用来定义变量。有一点需要强调的是，该变量仅存在于变量所处的作用域内。也就是说，它在作用域之外不可访问，并在作用域结束后立即消亡。

```

    
    
    module book::exp_scope {
      fun let_scope_sample() {
        let a = 1; // we've defined variable A inside function scope
        {
          let b = 2; // variable B is inside block scope
          {
            // variables A and B are accessible inside
            // nested scopes
            let c = a + b;
          }; // in here C dies
          // we can't write this line
          // let d = c + b;
          // as variable C died with its scope
          // but we can define another C
          let c = b - 1;
        }; // variable C dies, so does C
        // this is impossible
        // let d = b + c;
        // we can define any variables we want
        // no name reservation happened
        let b = a + 1;
        let c = b + 1;
      } // function scope ended - a, b and c are dropped and no longer accessible
    }
    
```

> 变量仅存在于其作用域（或代码块）内，当作用域结束时变量随之消亡。

### [块返回值](#块返回值)

上面我们了解到代码块是一个表达式，但是我们没有介绍为什么它是一个表达式以及代码块的返回值是什么。

> 代码块可以返回一个值，如果它后面没有分号，则返回值为代码块内最后一个表达式的值。

听起来不好理解，我们来看下面的例子：

```

    
    
    module book::exp_scope {
      fun block_ret_sample() {
        // since block is an expression, we can
        // assign it's value to variable with let
        let a = {
          let c = 10;
          c * 1000 // no semicolon!
        }; // scope ended, variable a got value 10000
        let b = {
          a * 1000 // no semi!
        };
        // variable b got value 10000000
        {
          10; // see semi!
        }; // this block does not return a value
        let _ = a + b; // both a and b get their values from blocks
      }
    }
    
```

> 代码块中的最后一个表达式（不带分号）是该块的返回值。

### [小结](#小结)

我们来总结一下本章要点：

  1. 每个表达式都必须以分号结尾，除非它是 block 的返回值。
  2. 关键字 let 使用值或表达式创建新变量，该变量的生命周期与其作用域相同。
  3. 代码块是一个可能具有也可能没有返回值的表达式。

下一章我们将介绍控制流和分支语句。

### [进一步阅读](#进一步阅读)

[ ](../basic/ch06.const.html "Previous chapter") [ ](../basic/ch08.control-flow.html "Next chapter")

[ ](../basic/ch06.const.html "Previous chapter") [ ](../basic/ch08.control-flow.html "Next chapter")

# ch08.control-flow.html
# sui move book

[ ](../print.html "Print this book")

# [控制流](#控制流)

通过控制流表达式，我们可以选择运行某个代码块，或者跳过某段代码而运行另一个代码块。

Move 支持 if 表达式和循环表达式。

## [`if` 表达式](#if-表达式)

if 表达式允许我们在条件为真时运行代码块，在条件为假时运行另一个代码块。

```

    
    
    module book::c_if {
      use std::debug;
      fun main() {
        let a = true;
        if (a) {
          debug::print<u8>(&0);
        } else {
          debug::print<u8>(&99);
        };
      }
    }
    
```

这个例子中，当`a == true`时打印`0`，当a是`false`时打印`99`，语法非常简单：

```

    
    
    if (<布尔表达式>) <表达式> else <表达式>;
    
```

if是一个表达式，我们可以在 let 声明中使用它。但是像所有其他表达式一样，它必须以分号结尾。

```

    
    
    module book::c_if {
      use std::debug;
      fun main() {
        // try switching to false
        let a = true;
        let b = if (a) { // 1st branch
          10
        } else { // 2nd branch
          20
        };
        debug::print<u8>(&b);
      }
    }
    
```

现在，b 将根据 a 表达式为变量分配不同的值。但是 if 的两个分支必须返回相同的类型！否则，变量 b 将会具有不同类型，这在静态类型的语言中是不允许的。在编译器术语中，这称为分支兼容性 —— 两个分支必须返回兼容（相同）类型。

if 不一定非要和 else 一起使用，也可以单独使用。

```

    
    
    module book::c_if {
      use std::debug;
      fun main() {
        let a = true;
        // only one optional branch
        // if a = false, debug won't be called
        if (a) {
          debug::print<u8>(&10);
        };
      }
    }
    
```

但是请记住，不能在 let 赋值语句中使用不带分支的表达式！因为如果 if 不满足条件，就会导致变量未被定义，这同样是不允许的。

## [循环表达式](#循环表达式)

在 Move 中定义循环有两种方法：

  1. `while` 条件循环
  2. `loop` 无限循环

### [`while` 条件循环](#while-条件循环)

while 是定义循环的一种方法：在条件为真时执行表达式。只要条件为 `true`，代码将一遍又一遍的执行。条件通常使用外部变量或计数器实现。

```

    
    
    module book::c_if {
      fun main() {
        let i = 0; // define counter
        // iterate while i < 5
        // on every iteration increase i
        // when i is 5, condition fails and loop exits
        while (i < 5) {
          i = i + 1;
        };
      }
    }
    
```

需要指出的是，`while` 表达式就像 `if` 表达式一样，也需要使用分号结束。while 循环的通用语法是：

```

    
    
    while (<布尔表达式>) <表达式>;
    
```

与 `if` 表达式不同的是，`while` 表达式没有返回值，因而也就不能像 `if` 那样把自己赋值给某变量。

### [无法访问的代码](#无法访问的代码)

安全是 Move 最显著的特性。出于安全考虑，Move 规定所有变量必须被使用。并且出于同样的原因，Move 禁止使用无法访问的代码。由于数字资产是可编程的，因此可以在代码中使用它们（我们将在 Resource 一章中对其进行介绍）。而将资产放置在无法访问的代码中可能会带来问题，并造成损失。

这就是为什么无法访问的代码如此重要的原因。

### [无限循环](#无限循环)

Move 提供了一种定义无限循环的方法，它没有条件判断，会一直执行。一旦执行该代码将消耗所有给定资源（交易费），大多数情况下，编译器也无法判断循环是否是无限的，也就无法阻止无限循环代码的发布。因此，使用无限循环时一定要注意安全，通常情况下建议使用 `while` 条件循环。

无限循环用关键字 `loop` 定义。

```

    
    
    module book::c_if {
      fun main() {
        let i = 0;
        loop {
          i = i + 1;
        };
        // UNREACHABLE CODE
        let _ = i;
      }
    }
    
```

下面的代码也是可以编译通过的:

```

    
    
    module book::c_if {
      fun main() {
        let i = 0;
        loop {
          if (i == 1) { // i never changed
            break // this statement breaks loop
          }
        };
        // actually unreachable
        std::debug::print<u8>(&i);
      }
    }
    
```

对于编译器而言，要了解循环是否真的是无限的，这是一项艰巨的任务，因此，就目前而言，只有开发者自己可以帮助自己发现循环错误，避免资产损失。

### [通过 `continue` 和 `break` 控制循环](#通过-continue-和-break-控制循环)

`continue` 和 `break` 关键字，分别允许程序跳过一轮循环或中断循环，可以在两种类型的循环中同时使用它们。

例如，让我们在 `loop` 中添加两个条件，如果i是偶数，我们通过 `continue` 跳转到下一个迭代，而无需执行循环中 `continue` 之后的代码。当 i 等于 5 时，我们通过 break 停止迭代并退出循环。

```

    
    
    module book::c_if {
      fun main() {
        let i = 0;
        loop {
          i = i + 1;
          if (i / 2 == 0) continue;
          if (i == 5) break;
          // assume we do something here
         };
        std::debug::print<u8>(&i);
      }
    }
    
```

注意，如果 break 和 continue 是代码块中的最后一个关键字，则不能在其后加分号，因为后面的任何代码都不会被执行。请看这个例子：

```

    
    
    module book::c_if {
      fun main() {
        let i = 0;
        loop {
          i = i + 1;
          if (i == 5) {
            break; // will result in compiler error. correct is `break` without semi
                // Error: Unreachable code
          };
          // same with continue here: no semi, never;
          if (true) {
            continue
          };
          // however you can put semi like this, because continue and break here
          // are single expressions, hence they "end their own scope"
          if (true) continue;
          if (i == 5) break;
        }
      }
    }
    
```

### [有条件退出 `abort`](#有条件退出-abort)

有时，当某些条件失败时，您需要中止程序的执行。对于这种情况，Move 提供了有键字 abort。

```

    
    
    module book::c_if {
      fun main(a: u8) {
        if (a != 10) {
          abort 0;
        }
        // code here won't be executed if a != 10
        // transaction aborted
      }
    }
    
```

关键字 abort 允许程序中止执行的同时报告错误代码。

### [使用 `assert` 内置方法](#使用-assert-内置方法)

内置方法 `assert(<condition>, <code>)` 对 `abort`和条件进行了封装，你可以在代码中任何地方使用它。

```

    
    
    module book::c_if {
      fun main(a: u8) {
        assert(a == 10, 0);
        // code here will be executed if (a == 10)
      }
    }
    
```

`assert()` 在不满足条件时将中止执行，在满足条件时将不执行任何操作。

[ ](../basic/ch07.expression-and-scope.html "Previous chapter") [ ](../basic/ch09.event.html "Next chapter")

[ ](../basic/ch07.expression-and-scope.html "Previous chapter") [ ](../basic/ch09.event.html "Next chapter")

# ch09.event.html
# sui move book

[ ](../print.html "Print this book")

# [事件 Event](#事件-event)

> 事件是追踪链上动作的主要方式

```

    
    
    /// Extended example of a shared object. Now with addition of events!
    module examples::donuts_with_events {
      use sui::transfer;
      use sui::sui::SUI;
      use sui::coin::{Self, Coin};
      use sui::object::{Self, ID, UID};
      use sui::balance::{Self, Balance};
      use sui::tx_context::{Self, TxContext};
      // This is the only dependency you need for events.
      use sui::event;
      /// For when Coin balance is too low.
      const ENotEnough: u64 = 0;
      /// Capability that grants an owner the right to collect profits.
      public struct ShopOwnerCap has key { id: UID }
      /// A purchasable Donut. For simplicity's sake we ignore implementation.
      public struct Donut has key { id: UID }
      public struct DonutShop has key {
        id: UID,
        price: u64,
        balance: Balance<SUI>
      }
      // ====== Events ======
      /// For when someone has purchased a donut.
      public struct DonutBought has copy, drop {
        id: ID
      }
      /// For when DonutShop owner has collected profits.
      public struct ProfitsCollected has copy, drop {
        amount: u64
      }
      // ====== Functions ======
      fun init(ctx: &mut TxContext) {
        transfer::transfer(ShopOwnerCap {
          id: object::new(ctx)
        }, tx_context::sender(ctx));
        transfer::share_object(DonutShop {
          id: object::new(ctx),
          price: 1000,
          balance: balance::zero()
        })
      }
      /// Buy a donut.
      public entry fun buy_donut(
        shop: &mut DonutShop, payment: &mut Coin<SUI>, ctx: &mut TxContext
      ) {
        assert!(coin::value(payment) >= shop.price, ENotEnough);
        let coin_balance = coin::balance_mut(payment);
        let paid = balance::split(coin_balance, shop.price);
        let id = object::new(ctx);
        balance::join(&mut shop.balance, paid);
        // Emit the event using future object's ID.
        event::emit(DonutBought { id: object::uid_to_inner(&id) });
        transfer::transfer(Donut { id }, tx_context::sender(ctx))
      }
      /// Consume donut and get nothing...
      public entry fun eat_donut(d: Donut) {
        let Donut { id } = d;
        object::delete(id);
      }
      /// Take coin from `DonutShop` and transfer it to tx sender.
      /// Requires authorization with `ShopOwnerCap`.
      public entry fun collect_profits(
        _: &ShopOwnerCap, shop: &mut DonutShop, ctx: &mut TxContext
      ) {
        let amount = balance::value(&shop.balance);
        let profits = coin::take(&mut shop.balance, amount, ctx);
        // simply create new type instance and emit it
        event::emit(ProfitsCollected { amount });
        transfer::public_transfer(profits, tx_context::sender(ctx))
      }
    }
    
```

[ ](../basic/ch08.control-flow.html "Previous chapter") [ ](../basic/ch10.error.html "Next chapter")

[ ](../basic/ch08.control-flow.html "Previous chapter") [ ](../basic/ch10.error.html "Next chapter")

# ch10.error.html
# sui move book

[ ](../print.html "Print this book")

[ ](../basic/ch09.event.html "Previous chapter") [ ](../advanced/index.html "Next chapter")

[ ](../basic/ch09.event.html "Previous chapter") [ ](../advanced/index.html "Next chapter")

# index.html
# sui move book

[ ](../print.html "Print this book")

[ ](../basic/ch10.error.html "Previous chapter") [ ](../advanced/ch01.struct.html "Next chapter")

[ ](../basic/ch10.error.html "Previous chapter") [ ](../advanced/ch01.struct.html "Next chapter")

# ch01.struct.html
# sui move book

[ ](../print.html "Print this book")

# [结构体](#结构体)

结构体是自定义类型，它可以包含复杂数据， 也可以不包含任何数据。结构体由字段组成，可以简单地理解成"key-value"存储，其中 key 是字段的名称，而 value 是存储的内容。结构体使用关键字 struct 定义。

> 结构体是在 Move 中创建自定义类型的唯一方法。

### [定义](#定义)

结构体只能在模块内部定义，并且以关键字 `struct` 开头：

```

    
    
    public struct NAME {
      FIELD1: TYPE1,
      FIELD2: TYPE2,
      ...
    }
    
```

# [什么时候用结构体，](#什么时候用结构体)

简单数据类型 u8 - u256 bool不能满足，一个完整的表述的时候， 比如一个人，有身高，年龄，姓名，性别等等属性的时候, 把不同的数据类型复合成了一个复杂的数据结构来表示一个具体的东西

```

    
    
    module sui::s {
      use std::string::String;
      public struct Person{
        name:String,
        age:u64,
        gender:u8,
        height:u64
      }
    }
    
```

我们来看一些例子：

```

    
    
    module book::m {
      // struct can be without fields
      // but it is a new type
      public struct Empty {}
      public struct MyStruct {
        field1: address,
        field2: bool,
        field3: Empty
      }
      public struct Example {
        field1: u8,
        field2: address,
        field3: u64,
        field4: bool,
        field5: bool,
        // you can use another struct as type
        field6: MyStruct
      }
    }
    
```

_一个结构体最多可以有 65535 个字段。_

被定义的结构体会成为新的类型，可以通过定义它的模块访问此类型：

```

    
    
    M::MyStruct;
    // or
    M::Example;
    
```

### [定义递归结构体](#定义递归结构体)

> _定义递归结构体_ 是不允许的。

Move 允许使用其它结构作为成员，但不能递归使用相同的结构体。Move 编译器会检查递归定义，不允许下面这样的代码：

```

    
    
    module book::m {
      public struct MyStruct {
        // WON'T COMPILE
        field: MyStruct
      }
    }
    
```

### [创建结构体实例](#创建结构体实例)

> 要使用某结构体类型，需要先创建其实例。

可以用结构体的定义来创建实例，不同的是传入具体的值而不是类型。

```

    
    
    module book::country {
      public struct Country {
        id: u8,
        population: u64
      }
      // Contry is a return type of this function!
      public fun new_country(c_id: u8, c_population: u64): Country {
        // structure creation is an expression
        let country = Country {
          id: c_id,
          population: c_population
        };
        country
      }
    }
    
```

还可以通过传递与结构体的字段名匹配的变量名来简化创建新实例的代码。下面的 new_country() 函数中使用了这个简化方法：

```

    
    
    // ...
    public fun new_country(id: u8, population: u64): Country {
      // id matches id: u8 field
      // population matches population field
      public Country {
        id,
        population
      }
      // or even in one line: Country { id, population }
    }
    
```

要创建一个空结构体（没有字段），只需使用花括号：

```

    
    
    public fun empty(): Empty {
      Empty {}
    }
    
```

### [访问结构体成员字段](#访问结构体成员字段)

如果我们没有办法访问结构体的字段，那么它几乎是无用的。

> 只有在模块内才可以访问其结构体的字段。在模块之外，该结构体字段是不可见的。

结构字段仅在其模块内部可见。在此模块之外（在脚本或其他模块中），它只是一种类型。要访问结构的字段，请使用"."符号：

```

    
    
    // ...
    public fun get_country_population(country: Country): u64 {
      country.population // <struct>.<property>
    }
    
```

如果在同一模块中定义了嵌套结构类型，则可以用类似的方式对其进行访问，通常可以将其描述为：

```

    
    
    <struct>.<field>
    // and field can be another struct so
    <struct>.<field>.<nested_struct_field>...
    
```

### [为结构体字段实现 getter 方法](#为结构体字段实现-getter-方法)

为了使结构体字段在外部可读，需要实现一些方法，这些方法将读取这些字段并将它们作为返回值传递。通常，getter 方法的调用方式与结构体的字段相同，但是如果你的模块定义了多个结构体，则 getter 方法可能会带来不便。

```

    
    
    module book::country {
      public struct Country {
        id: u8,
        population: u64
      }
      public fun new_country(id: u8, population: u64): Country {
        Country {
          id, population
        }
      }
      // don't forget to make these methods public!
      public fun id(country: &Country): u8 {
        country.id
      }
      // don't mind ampersand here for now. you'll learn why it's 
      // put here in references chapter 
      public fun population(country: &Country): u64 {
        country.population
      }
      // ... fun destroy ... 
    }
    
```

通过 getter 方法，我们允许模块的使用者访问结构体的字段：

```

    
    
    module book::m {
      use {{sender}}::Country as C;
      use std::debug;
      fun main() {
        // variable here is of type C::Country
        let country = C::new_country(1, 10000000);
        debug::print<u8>(
          &C::id(&country)
        ); // print id
        debug::print<u64>(
          &C::population(&country)
        );
        // however this is impossible and will lead to compile error
        // let id = country.id;
        // let population = country.population.
      }
    }
    
```

### [回收结构体](#回收结构体)

解构、或者销毁结构体需要使用语法 `let <STRUCT DEF> = <STRUCT>`：

```

    
    
    module book::country {
      // ...
      // we'll return values of this struct outside
      public fun destroy(country: Country): (u8, u64) {
        // variables must match struct fields
        // all struct fields must be specified
        let Country { id, population } = country;
        // after destruction country is dropped
        // but its fields are now variables and
        // can be used
        (id, population)
      }
    }
    
```

请注意，Move 中禁止定义不会被使用的变量，有时你可能需要在不使用其字段的情况下销毁该结构体。对于未使用的结构体字段，请使用下划线"_"表示：

```

    
    
    module book::country {
      // ...
      public fun destroy(country: Country) {
        // this way you destroy struct and don't create unused variables
        let Country { id: _, population: _ } = country;
        // or take only id and don't init `population` variable
        // let Country { id, population: _ } = country;
      }
    }
    
```

[ ](../advanced/index.html "Previous chapter") [ ](../advanced/ch02.object.html "Next chapter")

[ ](../advanced/index.html "Previous chapter") [ ](../advanced/ch02.object.html "Next chapter")

# ch02.object.html
# sui move book

[ ](../print.html "Print this book")

# [struct](#struct)

```

    
    
    module book::obj {
      public struct Color {
        red: u8,
        green: u8,
        blue: u8,
      }
    }
    
```

# [对象](#对象)

  * 要说明白Sui的对象模型得先从先从Sui的链上数据存储模型说起
  * 对象在Sui上存储，维护了一个全局的Map数据结构 Map<ID,object>, 学过数据结构的都知道Map结构也可以理解成事HashMap的结构就是 key 是id,值是Object， id 肯定是唯一的，通过这个唯一的id 查找到object

# [如何定义一个对象](#如何定义一个对象)

  * 必须有 `key` 能力
  * 必须第一个字段 是id,而且类型为 `sui::object::UID`

```

    
    
    module book::obj {
      use sui::object::UID;
      public struct Obj has key {
        id:UID,
      }
    }
    
```

```

    
    
    module book::obj {
      use sui::object::UID;
      
      public struct ColorObject has key {
        id: UID,
        red: u8,
        green: u8,
        blue: u8,
      }
    }
    
```

代表`ColorObject`一个 Sui 对象类型，您可以使用它来创建最终可以存储在 Sui 网络上的 Sui 对象

### [](#)

  * 这是会报错的

```

    
    
    module book::obj {
      public struct Obj has key {
        a:u64
      }
    }
    
```

### [](#-1)

  * 这不是一个对象，只是一个普通数据结构

```

    
    
    module book::obj {
      public struct Obj1 {
        a:u64,
        b:u64
      }
      
      public struct Obj2 has store {
        a:u64,
        b:u64
      }
    }
    
```

### [UID 类型的说明](#uid-类型的说明)

定义在Sui的标准库中，具体标准库是什么会在后面又详细的说明，我们一起看一下UID的定义

```

    
    
    /// Sui object identifiers
    module sui::object {
      use std::bcs;
      use sui::address;
      use sui::tx_context::{Self, TxContext};
      
      public struct ID has copy, drop, store {
        // We use `address` instead of `vector<u8>` here because `address` has a more
        // compact serialization. `address` is serialized as a BCS fixed-length sequence,
        // which saves us the length prefix we would pay for if this were `vector<u8>`.
        // See https://github.com/diem/bcs#fixed-and-variable-length-sequences.
        bytes: address
      }
      /// Globally unique IDs that define an object's ID in storage. Any Sui Object, that is a struct
      /// with the `key` ability, must have `id: UID` as its first field.
      /// These are globally unique in the sense that no two values of type `UID` are ever equal, in
      /// other words for any two values `id1: UID` and `id2: UID`, `id1` != `id2`.
      /// This is a privileged type that can only be derived from a `TxContext`.
      /// `UID` doesn't have the `drop` ability, so deleting a `UID` requires a call to `delete`.
      public struct UID has store {
        id: ID,
      }
      
      // === any object ===
      /// Create a new object. Returns the `UID` that must be stored in a Sui object.
      /// This is the only way to create `UID`s.
      public fun new(ctx: &mut TxContext): UID {
        UID {
          id: ID { bytes: tx_context::fresh_object_address(ctx) },
        }
      }
    
      public fun delete(id: UID) {
        let UID { id: ID { bytes } } = id;
        delete_impl(bytes)
      }
    
    }
    
```

# [创建 Sui 对象](#创建-sui-对象)

严格来说，上面的代码只是定义好了 object 的模板，并不能说已经是object了，怎么理解这个呢 就像生产一个鼠标，我们只是做好了鼠标的模具，需要用这个模具来真正生产出具体的鼠标了， 这个鼠标才能算真正的对象，那么如何用我们定义好的对象模板生产出真正的对象， 其实如果你有其他编程语言基础的话你可能会很容易理解， 我们已经定义好了一个对象模板，需要生成一个对象实例，具体的代码如下

```

    
    
     module book::obj {
      use sui::object;
      use sui::object::UID;
      // tx_context::TxContext creates an alias to the TxContext struct in the tx_context module.
      use sui::tx_context::TxContext;
      public struct ColorObject has key {
        id: UID,
        red: u8,
        green: u8,
        blue: u8,
      }
      
      
      fun new(red: u8, green: u8, blue: u8, ctx: &mut TxContext): ColorObject {
        ColorObject {
          id: object::new(ctx),
          red,
          green,
          blue,
        }
      }
    }
    
```

定义 Sui 对象类型模板后，您可以创建或实例化 Sui 对象。 要从其类型创建一个新的 Sui 对象，您必须为每个字段分配一个初始值， 包括id. 为 Sui 对象创建新对象的唯一方法UID是调用object::new. 该new函数将当前事务上下文作为生成唯一 ID 的参数

```

    
    
    module book::obj {
      use sui::object::UID;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      use sui::object;
    
     public struct Obj has key {
        id: UID,
      }
      
      fun init(ctx: &mut TxContext) {
        let obj = Obj {
          id: object::new(ctx)
        };
        transfer::transfer(obj, tx_context::sender(ctx))
      }
    }
    
```

正如代码所看到的，创建出来的实例，需要调用标准库的方法转移，这就是我们接下来需要讲到的 object owner模型 也叫对象所有权，但是在这之前 我们先看一下对象的内部其他信息

# [对象内部元数据](#对象内部元数据)

每个 Sui 对象都有以下元数据：

一个 32 字节的全局唯一 ID。对象 ID 源自创建对象的交易摘要和对交易生成的 ID 数量进行编码的计数器。 一个 8 字节无符号整数版本，随着每个读取或写入它的事务单调增加。 一个 32 字节的交易摘要，指示包含此对象作为输出的最后一笔交易。 一个 21 字节的所有者字段，指示如何访问此对象。对象所有权将在下一节中详细解释

[ ](../advanced/ch01.struct.html "Previous chapter") [ ](../advanced/ch03.object-owner.html "Next chapter")

[ ](../advanced/ch01.struct.html "Previous chapter") [ ](../advanced/ch03.object-owner.html "Next chapter")

# ch03.object-owner.html
# sui move book

[ ](../print.html "Print this book")

# [对象所有权](#对象所有权)

每个对象都有一个所有者字段，指示如何拥有该对象。所有权决定了一个对象如何在事务中使用。有 4 种不同类型的所有权：

## [由一个地址拥有](#由一个地址拥有)

这是 Move 对象最常见的情况。在 Move 代码中创建的 Move 对象可以转移到一个地址。转移后，该对象将归该地址所有。地址拥有的对象只能由该所有者地址签名的交易使用（即作为 Move 调用参数传递）。拥有的对象可以作为 Move 调用参数以 3 种形式中的任何一种传递：只读引用 ( &T)、可变引用 ( &mut T) 和按值 ( T)。重要的是要注意，即使一个对象通过只读引用传递（&T) 在 Move 调用中，仍然需要只有对象的所有者才能进行这样的调用。也就是说，在验证对象是否可以在交易中使用时，Move 调用的意图无关紧要，所有权才是最重要的。

```

    
    
    module book::obj {
      use sui::object::UID;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      use sui::object;
    
      public struct Obj has key {
        id: UID,
      }
    
      fun init(ctx: &mut TxContext) {
        let obj = Obj {
          id: object::new(ctx)
        };
        transfer::transfer(obj, tx_context::sender(ctx))
      }
    }
    
    
```

## [由另一个对象拥有](#由另一个对象拥有)

一个对象可以由另一个对象拥有。将这种直接所有权与对象包装区分开来很重要。 当您将一个对象的结构定义的字段设置为另一种对象类型时，可以将一个对象包装/嵌入到另一个对象中。例如：

```

    
    
    module sui::obj_inner {
      use sui::object::{UID};
      use sui::tx_context::{TxContext, sender};
      use sui::object;
      use sui::transfer;
      public struct A has key,store{
        id:UID
      }
     public struct B has key {
        id:UID,
        a: A
      }
      fun init(ctx:& mut TxContext){
        let a = A{
          id:object::new(ctx)
        };
        let b = B{
          id:object::new(ctx),
          a
        };
        transfer::transfer(b,sender(ctx));
      }
    }
    
```

定义一个对象类型A，该类型包含一个类型为另一个对象类型的字段B。在这种情况下， type 的对象B被包装到 type 的对象中A。使用对象包装，包装的对象（在本例中为 object b）不会作为顶级对象存储在 Sui 存储中，并且无法通过对象 ID 访问它。相反，它只是 type 对象的序列化字节内容的一部分A。你可以把一个对象被包装的情况想象成类似于被删除的情况，只不过它的内容仍然存在于另一个对象的某个地方。

现在回到另一个对象拥有的对象的主题。当一个对象为另一个对象所有时，它不会被包装。这意味着子对象仍然作为顶级对象独立存在，可以直接在 Sui 存储中访问。所有权关系仅通过子对象的所有者字段进行跟踪。如果您仍想观察子对象或能够在其他事务中使用它，这将很有用。Sui 提供库 API 来使一个对象为另一个对象所拥有。有关如何执行此操作的更多详细信息，请参阅Sui Move 库。

# [不可变的 常量](#不可变的-常量)

您不能改变不可变对象，并且不可变对象没有独占所有者。任何人都可以在 Sui Move 调用中使用不可变对象。

```

    
    
    module book::obj {
      use sui::object::UID;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      use sui::object;
    
      public struct Obj has key {
        id: UID,
      }
    
      fun init(ctx: &mut TxContext) {
        let obj = Obj {
          id: object::new(ctx)
        };
        transfer::freeze_object(obj);
      }
    }
    
    
```

所有 Sui Move 包都是不可变对象：您无法在发布后更改 Sui Move 包。您可以使用freeze_object操作将 Sui Move 对象转换为不可变对象。您只能在 Sui Move 调用中将不可变对象作为只读引用 ( &T) 传递。

# [共享](#共享)

对象可以共享，这意味着任何人都可以读取或写入该对象。与可变拥有的对象（单写者）相比，共享对象需要共识来排序读取和写入。有关创建和访问共享对象的示例，请参阅共享对象。

在其他区块链中，每个对象都是共享的。但是，Sui 程序员通常可以选择使用共享对象、拥有对象或组合来实现特定用例。这种选择可能会对性能、安全性和实施复杂性产生影响。理解这些权衡的最好方法是查看一些以两种方式实现的用例示例：

托管：共享，拥有 拍卖：共享，拥有 井字游戏：共享，拥有

```

    
    
    module book::obj {
      use sui::object::UID;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      use sui::object;
    
      public struct Obj has key {
        id: UID,
      }
      
      fun init(ctx: &mut TxContext) {
        let obj = Obj {
          id: object::new(ctx)
        };
        transfer::share_object(obj);
      }
    }
    
    
```

```

    
    
    module sui::transfer {
      
      public fun transfer<T: key>(obj: T, recipient: address) {
        transfer_impl(obj, recipient)
      }
      
      public fun public_transfer<T: key + store>(obj: T, recipient: address) {
        transfer_impl(obj, recipient)
      }
      
      public fun freeze_object<T: key>(obj: T) {
        freeze_object_impl(obj)
      }
      public fun public_freeze_object<T: key + store>(obj: T) {
        freeze_object_impl(obj)
      }
    
      public fun share_object<T: key>(obj: T) {
        share_object_impl(obj)
      }
    
      public fun public_share_object<T: key + store>(obj: T) {
        share_object_impl(obj)
      }
      
    }
    
    
```

## [key + store](#key--store)

  * 你会看到 关于所有权专业的方法其实有两组
  * public_* 开头的函数 和没有 public的函数，那么什么时候使用 `public_*` 开头的函数呢

> 简单来说就是如果 定义的对象是 通用资产 就是 key + store 就用 `public_*`开头的函数

```

    
    
    module book::obj {
      use sui::object::UID;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      use sui::object;
    
      public struct Obj has key , store {
        id: UID,
      }
    
      fun init(ctx: &mut TxContext) {
        let obj = Obj {
          id: object::new(ctx)
        };
        transfer::public_transfer(obj, tx_context::sender(ctx))
      }
    }
    
    
```

# [详细讲解](#详细讲解)

  * https://examples.sui.io/basics/transfer.html
  * https://examples.sui.io/basics/custom-transfer.html

# [参考文档](#参考文档)

  * https://docs.sui.io/build/programming-with-objects/ch1-object-basics
  * https://docs.sui.io/learn/objects
  * https://www.notion.so/706community/2-1-Object-3f2aeeefccf447d295375d803379f136?pvs=4

[ ](../advanced/ch02.object.html "Previous chapter") [ ](../advanced/ch04.types-with-abilities.html "Next chapter")

[ ](../advanced/ch02.object.html "Previous chapter") [ ](../advanced/ch04.types-with-abilities.html "Next chapter")

# ch04.types-with-abilities.html
# sui move book

[ ](../print.html "Print this book")

# [Abilities](#abilities)

Move 的类型系统非常灵活，每种类型都可以被四种限制符所修饰。这四种限制符我们称之为 abilities，它们定义了类型的值是否可以被复制、丢弃和存储。

> 这四种 abilities 限制符分别是: Copy, Drop, Store 和 Key.

它们的功能分别是：

  * **Copy** - 被修饰的值可以被 _复制_ 。
  * **Drop** - 被修饰的值在作用域结束时可以被 _丢弃_ 。
  * **Key** - 被修饰的值可以作为 _键值_ 对全局状态进行访问。
  * **Store** - 被修饰的值可以被 _存储_ 到全局状态。

本章我们先介绍 `Copy` 和 `Drop` ability；关于 `Key` 和 `Store` ability 我们将会在 _Resources_ 一章介绍.

### [Abilities 语法](#abilities-语法)

> 基本类型和内建类型的 abilities 是预先定义好的并且不可改变: integers, vector, addresses 和 boolean 类型的值先天具有 _copy_ ， _drop_ 和 _store_ ability。

然而，结构体的 ability 可以由开发者按照下面的语法进行添加：

```

    
    
    struct NAME has ABILITY [, ABILITY] { [FIELDS] }
    
```

下面是一些例子:

```

    
    
    module book::library {
      
      // each ability has matching keyword
      // multiple abilities are listed with comma
      public struct Book has store, copy, drop {
        year: u64
      }
      // single ability is also possible
      public struct Storage has key {
        books: vector<Book>
      }
      // this one has no abilities 
      public struct Empty {}
    }
    
```

### [不带 Abilities 限制符的结构体](#不带-abilities-限制符的结构体)

在进入 abilities 的具体用法之前, 我们不妨先来看一下，如果结构体不带任何 abilities 会发生什么？

```

    
    
    module book::country {
      public struct Country {
        id: u8,
        population: u64
      }
      
      public fun new_country(id: u8, population: u64): Country {
        Country { id, population }
      }
    }
    
```

```

    
    
    module book::country_use {
      use book::Country;
      fun main() {
        Country::new_country(1, 1000000);
      }  
    }
    
```

。

### [Drop](#drop)

按照 abilities 语法我们为这个结构体增加 `drop` ability，这个结构体的所有实例将可以被丢弃。

```

    
    
    module book::country {
      public struct Country has drop { // has <ability>
        id: u8,
        population: u64
      }
      // ...
    }
    
```

现在，`Country` 可以被丢弃了，代码也可以成功执行了。

```

    
    
    script {
      use {{sender}}::Country;
      fun main() {
        Country::new_country(1, 1000000); // value is dropped
      }  
    }
    
```

> **注意** [_Destructuring_](/advanced-topics/struct.html#destructing-structures) 并不需要 Drop ability.

### [Copy](#copy)

我们学习了如何创建一个结构体 `Country` 并在函数结束时丢弃它。但是如果我们想要复制一个结构体呢？缺省情况下结构体是按值传递的，制造一个结构体的副本需要借助关键字 `copy` (我们会在 [下一章](/advanced-topics/ownership-and-references.html) 更加深入的学习)：

```

    
    
    script {
      use {{sender}}::Country;
      fun main() {
        let country = Country::new_country(1, 1000000);
        let _ = copy country;
      }  
    }
    
```

```

    
    
      ┌── scripts/main.move:6:17 ───
      │
     6 │     let _ = copy country;
      │         ^^^^^^^^^^^^ Invalid 'copy' of owned value without the 'copy' ability
      │
    
```

正如所料，缺少 `copy` ability 限制符的类型在进行复制时会报错：

```

    
    
    module book::country {
      public struct Country has drop, copy { // see comma here!
        id: u8,
        population: u64
      }
      // ...
    }
    
```

修改后的代码就可以成功执行了。

### [小结](#小结)

  * 基本类型缺省具有 store, copy 和 drop 限制。
  * 缺省情况下结构体不带任何限制符。
  * Copy 和 Drop 限制符定义了一个值是否可以被复制和丢弃。
  * 一个结构体有可能带有所有4种限制符。

### [进一步阅读](#进一步阅读)

[ ](../advanced/ch03.object-owner.html "Previous chapter") [ ](../advanced/ch05.generics.html "Next chapter")

[ ](../advanced/ch03.object-owner.html "Previous chapter") [ ](../advanced/ch05.generics.html "Next chapter")

# ch05.generics.html
# sui move book

[ ](../print.html "Print this book")

# [了解泛型](#了解泛型)

泛型对于 Move 语言是必不可少的，它使得 Move 语言在区块链世界中如此独特，它是 Move 灵活性的重要来源。

首先，让我来引用 [Rust Book](https://doc.rust-lang.org/stable/book/ch10-00-generics.html) 对于泛型得定义： _泛型是具体类型或其他属性的抽象替代品_ 。实际上，泛型允许我们只编写单个函数，而该函数可以应用于任何类型。这种函数也被称为模板 —— 一个可以应用于任何类型的模板处理程序。

Move 中泛型可以应用于结构体和函数的定义中。

### [结构体中的泛型](#结构体中的泛型)

首先，我们将创建一个可容纳`u64`整型的 Box ：

```

    
    
    module book::generics {
      public struct Box {
        value: u64
      }
    }
    
```

这个 Box 只能包含`u64`类型的值，这一点是非常清楚的。但是，如果我们想为`u8`类型或 bool类型创建相同的 Box 该怎么办呢？分别创建`u8`类型的 Box1 和`bool`型 Box2 吗？答案是否定的，因为可以使用泛型。

```

    
    
    module book::generics {
      public struct Box<T> {
        value: T
      }
    }
    
```

我们在结构体名字的后面增加`<T>`。尖括号`<..>`里面用来定义泛型，这里`T`就是我们在结构体中模板化的类型。在结构体中，我们已经将`T`用作常规类型。类型T实际并不存在，它只是任何类型的占位符。

### [函数中的泛型](#函数中的泛型)

现在让我们为上面的结构体创建一个构造函数，该构造函数将首先使用u64类型。

```

    
    
    module book::generics {
      public struct Box<T> {
        value: T
      }
      // type u64 is put into angle brackets meaning
      // that we're using Box with type u64
      public fun create_box(value: u64): Box<u64> {
        Box<u64>{ value }
      }
    }
    
```

带有泛型的结构体的创建稍微有些复杂，因为它们需要指定类型参数，需要把常规结构体 Box 变为 Box。Move没有任何限制什么类型可以被放进尖括号中。但是为了让`create_box`方法更通用，有没有更简单的方法？有的，在函数中使用泛型！

```

    
    
    module book::generics {
      // ...
      public fun create_box<T>(value: T): Box<T> {
        Box<T> { value }
      }
      // we'll get to this a bit later, trust me
      public fun value<T: copy>(box: &Box<T>): T {
        *&box.value
      }
    }
    
```

### [函数调用中使用泛型](#函数调用中使用泛型)

上例中在定义函数时，我们像结构体一样在函数名之后添加了尖括号。如何使用它呢？就是在函数调用中指定类型。

```

    
    
    module book::generics {
      use {{sender}}::storage;
      use std::debug;
      fun main() {
        // value will be of type storage::Box<bool>
        let bool_box = storage::create_box<bool>(true);
        let bool_val = storage::value(&bool_box);
        assert(bool_val, 0);
        // we can do the same with integer
        let u64_box = storage::create_box<u64>(1000000);
        let _ = storage::value(&u64_box);
        // let's do the same with another box!
        let u64_box_in_box = storage::create_box<storage::Box<u64>>(u64_box);
        // accessing value of this box in box will be tricky :)
        // Box<u64> is a type and Box<Box<u64>> is also a type
        let value: u64 = storage::value<u64>(
          &storage::value<storage::Box<u64>>( // Box<u64> type
            &u64_box_in_box // Box<Box<u64>> type
          )
        );
        // you've already seed debug::print<T> method
        // which also uses generics to print any type
        debug::print<u64>(&value);
      }
    }
    
```

这里我们用三种类型使用了 Box：`bool`, `u64` 和 `Box<u64>`。最后一个看起来有些复杂，但是一旦你习惯了，并且理解了泛型是如何工作的，它成为你日常工作的好帮手。

继续下一步之前，让我们做一个简单的回顾。我们通过将泛型添加到`Box`结构体中，使`Box`变得抽象了。与 Box 能提供的功能相比，它的定义相当简单。现在，我们可以使用任何类型创建`Box`，u64 或 address，甚至另一个 Box 或另一个结构体。

### [abilities 限制符](#abilities-限制符)

我们已经学习了 [abilities](/advanced/types-with-abilities.html)，它们可以作为泛型的限制符来使用，限制符的名称和 ability 相同。

```

    
    
    fun name<T: copy>() {} // allow only values that can be copied
    fun name<T: copy + drop>() {} // values can be copied and dropped
    fun name<T: key + store + drop + copy>() {} // all 4 abilities are present
    
```

也可以在结构体泛型参数中使用:

```

    
    
    public struct name<T: copy + drop> { value: T } // T can be copied and dropped
    public struct name<T: stored> { value: T } // T can be stored in global storage
    
```

> 请记住 `+` 这个语法符号，第一眼看上去可能不太适应，因为很少有语言在关键字列表中使用 `+`。

下面是一个使用限制符的例子：

```

    
    
    module book::generics {
      // contents of the box can be stored
     public struct Box<T: store> has key, store {
        content: T
      }
    }
    
```

另一个需要被提及的是结构体的成员必须和结构体具有相同的 abilities （除了`key`以外）。这个很容易理解，如果结构体具有 **copy** ability，那么它的成员也必须能被 **copy** ，否则结构体作为一个整体不能被 **copy** 。Move 编译器允许代码不遵守这样的逻辑，但是运行时会出问题。

```

    
    
    module book::generics {
      // non-copyable or droppable struct
      public struct Error {}
      
      // constraints are not specified
      public struct Box<T> has copy, drop {
        contents: T
      }
      // this method creates box with non-copyable or droppable contents
      public fun create_box(): Box<Error> {
        Box { contents: Error {} }
      }
    }
    
```

这段代码可以成功编译和发布，但是如果你运行它就会出问题。

```

    
    
    module book::generics {
      fun main() {
        {{sender}}::storage::create_box() // value is created and dropped
      }  
    }
    
```

运行结果是报错 Box 不能被 drop。

```

    
    
      ┌── scripts/main.move:5:9 ───
      │
     5 │  storage::create_box();
      │  ^^^^^^^^^^^^^^^^^^^^^ Cannot ignore values without the 'drop' ability. The value must be used
      │
    
```

原因是创建结构体时所使用的成员值没有 drop ability。也就是 contents 不具备 Box 所要求的 abilities - copy 和 drop。

> 但是为了避免犯错，应该尽可能使泛型参数的限制符和结构体本身的 abilities 显式的保持一致。

所以下面这种定义的方法更安全：

```

    
    
    // we add parent's constraints
    // now inner type MUST be copyable and droppable
    public struct Box<T: copy + drop> has copy, drop {
      contents: T
    }
    
```

### [泛型中包含多个类型](#泛型中包含多个类型)

我们也可以在泛型中使用多个类型，像使用单个类型一样，把多个类型放在尖括号中，并用逗号分隔。我们来试着添加一个新类型`Shelf`，它将容纳两个不同类型的`Box`。

```

    
    
    module book::generics {
      public struct Box<T> {
        value: T
      }
      public struct Shelf<T1, T2> {
        box_1: Box<T1>,
        box_2: Box<T2>
      }
      public fun create_shelf<Type1, Type2>(
        box_1: Box<Type1>,
        box_2: Box<Type2>
      ): Shelf<Type1, Type2> {
        Shelf {
          box_1,
          box_2
        }
      }
    }
    
```

`Shelf`的类型参数需要与结构体字段定义中的类型顺序相匹配，而泛型中的类型参数的名称则无需相同，选择合适的名称即可。正是因为每种类型参数仅仅在其作用域范围内有效，所以无需使用相同的名字。

多类型泛型的使用与单类型泛型相同：

```

    
    
    module book::generics {
      use {{sender}}::storage;
      fun main() {
        let b1 = storage::create_box<u64>(100);
        let b2 = storage::create_box<u64>(200);
        // you can use any types - so same ones are also valid
        let _ = storage::create_shelf<u64, u64>(b1, b2);
      }
    }
    
```

*你可以在函数或结构体定义中最多使用 18,446,744,073,709,551,615 (u64 最大值) 个泛型。你绝对不会达到此限制，因此可以随意使用。

### [未使用的类型参数](#未使用的类型参数)

并非泛型中指定的每种类型参数都必须被使用。看这个例子：

```

    
    
    module book::generics {
      // these two types will be used to mark
      // where box will be sent when it's taken from shelf
      public struct Abroad {}
      public struct Local {}
      // modified Box will have target property
      public struct Box<T, Destination> {
        value: T
      }
      public fun create_box<T, Dest>(value: T): Box<T, Dest> {
        Box { value }
      }
    }
    
```

也可以在脚本中使用 :

```

    
    
    module book::generics {
      use {{sender}}::storage;
      fun main() {
        // value will be of type storage::Box<bool>
        let _ = storage::create_box<bool, storage::Abroad>(true);
        let _ = storage::create_box<u64, storage::Abroad>(1000);
        let _ = storage::create_box<u128, storage::Local>(1000);
        let _ = storage::create_box<address, storage::Local>(0x1);
        // or even u64 destination!
        let _ = storage::create_box<address, u64>(0x1);
      }
    }
    
```

[ ](../advanced/ch04.types-with-abilities.html "Previous chapter") [ ](../framework/index.html "Next chapter")

[ ](../advanced/ch04.types-with-abilities.html "Previous chapter") [ ](../framework/index.html "Next chapter")

# index.html
# sui move book

[ ](../print.html "Print this book")

[ ](../advanced/ch05.generics.html "Previous chapter") [ ](../framework/01.into.html "Next chapter")

[ ](../advanced/ch05.generics.html "Previous chapter") [ ](../framework/01.into.html "Next chapter")

# 01.into.html
# sui move book

[ ](../print.html "Print this book")

# [Sui Framework](#sui-framework)

  * [Sui Framework](https://github.com/MystenLabs/sui/tree/main/crates/sui-framework/packages) 什么是 Sui Framework， 大家第一次看到 Framework的时候会觉得奇怪，不知道是什么， 简单来说就是Sui Move的标准库，官方已经封装好了一些常用的库供大家使用
  * Sui Framework 是经过了长期打磨，和安全验证的代码，阅读Framework不仅仅能掌握常用的工具库， 而且能更好的学习Sui Move，Framework每一个包和库都值得大家精心阅读和学习

# [move-stdlib](#move-stdlib)

> move stdlib 是来自Move上游的核心标准库，可以理解成最核心的标准库，基本上是需要大家都掌握的

## [总览，可以这样说 就是最好把这个系列的都完全掌握](#总览可以这样说-就是最好把这个系列的都完全掌握)

模块名| 大概用途| 需要掌握程度  
---|---|---  
address.move| 地址长度| 了解  
ascii.move| ascii编码的字符串| 完全掌握  
bcs.move| 把数据序列化成二进制| 掌握  
bit_vector.move| bit 位标记的数组| 掌握  
debug.move| 调试代码，打印输出| 完全掌握  
fixed_point32.move| 浮点数| 掌握  
hash.move| hash函数| 掌握  
option.move| 可选值| 完全掌握  
type_name.move| 获取结构的类型| 完全掌握  
unit_test.move| 单元测试生成测试signers| 了解  
vector.move| 数组| 完全掌握  
  
## [address.move](#addressmove)

```

    
    
    基本上没什么用处
    
```

```

    
    
    module std::address {
      public fun length(): u64 {
        32
      }
    }
    
```

## [ascii.move](#asciimove)

ascii 编码规则的字符串,说白了就是只有a-z A-Z 0-9这些编码的字符串，只支持早期的英文字符

```

    
    
    // Copyright (c) Mysten Labs, Inc.
    // SPDX-License-Identifier: Apache-2.0
    /// The `ASCII` module defines basic string and char newtypes in Move that verify
    /// that characters are valid ASCII, and that strings consist of only valid ASCII characters.
    module std::ascii {
      use std::vector;
      use std::option::{Self, Option};
      /// An invalid ASCII character was encountered when creating an ASCII string.
      const EINVALID_ASCII_CHARACTER: u64 = 0x10000;
    
      struct String has copy, drop, store {
        bytes: vector<u8>,
      }
      /// An ASCII character.
      struct Char has copy, drop, store {
        byte: u8,
      }
    
      /// Convert a `byte` into a `Char` that is checked to make sure it is valid ASCII.
      public fun char(byte: u8): Char {
        assert!(is_valid_char(byte), EINVALID_ASCII_CHARACTER);
        Char { byte }
      }
    
      /// Convert a vector of bytes `bytes` into an `String`. Aborts if
      /// `bytes` contains non-ASCII characters.
      public fun string(bytes: vector<u8>): String {
        let x = try_string(bytes);
        assert!(
          option::is_some(&x),
          EINVALID_ASCII_CHARACTER
        );
        option::destroy_some(x)
      }
     
      
      public fun try_string(bytes: vector<u8>): Option<String> {
        let len = vector::length(&bytes);
        let i = 0;
        while ({
          spec {
            invariant i <= len;
            invariant forall j in 0..i: is_valid_char(bytes[j]);
          };
          i < len
        }) {
          let possible_byte = *vector::borrow(&bytes, i);
          if (!is_valid_char(possible_byte)) return option::none();
          i = i + 1;
        };
        spec {
          assert i == len;
          assert forall j in 0..len: is_valid_char(bytes[j]);
        };
        option::some(String { bytes })
      }
      /// Returns `true` if all characters in `string` are printable characters
      /// Returns `false` otherwise. Not all `String`s are printable strings.
      public fun all_characters_printable(string: &String): bool {
        let len = vector::length(&string.bytes);
        let i = 0;
        while ({
          spec {
            invariant i <= len;
            invariant forall j in 0..i: is_printable_char(string.bytes[j]);
          };
          i < len
        }) {
          let byte = *vector::borrow(&string.bytes, i);
          if (!is_printable_char(byte)) return false;
          i = i + 1;
        };
        spec {
          assert i == len;
          assert forall j in 0..len: is_printable_char(string.bytes[j]);
        };
        true
      }
     
      public fun push_char(string: &mut String, char: Char) {
        vector::push_back(&mut string.bytes, char.byte);
      }
    
      public fun pop_char(string: &mut String): Char {
        Char { byte: vector::pop_back(&mut string.bytes) }
      }
     
      public fun length(string: &String): u64 {
        vector::length(as_bytes(string))
      }
      /// Get the inner bytes of the `string` as a reference
      public fun as_bytes(string: &String): &vector<u8> {
        &string.bytes
      }
      /// Unpack the `string` to get its backing bytes
      public fun into_bytes(string: String): vector<u8> {
        let String { bytes } = string;
        bytes
      }
      /// Unpack the `char` into its underlying byte.
      public fun byte(char: Char): u8 {
        let Char { byte } = char;
        byte
      }
      /// Returns `true` if `b` is a valid ASCII character. Returns `false` otherwise.
      public fun is_valid_char(b: u8): bool {
        b <= 0x7F
      }
      /// Returns `true` if `byte` is an printable ASCII character. Returns `false` otherwise.
      public fun is_printable_char(byte: u8): bool {
        byte >= 0x20 && // Disallow metacharacters
        byte <= 0x7E // Don't allow DEL metacharacter
      }
    }
    
    
```

### [bcs](#bcs)

> bcs 大家可能比较模糊，有的人不太了解是什么东西，我举一个例子，就是你定义好一个json的数据结构，然后把它转成json字符串 字符串这个类型就是一个通用的数据类型 bsc存在的意义就是把Move的数据结构转成通用的序列化好的一种二进制 好处就是单一类型了，通用性很强，能在不同的编程语言之间传递，反序列化回来了也具有唯一的数据对应

```

    
    
    module std::bcs {
      /// Return the binary representation of `v` in BCS (Binary Canonical Serialization) format
      native public fun to_bytes<MoveValue>(v: &MoveValue): vector<u8>;
    }
    
```

### [bit_vector.move](#bit_vectormove)

数组的一种结构， [bool,length] [ture,false,ture],就是会标注index位置有没有被使用

```

    
    
    module std::bit_vector {
      struct BitVector has copy, drop, store {
        length: u64,
        bit_field: vector<bool>,
      }
    }
    
```

### [debug.move](#debugmove)

提供了在开发节点，人工打印调试信息

```

    
    
    module std::debug {
      native public fun print<T>(x: &T);
      native public fun print_stack_trace();
    }
    
```

### [hash.move](#hashmove)

提供了两个常用的hash函数

```

    
    
    module std::hash {
      native public fun sha2_256(data: vector<u8>): vector<u8>;
      native public fun sha3_256(data: vector<u8>): vector<u8>;
    }
    
```

### [option.move](#optionmove)

一种容器类，表达的是，一个东西能为空，而且用这种类型会约束你强制处理为空的情况 简单来说一个NFT的图片，允许为空，或者必须填写

```

    
    
    // Copyright (c) Mysten Labs, Inc.
    // SPDX-License-Identifier: Apache-2.0
    /// This module defines the Option type and its methods to represent and handle an optional value.
    module std::option {
      use std::vector;
      
      struct Option<Element> has copy, drop, store {
        vec: vector<Element>
      }
      
      /// Return an empty `Option`
      public fun none<Element>(): Option<Element> {
        Option { vec: vector::empty() }
      }
      /// Return an `Option` containing `e`
      public fun some<Element>(e: Element): Option<Element> {
        Option { vec: vector::singleton(e) }
      }
      
      /// Return true if `t` does not hold a value
      public fun is_none<Element>(t: &Option<Element>): bool {
        vector::is_empty(&t.vec)
      }
      /// Return true if `t` holds a value
      public fun is_some<Element>(t: &Option<Element>): bool {
        !vector::is_empty(&t.vec)
      }
      
    }
    
```

### [string.move](#stringmove)

  * 字符串的UTF-8版本
  * 提供了一些ascii转换的方法和定义

```

    
    
    module std::string {
      use std::ascii;
      use std::vector;
      use std::option::{Self, Option};
      /// An invalid UTF8 encoding.
      const EINVALID_UTF8: u64 = 1;
      /// Index out of range.
      const EINVALID_INDEX: u64 = 2;
      /// A `String` holds a sequence of bytes which is guaranteed to be in utf8 format.
      struct String has copy, drop, store {
        bytes: vector<u8>,
      }
      /// Creates a new string from a sequence of bytes. Aborts if the bytes do not represent valid utf8.
      public fun utf8(bytes: vector<u8>): String {
        assert!(internal_check_utf8(&bytes), EINVALID_UTF8);
        String{bytes}
      }
      /// Convert an ASCII string to a UTF8 string
      public fun from_ascii(s: ascii::String): String {
        String { bytes: ascii::into_bytes(s) }
      }
      /// Convert an UTF8 string to an ASCII string.
      /// Aborts if `s` is not valid ASCII
      public fun to_ascii(s: String): ascii::String {
        let String { bytes } = s;
        ascii::string(bytes)
      }
    }
    
```

### [type_name.move](#type_namemove)

```

    
    
    // Copyright (c) Mysten Labs, Inc.
    // SPDX-License-Identifier: Apache-2.0
    /// Functionality for converting Move types into values. Use with care!
    module std::type_name {
      use std::ascii::{Self, String};
      use std::address;
      use std::vector;
      /// ASCII Character code for the `:` (colon) symbol.
      const ASCII_COLON: u8 = 58;
      struct TypeName has copy, drop, store {
        name: String
      }
      /// Return a value representation of the type `T`.
      public native fun get<T>(): TypeName;
     
      /// Get the String representation of `self`
      public fun borrow_string(self: &TypeName): &String {
        &self.name
      }
      /// Get Address string (Base16 encoded), first part of the TypeName.
      public fun get_address(self: &TypeName): String {
        // Base16 (string) representation of an address has 2 symbols per byte.
        let len = address::length() * 2;
        let str_bytes = ascii::as_bytes(&self.name);
        let addr_bytes = vector[];
        let i = 0;
        // Read `len` bytes from the type name and push them to addr_bytes.
        while (i < len) {
          vector::push_back(
            &mut addr_bytes,
            *vector::borrow(str_bytes, i)
          );
          i = i + 1;
        };
        ascii::string(addr_bytes)
      }
      /// Get name of the module.
      public fun get_module(self: &TypeName): String {
        // Starts after address and a double colon: `<addr as HEX>::`
        let i = address::length() * 2 + 2;
        let str_bytes = ascii::as_bytes(&self.name);
        let module_name = vector[];
        loop {
          let char = vector::borrow(str_bytes, i);
          if (char != &ASCII_COLON) {
            vector::push_back(&mut module_name, *char);
            i = i + 1;
          } else {
            break
          }
        };
        ascii::string(module_name)
      }
      /// Convert `self` into its inner String
      public fun into_string(self: TypeName): String {
        self.name
      }
    }
    
```

# [sui-framework](#sui-framework-1)

> sui 独有的标准库，和其他链的不一样

模块名| 大概用途| 需要掌握程度  
---|---|---  
address.move| 提供了一些地址和其他类型的转换| 掌握  
bag.move| map结构，值可以类型不一样| 完全掌握  
bcs.move| 在核心标准库上做了补充| 完全掌握  
borrow.move| 掌握  
clock.move| 获取链上时间| 完全掌握  
coin.move| 类似ERC20标准| 完全掌握  
display.move| NFT展现标准| 完全掌握  
dynamic_field.move| 动态属性| 掌握  
dynamic_object_field.move| 动态对象属性| 掌握  
event.move| 打印日志| 完全掌握  
hex.move| hex编码| 完全掌握  
linked_table.move| table的一种| 掌握  
math.move| 常用数学工具函数| 完全掌握  
object.move| 对象工具库| 完全掌握  
object_bag.move| map结构，值是对象类型可以不一样| 完全掌握  
object_table.move| map结构，值是对象类型必须一样| 完全掌握  
package.move| 包管理和升级| 掌握  
pay.move| 对Coin的快捷处理函数，| 完全掌握  
prover.move| 用不上自行了解| 了解  
sui.move| SUI 的定义| 了解  
table.move| map结构，值类型必须一样| 完全掌握  
table_vec.move| 用table实现的数组| 掌握  
transfer.move| 转移对象所有权，| 完全掌握  
tx_context.move| 取得当前交易钱包信息的上下文| 完全掌握  
types.move| 类型工具，目前只有判断OWT类型| 完全掌握  
vec_map.move| 底层是vec数组的map| 掌握  
vec_set.move| 底层是vec数组的Set| 掌握  
versioned.move| 版本管理的工具类| 掌握  
kiosk 目录| NFT交易的基础工具类| 完全掌握  
crypto 目录| 高阶的加密算法| 了解  
test.move| 测试相关工具库| 完全掌握  
  
### [borrow.move](#borrowmove)

  * 一个简单的库，支持烫手山芋的借用机制。
  * 在可编程事务中，可以在内部借用值
  * 一个事务，使用它并在最后放回。“Borrow”是个烫手山芋 
  * 确保返回的对象没有被替换为另一个对象。

> 如何理解呢？ 就是用确定一个对象在不同的合约之间传递到还回来的时候没有被改变id

### [clock.move](#clockmove)

  * 提供了一个获取链上时间的方法,Sui上链上的时间在 0x6对象实例，只有这一个唯一的对象实例

```

    
    
    module sui::clock {
      use sui::object::{Self, UID};
      struct Clock has key {
        id: UID,
        timestamp_ms: u64,
      }
    
      public fun timestamp_ms(clock: &Clock): u64 {
        clock.timestamp_ms
      }
    }
    
```

### [hex.move](#hexmove)

base16编码 就是把二进制的数据转成16进制表示让肉眼方便阅读和简短，比如 address

### [linked_table.move](#linked_tablemove)

链表数据结构 类似于`sui::table`，但值是链接在一起的，允许有序插入和删除

### [math.move](#mathmove)

  * 最大值，最小值，平均值，差值，指数，开平方

## [集合的类型如何选择？](#集合的类型如何选择)

  * bag 和 table的选择

> 如果值类型一样就选table,如果值类型不一样就选bas

  * object_table 和 table的选择

> 如果值确定是 object就选 object_table ，否则选 table， table范围更广泛

  * object_bag 和 bag

> 如果值确定是 object就选 object_bag ，否则选 table， bag范围更广泛

  * vec相关的和 table 和bag

> 大小已知而且小于1000用 vec ，大小未知 数据比较大 用 table

## [vec相关的集合](#vec相关的集合)

  * 使用vec相关的集合都要非常的小心， 不能存储大量的数据， 理论上必须小于1000，而且最好不要提供让用户来自行添加数据，也就是不确定长度的，很容易产生gas不足的安全攻击

[ ](../framework/index.html "Previous chapter") [ ](../framework/02.coin.html "Next chapter")

[ ](../framework/index.html "Previous chapter") [ ](../framework/02.coin.html "Next chapter")

# 02.coin.html
# sui move book

[ ](../print.html "Print this book")

# [Coin & ERC20](#coin---erc20)

很多学过其他智能合约编程语言的，或者对以太坊比较了解的，学习Sui Move的时候 经常会问，Sui Move的REC20，简单么，怎么实现？

## [什么是ERC20](#什么是erc20)

  * ERC-20就是一套基于以太坊网络的标准代币发行协议。 其中的ERC是"EthereumRequest for Comment"也就是“以太坊征求意见协议”的缩写。 和其他的开源社区一样，以太坊会使用这样的方式收集开发者的反馈、并在批准后作为后续开发的执行标准。 正是因为有了ERC-20，开发者们得以高效、可靠、低成本地创造专属自己项目的代币。

  * 如果我们把众多区块链项目的开发者，看作是在一个小区门口商铺里，经营不同业态的众多商户。 这些商户根据自己的专长提供各自不同的商品和服务，也都希望发行自己店铺专属的消费储值卡。方便消费者光顾的同时，也能提升用户的体验和粘性。 如果我们把众多区块链项目的开发者，看作是在一个小区门口商铺里，经营不同业态的众多商户。这些商户根据自己的专长提供各自不同的商品和服务，也都希望发行自己店铺专属的消费储值卡。

  * 方便消费者光顾的同时，也能提升用户的体验和粘性。

  * 它提供一整套标准化的储值卡发放协议和配套服务。借助这套叫做ERC-20的整体解决方案，

  * 每个商户（开发者）都可以傻瓜式地发行专属于自己店铺的消费储值卡，同时由于这种储值卡采用了统一的协议，可以非常方便地和其他商户的储值卡做无缝兑换。

  * 于是借助ERC-20，用户可以通过持有其中一种储值卡（token）很方便地享受整个生态的各种服务；商户（开发者）则节约了开发运营成本、同时提升了获取用户的效率；而物业公司（以太坊基金会和矿工）则可以通过做大生态体量实现更多的租金（ETH增值）和储值卡结算手续费（Gas费用）收入。

  * ERC-20就是用这种做大生态价值的方式，实现了用户、开发者和以太坊网络三方面的共赢

## [Sui Move Coin](#sui-move-coin)

  * 正式因为ERC20的影响力非常的大，后续出现的公链或多或少的都受到了以太坊ERC20的影响，Sui Move也buliw ，但是Move面向资产和所有权的模型，还有很多Sui Move 独有的东西比如object，让ERC20来Sui上对应的Coin协议 有了很大创新和不同，总结起来就是使用的时候更简单，和更加通用了

## [发行一个Sui Move Coin](#发行一个sui-move-coin)

很多人没有接触过Move的coin协议 （类似ERC20），我们先不关注里面的概念先从第一个Sui Move Coin的例子开始学习Sui Move的coin协议

```

    
    
    module examples::mycoin {
      use std::option;
      use sui::coin;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      /// The type identifier of coin. The coin will have a type
      /// tag of kind: `Coin<package_object::mycoin::MYCOIN>`
      /// Make sure that the name of the type matches the module's name.
      public struct MYCOIN has drop {}
      /// Module initializer is called once on module publish. A treasury
      /// cap is sent to the publisher, who then controls minting and burning
      fun init(witness: MYCOIN, ctx: &mut TxContext) {
        let (treasury, metadata) = coin::create_currency(witness, 6, b"MYCOIN", b"", b"", option::none(), ctx);
        transfer::public_freeze_object(metadata);
        transfer::public_transfer(treasury, tx_context::sender(ctx))
      }
    }
    
```

  * from https://examples.sui.io [mycoin](https://examples.sui.io/samples/coin.html)

```

    
    
    module sui::coin {
      /// A coin of type `T` worth `value`. Transferable and storable
      struct Coin<phantom T> has key, store {
        id: UID,
        balance: Balance<T>
      }
      public struct CoinMetadata<phantom T> has key, store {
        id: UID,
        /// Number of decimal places the coin uses.
        /// A coin with `value ` N and `decimals` D should be shown as N / 10^D
        /// E.g., a coin with `value` 7002 and decimals 3 should be displayed as 7.002
        /// This is metadata for display usage only.
        decimals: u8,
        /// Name for the token
        name: string::String,
        /// Symbol for the token
        symbol: ascii::String,
        /// Description of the token
        description: string::String,
        /// URL for the token logo
        icon_url: Option<Url>
      }
      /// Capability allowing the bearer to mint and burn
      /// coins of type `T`. Transferable
      public struct TreasuryCap<phantom T> has key, store {
        id: UID,
        total_supply: Supply<T>
      }
      
      
      public fun treasury_into_supply<T>(treasury: TreasuryCap<T>): Supply<T> {
        let TreasuryCap { id, total_supply } = treasury;
        object::delete(id);
        total_supply
      }
      
      
      public fun create_currency<T: drop>(
        witness: T,
        decimals: u8,
        symbol: vector<u8>,
        name: vector<u8>,
        description: vector<u8>,
        icon_url: Option<Url>,
        ctx: &mut TxContext
      ): (TreasuryCap<T>, CoinMetadata<T>) {
        // Make sure there's only one instance of the type T
        assert!(sui::types::is_one_time_witness(&witness), EBadWitness);
        (
          TreasuryCap {
            id: object::new(ctx),
            total_supply: balance::create_supply(witness)
          },
          CoinMetadata {
            id: object::new(ctx),
            decimals,
            name: string::utf8(name),
            symbol: ascii::string(symbol),
            description: string::utf8(description),
            icon_url
          }
        )
      }
      /// Create a coin worth `value`. and increase the total supply
      /// in `cap` accordingly.
      public fun mint<T>(
        cap: &mut TreasuryCap<T>, value: u64, ctx: &mut TxContext,
      ): Coin<T> {
        Coin {
          id: object::new(ctx),
          balance: balance::increase_supply(&mut cap.total_supply, value)
        }
      }
      
      /// Destroy the coin `c` and decrease the total supply in `cap`
      /// accordingly.
      public entry fun burn<T>(cap: &mut TreasuryCap<T>, c: Coin<T>): u64 {
        let Coin { id, balance } = c;
        object::delete(id);
        balance::decrease_supply(&mut cap.total_supply, balance)
      }
      
    }
    
```

```

    
    
    module sui::balance {
      use sui::tx_context::{Self, TxContext};
      /// For when trying to destroy a non-zero balance.
      const ENonZero: u64 = 0;
      /// For when an overflow is happening on Supply operations.
      const EOverflow: u64 = 1;
      
      /// A Supply of T. Used for minting and burning.
      /// Wrapped into a `TreasuryCap` in the `Coin` module.
      public struct Supply<phantom T> has store {
        value: u64
      }
      /// Storable balance - an inner struct of a Coin type.
      /// Can be used to store coins which don't need the key ability.
      public struct Balance<phantom T> has store {
        value: u64
      }
      
      /// Create a new supply for type T.
      public fun create_supply<T: drop>(_: T): Supply<T> {
        Supply { value: 0 }
      }
      /// Increase supply by `value` and create a new `Balance<T>` with this value.
      public fun increase_supply<T>(self: &mut Supply<T>, value: u64): Balance<T> {
        assert!(value < (18446744073709551615u64 - self.value), EOverflow);
        self.value = self.value + value;
        Balance { value }
      }
      /// Burn a Balance<T> and decrease Supply<T>.
      public fun decrease_supply<T>(self: &mut Supply<T>, balance: Balance<T>): u64 {
        let Balance { value } = balance;
        assert!(self.value >= value, EOverflow);
        self.value = self.value - value;
        value
      }
      
    }
    
```

## [如何发行一个由一个地址控制mint和销毁的 Coin](#如何发行一个由一个地址控制mint和销毁的-coin)

### [初始化](#初始化)

```

    
    
    module examples::mycoin {
      use std::option;
      use sui::coin;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      /// The type identifier of coin. The coin will have a type
      /// tag of kind: `Coin<package_object::mycoin::MYCOIN>`
      /// Make sure that the name of the type matches the module's name.
      public struct MYCOIN has drop {}
      /// Module initializer is called once on module publish. A treasury
      /// cap is sent to the publisher, who then controls minting and burning
      fun init(witness: MYCOIN, ctx: &mut TxContext) {
        let (treasury, metadata) = coin::create_currency(witness, 6, b"MYCOIN", b"", b"", option::none(), ctx);
        transfer::public_freeze_object(metadata);
        transfer::public_transfer(treasury, tx_context::sender(ctx))
      }
    }
    
```

上述代码将 treasury cap 发送给发行者，发行者可以mint和burn

### [铸造 mint](#铸造-mint)

```

    
    
    public entry fun mint(
      treasury_cap: &mut TreasuryCap<MYCOIN>, 
      amount: u64, 
      recipient: address, 
      ctx: &mut TxContext,
    ) {
      let coin = coin::mint(treasury_cap, amount, ctx);
      transfer::public_transfer(coin, recipient)
    }
    
```

### [销毁 Coin](#销毁-coin)

需要传入之前生成的 treasury cap id

```

    
    
      函数原型
      public entry fun burn<T>(cap: &mut TreasuryCap<T>, c: Coin<T>): u64 {
        let Coin { id, balance } = c;
        id.delete();
        cap.total_supply.decrease_supply(balance)
      }
    
```

## [如何发行一个水龙头Coin](#如何发行一个水龙头coin)

```

    
    
    module examples::mycoin {
      use std::option;
      use sui::coin;
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      /// The type identifier of coin. The coin will have a type
      /// tag of kind: `Coin<package_object::mycoin::MYCOIN>`
      /// Make sure that the name of the type matches the module's name.
      public struct MYCOIN has drop {}
      /// Module initializer is called once on module publish. A treasury
      /// cap is sent to the publisher, who then controls minting and burning
      fun init(witness: MYCOIN, ctx: &mut TxContext) {
        let (treasury, metadata) = coin::create_currency(witness, 6, b"MYCOIN", b"", b"", option::none(), ctx);
        transfer::public_freeze_object(metadata);
        transfer::public_share_object(treasury)
      }
      public fun mint(
      treasury_cap: &mut TreasuryCap<MYCOIN>, 
      amount: u64, 
      recipient: address, 
      ctx: &mut TxContext,
      ) {
        let coin = coin::mint(treasury_cap, amount, ctx);
        transfer::public_transfer(coin, recipient)
      }
    }
    
```

## [如何限制最大供应量](#如何限制最大供应量)

我们用SUI Token发行的例子来说说明

```

    
    
    // Copyright (c) Mysten Labs, Inc.
    // SPDX-License-Identifier: Apache-2.0
    /// Coin<SUI> is the token used to pay for gas in Sui.
    /// It has 9 decimals, and the smallest unit (10^-9) is called "mist".
    module sui::sui {
      use std::option;
      use sui::tx_context::{Self, TxContext};
      use sui::balance::{Self, Balance};
      use sui::transfer;
      use sui::coin;
      const EAlreadyMinted: u64 = 0;
      /// Sender is not @0x0 the system address.
      const ENotSystemAddress: u64 = 1;
      /// The total supply of Sui denominated in Mist (10 Billion * 10^9)
      const TOTAL_SUPPLY_MIST: u64 = 10_000_000_000_000_000_000;
      /// Name of the coin
      public struct SUI has drop {}
      /// Register the `SUI` Coin to acquire its `Supply`.
      /// This should be called only once during genesis creation.
      fun new(ctx: &mut TxContext): Balance<SUI> {
        assert!(tx_context::sender(ctx) == @0x0, ENotSystemAddress);
        assert!(tx_context::epoch(ctx) == 0, EAlreadyMinted);
        let (treasury, metadata) = coin::create_currency(
          SUI {},
          9,
          b"SUI",
          b"Sui",
          // TODO: add appropriate description and logo url
          b"",
          option::none(),
          ctx
        );
        transfer::public_freeze_object(metadata);
        let supply = coin::treasury_into_supply(treasury);
        let total_sui = balance::increase_supply(&mut supply, TOTAL_SUPPLY_MIST);
        balance::destroy_supply(supply);
        total_sui
      }
      
    }
    
```

## [如何按经济模型锁仓](#如何按经济模型锁仓)

  * 涉及到锁仓的操作一般都会吧Coin 转成 Balance 存储在合约里面

  * 涉及动态mint挖矿的一把都会把 TreasuryCap 转成 Supply 把 Supply 锁定在合约里面 并且做好限制

## [通用的锁仓例子](#通用的锁仓例子)

[ ](../framework/01.into.html "Previous chapter") [ ](../collections/index.html "Next chapter")

[ ](../framework/01.into.html "Previous chapter") [ ](../collections/index.html "Next chapter")

# index.html
# sui move book

[ ](../print.html "Print this book")

[ ](../framework/02.coin.html "Previous chapter") [ ](../collections/ch01.vectors.html "Next chapter")

[ ](../framework/02.coin.html "Previous chapter") [ ](../collections/ch01.vectors.html "Next chapter")

# ch01.vectors.html
# sui move book

[ ](../print.html "Print this book")

# [用 vector 管理集合](#用-vector-管理集合)

我们已经非常熟悉结构体类型了，它使我们能够创建自己的类型并存储复杂数据。但是有时我们需要动态、可扩展和可管理的功能。为此，Move 提供了向量 vector。

vector 是用于存储数据集合的内置类型。集合的数据可以是任何类型（但仅一种）。vector 功能实际上是由 VM 提供的，不是由 Move 语言提供的，使用它的唯一方法是使用标准库和 native 函数。

```

    
    
    script {
      use 0x1::vector;
      fun main() {
        // use generics to create an emtpy vector
        let a = vector::empty<&u8>();
        let i = 0;
        // let's fill it with data
        while (i < 10) {
          vector::push_back(&mut a, i);
          i = i + 1;
        }
        // now print vector length
        let a_len = vector::length(&a);
        0x1::debug::print<u64>(&a_len);
        // then remove 2 elements from it
        vector::pop_back(&mut a);
        vector::pop_back(&mut a);
        // and print length again
        let a_len = vector::length(&a);
        0x1::debug::print<u64>(&a_len);
      }
    }
    
```

vector 最多可以存储 18446744073709551615u64（u64最大值）个非引用类型的值。要了解它如何帮助我们管理大型数据，我们试着编写一个模块。

```

    
    
    module Shelf {
      use 0x1::vector;
      struct Box<T> {
        value: T
      }
      struct Shelf<T> {
        boxes: vector<Box<T>>
      }
      public fun create_box<T>(value: T): Box<T> {
        Box { value }
      }
      // this method will be inaccessible for non-copyable contents
      public fun value<T: copy>(box: &Box<T>): T {
        *&box.value
      }
      public fun create<T>(): Shelf<T> {
        Shelf {
          boxes: vector::empty<Box<T>>()
        }
      }
      // box value is moved to the vector
      public fun put<T>(shelf: &mut Shelf<T>, box: Box<T>) {
        vector::push_back<Box<T>>(&mut shelf.boxes, box);
      }
      public fun remove<T>(shelf: &mut Shelf<T>): Box<T> {
        vector::pop_back<Box<T>>(&mut shelf.boxes)
      }
      public fun size<T>(shelf: &Shelf<T>): u64 {
        vector::length<Box<T>>(&shelf.boxes)
      }
    }
    
```

我们将创建一个 Shelf，为其提供几个 Box，并观察如何在模块中使用 vector：

```

    
    
    script {
      use {{sender}}::Shelf;
      fun main() {
        // create shelf and 2 boxes of type u64
        let shelf = Shelf::create<u64>();
        let box_1 = Shelf::create_box<u64>(99);
        let box_2 = Shelf::create_box<u64>(999);
        // put both boxes to shelf
        Shelf::put(&mut shelf, box_1);
        Shelf::put(&mut shelf, box_2);
        // prints size - 2
        0x1::debug::print<u64>(&Shelf::size<u64>(&shelf));
        // then take one from shelf (last one pushed)
        let take_back = Shelf::remove(&mut shelf);
        let value   = Shelf::value<u64>(&take_back);
        // verify that the box we took back is one with 999
        assert(value == 999, 1);
        // and print size again - 1
        0x1::debug::print<u64>(&Shelf::size<u64>(&shelf));
      }
    }
    
```

向量非常强大，它使我们可以存储大量数据，并可以在索引的存储中使用它。

### [内联 vector 定义的十六进制数组和字符串](#内联-vector-定义的十六进制数组和字符串)

vector 也可以表示字符串。VM支持将`vector<u8>`作为参数传递给`main`脚本中的函数。

也可以使用十六进制字面值（literal）在脚本或模块中定义`vector<u8>`：

```

    
    
    script {
      use 0x1::vector;
      // this is the way to accept arguments in main
      fun main(name: vector<u8>) {
        let _ = name;
        // and this is how you use literals
        // this is a "hello world" string!
        let str = x"68656c6c6f20776f726c64";
        // hex literal gives you vector<u8> as well
        vector::length<u8>(&str);
      }
    }
    
```

更简单的方法是使用字符串字面值（literal）：

```

    
    
    script {
      fun main() {
        let _ = b"hello world";
      }
    }
    
```

它们被视为 ASCII 字符串，也被解释为 vector。

### [vector 速查表](#vector-速查表)

这是标准库中 vector 方法的简短列表：

  * 创建一个类型为`<E>`的空向量

```

    
    
    vector::empty<E>(): vector<E>;
    
```

  * 获取向量的长度

```

    
    
    vector::length<E>(v: &vector<E>): u64;
    
```

  * 将元素 e 添加到向量末尾

```

    
    
    vector::push_back<E>(v: &mut vector<E>, e: E);
    
```

  * 获取对向量元素的可变引用。不可变引用可使用`vector::borrow()`

```

    
    
    vector::borrow_mut<E>(v: &mut vector<E>, i: u64): &E;
    
```

  * 从向量的末尾取出一个元素

```

    
    
    vector::pop_back<E>(v: &mut vector<E>): E;
    
```

标准库中的 vector 模块：

[ ](../collections/index.html "Previous chapter") [ ](../collections/table.html "Next chapter")

[ ](../collections/index.html "Previous chapter") [ ](../collections/table.html "Next chapter")

# table.html
# sui move book

[ ](../print.html "Print this book")

## [Table是一个类似映射的集合，动态存储键值对。](#table是一个类似映射的集合动态存储键值对)

但与传统的地图集合不同，它的键和值不存储在Table值中，而是使用 Sui 的对象系统存储。该Table结构仅充当对象系统的句柄来检索这些键和值。

keya 的类型必须Table具有 的能力约束copy + drop + store，并且该value类型必须具有 的能力约束store。

Table也是一种同构集合类型，其中键和值字段可以指定或泛型类型，但集合中的所有值和所有键Table必须属于相同类型。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module collection::table {
      use sui::table::{Table, Self};
      use sui::tx_context::{TxContext};
      // Defining a table with specified types for the key and value
      struct IntegerTable {
        table_values: Table<u8, u8>
      }
      // Defining a table with generic types for the key and value 
      struct GenericTable<phantom K: copy + drop + store, phantom V: store> {
        table_values: Table<K, V>
      }
      // Create a new, empty GenericTable with key type K, and value type V
      public fun create<K: copy + drop + store, V: store>(ctx: &mut TxContext): GenericTable<K, V> {
        GenericTable<K, V> {
          table_values: table::new<K, V>(ctx)
        }
      }
      // Adds a key-value pair to GenericTable
      public fun add<K: copy + drop + store, V: store>(table: &mut GenericTable<K, V>, k: K, v: V) {
        table::add(&mut table.table_values, k, v);
      }
      /// Removes the key-value pair in the GenericTable `table: &mut Table<K, V>` and returns the value.  
      public fun remove<K: copy + drop + store, V: store>(table: &mut GenericTable<K, V>, k: K): V {
        table::remove(&mut table.table_values, k)
      }
      // Borrows an immutable reference to the value associated with the key in GenericTable
      public fun borrow<K: copy + drop + store, V: store>(table: &GenericTable<K, V>, k: K): &V {
        table::borrow(&table.table_values, k)
      }
      /// Borrows a mutable reference to the value associated with the key in GenericTable
      public fun borrow_mut<K: copy + drop + store, V: store>(table: &mut GenericTable<K, V>, k: K): &mut V {
        table::borrow_mut(&mut table.table_values, k)
      }
      /// Check if a value associated with the key exists in the GenericTable
      public fun contains<K: copy + drop + store, V: store>(table: &GenericTable<K, V>, k: K): bool {
        table::contains<K, V>(&table.table_values, k)
      }
      /// Returns the size of the GenericTable, the number of key-value pairs
      public fun length<K: copy + drop + store, V: store>(table: &GenericTable<K, V>): u64 {
        table::length(&table.table_values)
      }
    }
    }
```

```

[ ](../collections/ch01.vectors.html "Previous chapter") [ ](../collections/bag.html "Next chapter")

[ ](../collections/ch01.vectors.html "Previous chapter") [ ](../collections/bag.html "Next chapter")

# bag.html
# sui move book

[ ](../print.html "Print this book")

## [Bag Type](#bag-type)

Bag是一个异构的类似地图的集合。集合的类似之处Table在于它的键和值不存储在Bag值中，而是使用 Sui 的对象系统存储。该Bag结构仅充当对象系统的句柄来检索这些键和值。

## [常用Bag操作](#常用bag操作)

常用Bag操作的示例代码如下：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module collection::bag {
      use sui::bag::{Bag, Self};
      use sui::tx_context::{TxContext};
      // Defining a table with generic types for the key and value 
      public struct GenericBag {
        items: Bag
      }
      // Create a new, empty GenericBag
      public fun create(ctx: &mut TxContext): GenericBag {
        GenericBag{
          items: bag::new(ctx)
        }
      }
      // Adds a key-value pair to GenericBag
      public fun add<K: copy + drop + store, V: store>(bag: &mut GenericBag, k: K, v: V) {
        bag::add(&mut bag.items, k, v);
      }
      /// Removes the key-value pair from the GenericBag with the provided key and returns the value.  
      public fun remove<K: copy + drop + store, V: store>(bag: &mut GenericBag, k: K): V {
        bag::remove(&mut bag.items, k)
      }
      // Borrows an immutable reference to the value associated with the key in GenericBag
      public fun borrow<K: copy + drop + store, V: store>(bag: &GenericBag, k: K): &V {
        bag::borrow(&bag.items, k)
      }
      /// Borrows a mutable reference to the value associated with the key in GenericBag
      public fun borrow_mut<K: copy + drop + store, V: store>(bag: &mut GenericBag, k: K): &mut V {
        bag::borrow_mut(&mut bag.items, k)
      }
      /// Check if a value associated with the key exists in the GenericBag
      public fun contains<K: copy + drop + store>(bag: &GenericBag, k: K): bool {
        bag::contains<K>(&bag.items, k)
      }
      /// Returns the size of the GenericBag, the number of key-value pairs
      public fun length(bag: &GenericBag): u64 {
        bag::length(&bag.items)
      }
    }
    }
```

```

[ ](../collections/table.html "Previous chapter") [ ](../design-patterns/index.html "Next chapter")

[ ](../collections/table.html "Previous chapter") [ ](../design-patterns/index.html "Next chapter")

# index.html
# sui move book

[ ](../print.html "Print this book")

[ ](../collections/bag.html "Previous chapter") [ ](../design-patterns/index.html "Next chapter")

[ ](../collections/bag.html "Previous chapter") [ ](../design-patterns/index.html "Next chapter")

# index.html
# sui move book

[ ](../print.html "Print this book")

[ ](../design-patterns/index.html "Previous chapter") [ ](../nft/01.intro.html "Next chapter")

[ ](../design-patterns/index.html "Previous chapter") [ ](../nft/01.intro.html "Next chapter")

# 01.intro.html
# sui move book

[ ](../print.html "Print this book")

# [NFT 介绍](#nft-介绍)

NFT，全称为Non-Fungible Token，指非同质化通证，实质是区块链网络里具有唯一性特点的可信数字权益凭证，是一种可在区块链上记录和处理多维、复杂属性的数据对象

# [NFT 可以是什么](#nft-可以是什么)

NFT可以是任何数字化的东西：

声音、图像、一段文字、

一件游戏里的道具等等，

其应用范围取决于人们的想象力，

它的一大优势是可以把之前

不能变现的虚拟物品资产化

NFT可以用来代表一幅画、一首歌、一项专利、一段影片、一张照片，帮助每一个独一无二的事物进行“版权登记”

# [动态NFT](#动态nft)

什么是动态 NFT？

动态 NFT 是静态 NFT 的迭代。它可以根据数据的反馈，实时更改 NFT 的数据，而智能合约的逻辑也能够用来根据外部因素的刺激对 NFT 的属性进行微调。动态 NFT（dNFTs）的另一个有趣的定义是：

「有生命的真实资产。能够在外界的刺激下生长、变化、分解、再生。」

dNFT 接受什么样的数据呢？有两种：「链上数据」和「链下数据」。链上数据可以通过智能合约直接访问，链下数据则需要由名为「预言机（Oracle）」的实体验证后，再添加到区块链中。

# [ERC721](#erc721)

ERC721是以太坊上最早也是最基础的NFT底层协议标准，2017年9月提出，2018年制定。其作为一种非同质化代币智能合约标准接口，允许发行基于ERC721的NFT，它规定了NFT资产的最小单位为1、不可拆分且非同质化（独一无二）的特性，ERC721是目前NFT资产的主要规范标准与基础之一（并也为其他链上NFT标准的制定提供了参照），目前以太坊绝大多数NFT都是ERC721标准的

# [Sui NFT](#sui-nft)

得益于Sui Move的特性和其他链有很多区别，NFT 标准也没有和其他链一样

[ ](../nft/index.html "Previous chapter") [ ](../nft/02.display.html "Next chapter")

[ ](../nft/index.html "Previous chapter") [ ](../nft/02.display.html "Next chapter")

# 02.display.html
# sui move book

[ ](../print.html "Print this book")

# [Display Sui对象显示标准](#display-sui对象显示标准)

Sui 对象显示标准是一个模板引擎，允许对类型的链下表示（显示）进行链上管理。使用它，您可以将对象的数据替换为模板字符串。该标准不限制您可以设置的字段。您可以使用 {property}语法访问所有对象属性，然后将它们作为模板字符串的一部分插入。

使用Publisher您拥有的对象来设置sui::display类型。有关Publisher对象的更多信息，请参阅Sui Move 示例中的发布者主题。

在 Sui Move 中，Display表示一个为类型指定一组命名模板的对象T。例如，对于某种类型，0x2::capy::Capy显示语法为： Display<0x2::capy::Capy>。

TSui Full节点通过匹配定义来处理该类型的所有对象，并在使用查询中的设置Display查询对象时返回处理结果。{ showDisplay: true }

# [显示属性](#显示属性)

建议的基本属性集包括：

  * name- 对象的名称。当用户查看对象时，会显示该名称。
  * description- 对象的描述。当用户查看对象时，会显示描述。
  * link- 指向要在应用程序中使用的对象的链接。
  * image_url- 包含对象图像的 URL 或 blob。
  * thumbnail_url-较小图像的 URL，可在钱包、浏览器和其他产品中用作预览。
  * project_url- 指向与对象或创建者相关的网站的链接。
  * creator- 指示对象创建者的字符串。

# [Sui Hero 模块示例](#sui-hero-模块示例)

以下代码示例演示了Display示例Hero模块如何根据类型的name、id和属性而变化。以下表示该函数定义的模板：image_urlHeroinit

```

    
    
    {
    "name": "{name}",
    "link": "https://sui-heroes.io/hero/{id}",
    "image_url": "ipfs://{img_url}",
    "description": "A true Hero of the Sui ecosystem!",
    "project_url": "https://sui-heroes.io",
    "creator": "Unknown Sui Fan"
    }
    
```

```

    
    
    /// Example of an unlimited "Sui Hero" collection - anyone can
    /// mint their Hero. Shows how to initialize the `Publisher` and how
    /// to use it to get the `Display<Hero>` object - a way to describe a
    /// type for the ecosystem.
    module examples::my_hero {
      use sui::tx_context::{sender, TxContext};
      use std::string::{utf8, String};
      use sui::transfer::transfer;
      use sui::object::{Self, UID};
      // The creator bundle: these two packages often go together.
      use sui::package;
      use sui::display;
      /// The Hero - an outstanding collection of digital art.
      public struct Hero has key, store {
        id: UID,
        name: String,
        image_url: String,
      }
      /// One-Time-Witness for the module.
      public struct MY_HERO has drop {}
      /// In the module initializer one claims the `Publisher` object
      /// to then create a `Display`. The `Display` is initialized with
      /// a set of fields (but can be modified later) and published via
      /// the `update_version` call.
      ///
      /// Keys and values are set in the initializer but could also be
      /// set after publishing if a `Publisher` object was created.
      fun init(otw: MY_HERO, ctx: &mut TxContext) {
        let keys = vector[
          utf8(b"name"),
          utf8(b"link"),
          utf8(b"image_url"),
          utf8(b"description"),
          utf8(b"project_url"),
          utf8(b"creator"),
        ];
        let values = vector[
          // For `name` one can use the `Hero.name` property
          utf8(b"{name}"),
          // For `link` one can build a URL using an `id` property
          utf8(b"https://sui-heroes.io/hero/{id}"),
          // For `image_url` use an IPFS template + `image_url` property.
          utf8(b"ipfs://{image_url}"),
          // Description is static for all `Hero` objects.
          utf8(b"A true Hero of the Sui ecosystem!"),
          // Project URL is usually static
          utf8(b"https://sui-heroes.io"),
          // Creator field can be any
          utf8(b"Unknown Sui Fan")
        ];
        // Claim the `Publisher` for the package!
        let publisher = package::claim(otw, ctx);
        // Get a new `Display` object for the `Hero` type.
        let display = display::new_with_fields<Hero>(
          &publisher, keys, values, ctx
        );
        // Commit first version of `Display` to apply changes.
        display::update_version(&mut display);
        transfer(publisher, sender(ctx));
        transfer(display, sender(ctx));
      }
      /// Anyone can mint their `Hero`!
      public fun mint(name: String, image_url: String, ctx: &mut TxContext): Hero {
        let id = object::new(ctx);
        Hero { id, name, image_url }
      }
    }
    
```

# [使用对象显示](#使用对象显示)

该调用在自定义函数或模块初始值设定项中display::new创建一个，或者作为可编程事务的一部分。Display以下代码示例演示了如何创建Display：

```

    
    
    module sui::display {
    /// Get a new Display object for the `T`.
    /// Publisher must be the publisher of the T, `from_package`
    /// check is performed.
      public fun new<T>(pub: &Publisher): Display<T> { /* ... */ }
    }
    
```

创建后Display，您可以对其进行修改。以下代码示例演示了如何修改Display：

```

    
    
    module sui::display {
    /// Sets multiple fields at once
      public fun add_multiple(
      self: &mut Display,
      keys: vector<String>,
      values: vector<String>
      ) { /* ... */ }
      /// Edit a single field
      public fun edit(self: &mut Display, key: String, value: String) { /* ... */ }
      /// Remove a key from Display
      public fun remove(self: &mut Display, key: String ) { /* ... */ }
    }
    
```

接下来，update_version调用应用更改并通过发出事件Display来设置 。T完整节点接收事件并使用事件中的数据来检索该类型的模板。

以下代码示例演示了如何使用该update_version调用：

```

    
    
    module sui::display {
    /// Update the version of Display and emit an event
      public fun update_version(self: &mut Display) { /* ... */ }
    }
    
```

# [Sui实用对象](#sui实用对象)

在 Sui 中，实用程序对象启用功能授权。几乎所有模块都具有只有具有所需功能才能访问的功能。通用模块允许每个应用程序具有一种功能，例如市场。某些功能标记链上共享对象的所有权，或从另一个帐户访问共享数据。对于功能，提供有意义的对象描述以促进用户界面实现非常重要。这有助于避免当对象相似时意外传输错误的对象。它还为用户看到的项目提供用户友好的描述。

以下示例演示了如何创建 capy 功能：

```

    
    
    module capy::utility {
    /// A capability which grants Capy Manager permission to add
    /// new genes and manage the Capy Market
      struct CapyManagerCap has key, store {
        id: UID 
      }
    }
    
```

# [具有数据重复的典型对象](#具有数据重复的典型对象)

游戏内物品的常见情况是有大量相似的对象按某些标准分组。优化它们的大小以及铸造和更新它们的成本非常重要。通常，游戏对每个组或项目标准使用单个源图像或 URL。将源图像存储在每个对象内并不是最佳选择。在某些情况下，当游戏允许或购买游戏内物品时，用户会铸造游戏内物品。为此，必须提前创建并存储一些 IPFS/Arweave 元数据。这需要额外的逻辑，这些逻辑通常与物品的游戏内属性无关。

以下示例演示了如何创建 Capy：

```

    
    
    module capy::capy_items {
    /// A wearable Capy item. For some items there can be an
    /// unlimited supply. And items with the same name are identical.
      public struct CapyItem has key, store {
        id: UID,
        name: String
      }
    }
    
```

具有动态表示的独特对象# Sui Capys 使用动态图像生成。当卡比出生时，其属性决定了卡比的外观，例如颜色或图案。当用户将物品放在 Capy 上时，Capy 的外观会发生变化。当用户将多个物品放在 Capy 上时，物品组合就有机会获得奖励。

为了实现这一点，Capys 游戏 API 服务会刷新图像以响应用户发起的更改。Capy 的 URL 是一个带有capy.id. 但存储完整的 URL——以及 Capy 对象中的其他字段（由于其人口的多样性）——也会导致用户支付过多的存储费用并增加汽油费。

下面的例子演示了如何实现动态图像生成：

```

    
    
    module capy::capy {
    /// A Capy - very diverse object with different combination
    /// of genes. Created dynamically + for images a dynamic SVG
    /// generation is used.
      public struct Capy has key, store {
        id: UID,
        genes: vector<u8>
      }
    }
    
```

具有独特静态内容的对象# 这是最简单的场景——一个对象代表一切本身。将元数据标准应用于此类对象非常容易，特别是当该对象永远保持不可变时。但是，如果元数据标准不断发展，并且某些生态系统项目为某些属性添加了新功能，则该对象始终保持其原始形式，并且可能需要向后兼容的更改。

```

    
    
    module sui::devnet_nft {
    /// A Collectible with a static data. URL, name, description are
    /// set only once on a mint event
      public struct DevNetNFT has key, store {
        id: UID,
        name: String,
        description: String,
        url: Url,
      }
    }
    
```

[ ](../nft/01.intro.html "Previous chapter") [ ](../nft/03.publisher.html "Next chapter")

[ ](../nft/01.intro.html "Previous chapter") [ ](../nft/03.publisher.html "Next chapter")

# 03.publisher.html
# sui move book

[ ](../print.html "Print this book")

[ ](../nft/02.display.html "Previous chapter") [ ](../nft/04.pub_nft.html "Next chapter")

[ ](../nft/02.display.html "Previous chapter") [ ](../nft/04.pub_nft.html "Next chapter")

# 04.pub_nft.html
# sui move book

[ ](../print.html "Print this book")

[ ](../nft/03.publisher.html "Previous chapter") [ ](../nft/05.kiosk.html "Next chapter")

[ ](../nft/03.publisher.html "Previous chapter") [ ](../nft/05.kiosk.html "Next chapter")

# 05.kiosk.html
# sui move book

[ ](../print.html "Print this book")

# [Sui kiosk](#sui-kiosk)

Kiosk 是 Sui 上商业应用程序的去中心化系统。它由“信息亭”组成 - 由各方拥有的共享对象，用于存储资产并允许将其列出以供出售以及利用自定义交易功能 - 例如拍卖。在高度去中心化的同时，Kiosk 提供了一系列强有力的保证：

信息亭所有者保留其资产的所有权直至购买之时； 创建者设置自定义“策略” - 适用于每笔交易的规则集（例如支付版税、执行某些任意操作 X）； 市场可以对 Kiosk 发出的事件进行索引，并订阅单个源以进行链上资产交易； 实际上，Kiosk 是 Sui 框架的一部分，它是系统原生的，并且“开箱即用”地可供每个人使用。

# [Sui 亭所有者](#sui-亭所有者)

任何人都可以创建 Sui Kiosk。信息亭的所有权由 的所有者确定KioskOwnerCap，这是一个授予对单个信息亭的完全访问权限的特殊对象。作为所有者，您可以出售具有共享TransferPolicy可用的类型 (T) 的任何资产，或者即使没有共享策略，您也可以使用信息亭来存储资产。您无法从自助终端出售或转让任何没有相关转让政策的资产。要出售商品，如果存在类型 (T) 的现有转让政策，您只需将资产添加到您的信息亭，然后列出它们即可。您在列出商品时指定报价金额。然后，任何人都可以按照列表中指定的 SUI 金额购买该商品。关联的转让政策决定买方可以对所购买的资产执行哪些操作。信息亭所有者可以：

放置和取出物品 列出待售物品 添加和删​​除扩展 从销售中提取利润 借用和改变拥有的资产 使用全套交易工具，例如拍卖、彩票和收藏竞价 买家 Sui Kiosk # 买家是从信息亭购买（或更一般地说，接收）物品的一方，网络上的任何人都可以是买家（例如，同时也是信息亭所有者）。** 好处：**

买家可以获得全球流动性并可以获得最好的报价 买家可以通过他们的信息亭对藏品进行投标 买家在信息亭中执行的大部分操作都是免费的（无需gas） 职责：

买方是支付政策中规定的费用的一方 买家必须遵守创作者设定的规则，否则交易将不会成功 保证：

当使用自定义交易逻辑（例如拍卖）时，保证物品在交易完成之前保持不变

# [适用于市场的 Sui Kiosk](#适用于市场的-sui-kiosk)

作为市场运营商，您可以实施 Sui Kiosk 来监视一系列信息亭中提供的报价并将其显示在市场网站上。您还可以使用 Kiosk 扩展（由社区或第三方创建）来实现自定义系统。例如，市场可以使用 aTransferPolicyCap来实现特定于应用程序的传输规则。

# [为创作者提供的 Sui Kiosk](#为创作者提供的-sui-kiosk)

作为创建者，Sui Kiosk 支持严格执行转让政策和相关规则，以保护资产并强制执行资产所有权。Sui Kiosk 使创作者能够更好地控制自己的创作，并让创作者和所有者控制其作品的使用方式。

Creator 是创建并控制单一类型的 TransferPolicy 的一方。例如，SuiFren 的作者是 SuiFren 的创建者在 Kiosk 生态系统中键入并充当创建者。创作者制定了政策，但他们也可能是通过信息亭出售其资产的第一批卖家。

创作者可以：

设定交易规则 设置规则的多种方式（“轨道”） 通过政策随时启用或禁用交易 对所有交易执行政策（例如特许权使用费） 通过信息亭进行资产的主要出售 上述所有内容立即在全球范围内生效。

创作者不能：

获取或修改存储在其他人的信息亭中的项目 如果政策中未设置“锁定”规则，则限制从信息亭获取物品

# [Sui Kiosk 保证](#sui-kiosk-保证)

Sui Kiosk 提供了 Sui 通过智能合约执行的一系列保证。这些保证包括：

Sui Kiosk中的每笔交易都需要一个TransferPolicy决议。这使创作者可以控制其资产的交易方式。 真正的所有权，这意味着只有信息亭所有者才能获取、列出、借用或修改添加到其信息亭的资产。这类似于 Sui 上单所有者对象的工作方式。 强大的策略执行，例如版税策略，允许创建者随时启用或禁用适用于平台上附加该策略的对象的所有交易的策略。 对 a 的更改会TransferPolicy立即全局应用。 实际上，这些保证意味着：

当您列出待售商品时，任何人都无法修改它或从售货亭拿走它。 当您定义 时PurchaseCap，项目将保持锁定状态，除非交易使用或返回 ，否则您无法修改该项目或从信息亭获取该项目PurchaseCap。 您可以随时删除任何规则（作为所有者）。 您可以随时禁用任何扩展（作为所有者）。 扩展状态的状态始终可供扩展访问。

# [Sui kiosk 中的资产状态](#sui-kiosk-中的资产状态)

Sui Kiosk 是一个共享对象，可以存储异构值，例如不同组的资产收藏品。当您将资产添加到自助服务终端时，它会处于以下状态之一：

已放置 - 使用该功能放置在信息亭中的物品kiosk::place。信息亭所有者可以提取并直接使用它、借用它（可变或不可变），或者列出待售物品。 锁定 - 使用该功能放置在信息亭中的物品kiosk::lock。您无法从自助服务终端提取锁定的物品，但您可以可变地借用它并将其挂牌出售。放置在具有关联 Kiosk 锁定策略的 Kiosk 中的任何项目都具有“锁定”状态。 kiosk::list已列出 - 信息亭中使用或功能列出待售的商品kiosk::place_and_list。您无法在列出时修改项目，但可以一成不变地借用它或将其除名，这会将其返回到之前的状态。 独家列出 - 由调用该kiosk::list_with_purchase_cap功能的扩展程序放置或锁定在信息亭中的项目。只有信息亭所有者才能批准调用该功能。所有者只能一成不变地借用它。该扩展必须提供删除/解锁资产的功能，否则它可能会永远保持锁定状态。鉴于此操作是由所有者明确执行的 - 所有者有责任选择使用经过验证和审核的扩展。 当有人从信息亭购买资产时，该资产离开信息亭，所有权转移到买方的地址。

# [打开 Sui Kiosk](#打开-sui-kiosk)

要使用 Sui Kiosk，您必须创建一个 Sui Kiosk 并拥有与该对象KioskOwnerCap匹配的 Sui Kiosk Kiosk。您可以通过调用该函数使用单个事务创建新的信息亭kiosk::default。该函数创建并共享一个Kiosk，并将 传输KioskOwnerCap到您的地址。

[ ](../nft/04.pub_nft.html "Previous chapter") [ ](../nft/06.kiosk-sdk.html "Next chapter")

[ ](../nft/04.pub_nft.html "Previous chapter") [ ](../nft/06.kiosk-sdk.html "Next chapter")

# 06.kiosk-sdk.html
# sui move book

[ ](../print.html "Print this book")

# [使用 Kiosk SDK 创建 Sui Kiosk](#使用-kiosk-sdk-创建-sui-kiosk)

```

    
    
    import { createKioskAndShare } from '@mysten/kiosk';
    let tx = new TransactionBuilder();
    let kioskOwnerCap = createKioskAndShare(tx);
    tx.transferObjects([ kioskOwnerCap ], tx.pure(sender, 'address'));
    
```

# [使用可编程事务块创建 Kiosk](#使用可编程事务块创建-kiosk)

```

    
    
    let tx = new TransactionBuilder();
    tx.moveCall({
    target: '0x2::kiosk::default'
    });
    
```

# [使用 Sui CLI 创建信息亭](#使用-sui-cli-创建信息亭)

```

    
    
    sui client call \
    --package 0x2 \
    --module kiosk \
    --function default \
    --gas-budget 1000000000
    
```

# [创建具有高级选项的信息亭](#创建具有高级选项的信息亭)

对于更高级的用例，当您想要选择存储模型或立即执行操作时，可以使用可编程事务块（PTB）友好功能 kiosk::new。Kiosk 旨在共享。如果您选择不同的存储模式（例如自有存储模式），您的信息亭可能无法按预期运行或无法被其他用户访问。您可以通过在 Sui Testnet 上进行测试来确保您的 Kiosk 正常运行。

# [使用 Kiosk SDK 创建具有高级选项的 Kiosk](#使用-kiosk-sdk-创建具有高级选项的-kiosk)

```

    
    
    import { createKiosk } from '@mysten/kiosk';
    let tx = new TransactionBuilder();
    let [kiosk, kioskOwnerCap] = createKiosk(tx);
    tx.transferObjects([ kioskOwnerCap ], tx.pure(sender, 'address'));
    tx.moveCall({
    target: '0x2::transfer::public_share_object',
    arguments: [ kiosk ],
    typeArguments: '0x2::kiosk::Kiosk'
    })
    
```

# [使用可编程交易块创建具有高级选项的信息亭](#使用可编程交易块创建具有高级选项的信息亭)

```

    
    
    let tx = new TransactionBuilder();
    let [kiosk, kioskOwnerCap] = tx.moveCall({
    target: '0x2::kiosk::new'
    });
    tx.transferObjects([ kioskOwnerCap ], tx.pure(sender, 'address'));
    tx.moveCall({
    target: '0x2::transfer::public_share_object',
    arguments: [ kiosk ],
    typeArguments: '0x2::kiosk::Kiosk'
    })
    
```

# [使用 SUI CLI 创建具有高级选项的 Kiosk](#使用-sui-cli-创建具有高级选项的-kiosk)

Sui CLI 尚不支持 PTB 和事务链。您可以改用 kiosk::default 函数。

将物品放入您的信息亭或从您的信息亭取出物品# 作为 Kiosk 所有者，您可以将任何资产放入您的 Sui Kiosk 中。您可以从您的售货亭中取出当前未列出的任何商品。您可以在自助服务终端中放置哪些资产没有限制。但是，您不一定会列出和交易您放置在自助服务终端中的所有商品。TransferPolicy与物品类型相关的信息决定了您是否可以交易它。要了解更多信息，请参阅从信息亭购买商品部分。

将商品放入您的信息亭# 要将项目放置到信息亭，所有者需要调用sui::kiosk::place该对象的函数Kiosk并将KioskOwnerCap和 作为Item参数传递。

ITEM_TYPE以下示例中表示项目的完整类型。

# [使用 Kiosk SDK 放置项目](#使用-kiosk-sdk-放置项目)

```

    
    
    import { place } from '@mysten/kiosk';
    let tx = new TransactionBuilder();
    let itemArg = tx.object('<ID>');
    let kioskArg = tx.object('<ID>');
    let kioskOwnerCapArg = tx.object('<ID>');
    place(tx, '<ITEM_TYPE>', kioskArg, kioskOwnerCapArg, item);
    
```

# [使用可编程交易块放置物品](#使用可编程交易块放置物品)

```

    
    
    let tx = new TransactionBuilder();
    let itemArg = tx.object('<ID>');
    let kioskArg = tx.object('<ID>');
    let kioskOwnerCapArg = tx.object('<ID>');
    tx.moveCall({
    target: '0x2::kiosk::place',
    arguments: [ kioskArg, kioskOwnerCapArg, itemArg ],
    typeArguments: [ '<ITEM_TYPE>' ]
    
```

})

# [使用 Sui CLI 放置项目](#使用-sui-cli-放置项目)

```

    
    
    sui client call \
    --package 0x2 \
    --module kiosk \
    --function place \
    --args "<KIOSK_ID>" "<CAP_ID>" "<ITEM_ID>" \
    --type-args "<ITEM_TYPE>" \
    --gas-budget 1000000000
    
```

# [从售货亭拿走物品](#从售货亭拿走物品)

要从售货亭获取商品，您必须是售货亭所有者。作为所有者，调用对象sui::kiosk::take的函数Kiosk，并将项目的KioskOwnerCap和作为参数传递。ID

ITEM_TYPE以下示例中表示项目的完整类型。

# [使用 Kiosk SDK 从 Kiosk 获取商品](#使用-kiosk-sdk-从-kiosk-获取商品)

```

    
    
    import { take } from '@mysten/kiosk';
    let tx = new TransactionBuilder();
    let itemId = tx.pure('<ITEM_ID>', 'address');
    let kioskArg = tx.object('<ID>');
    let kioskOwnerCapArg = tx.object('<ID>');
    let item = take('<ITEM_TYPE>', kioskArg, kioskOwnerCapArg, itemId);
    tx.transferObjects([ item ], tx.pure(sender, 'address'));
    
```

# [使用可编程交易块从售货亭获取商品](#使用可编程交易块从售货亭获取商品)

```

    
    
    let tx = new TransactionBuilder();
    let itemId = tx.pure('<ITEM_ID>', 'address');
    let kioskArg = tx.object('<ID>');
    let kioskOwnerCapArg = tx.object('<ID>');
    let item = tx.moveCall({
    target: '0x2::kiosk::take',
    arguments: [ kioskArg, kioskOwnerCapArg, itemId ],
    typeArguments: [ '<ITEM_TYPE>' ]
    });
    
```

# [使用 Sui CLI 从自助服务终端获取商品](#使用-sui-cli-从自助服务终端获取商品)

该kiosk::take函数被构建为 PTB 友好并返回资产。Sui CLI 尚不支持事务链。

# [将物品锁定在信息亭中](#将物品锁定在信息亭中)

一些政策要求资产永远不会从信息亭中移走，例如为了严格的特许权使用费执行。为了支持这一点，Sui Kiosk 提供了锁定机制。锁定与放置类似，不同之处在于您无法将锁定的资产带出 Kiosk。

要将资产锁定在信息亭中，请调用该sui::kiosk::lock函数。为了确保您稍后可以解锁资产，您必须将 TransferPolicy 与资产关联。

# [锁定信息亭中的物品](#锁定信息亭中的物品)

当您使用 lock 时function，与使用该函数类似place，您可以将KioskOwnerCap和指定Item为参数。但要锁定该项目，您还必须显示 TransferPolicy。

<ITEM_TYPE>以下示例中代表资产的完整类型。

# [使用 Kiosk SDK 锁定项目](#使用-kiosk-sdk-锁定项目)

```

    
    
    import { lock } from '@mysten/kiosk';
    const tx = new TransactionBuilder();
    let kioskArg = tx.object('<ID>');
    let kioskOwnerCapArg = tx.object('<ID>');
    let itemArg = tx.object('<ID>');
    let transferPolicyArg = tx.object('<ID>');
    lock(tx, '<ITEM_TYPE>', kioskArg, kioskOwnerCapArg, transferPolicyArg, itemArg);
    
```

# [使用可编程交易块锁定项目](#使用可编程交易块锁定项目)

```

    
    
    const tx = new TransactionBuilder();
    let kioskArg = tx.object('<ID>');
    let kioskOwnerCapArg = tx.object('<ID>');
    let itemArg = tx.object('<ID>');
    let transferPolicyArg = tx.object('<ID>');
    tx.moveCall({
    target: '0x2::kiosk::lock',
    arguments: [ kioskArg, kioskOwnerCapArg, transferPolicyArg, itemArg ],
    typeArguments: [ '<ITEM_TYPE>' ]
    });
    
```

# [使用 Sui CLI 锁定项目](#使用-sui-cli-锁定项目)

```

    
    
    sui client call \
    --package 0x2 \
    --module kiosk \
    --function lock \
    --args "<KIOSK_ID>" "<CAP_ID>" "<TRANSFER_POLICY_ID>" "<ITEM_ID>" \
    --type-args "<ITEM_TYPE>" \
    --gas-budget 1000000000
    
```

# [从信息亭列出和删除商品](#从信息亭列出和删除商品)

Sui Kiosk 提供基本的交易功能。作为信息亭所有者，您可以列出待售资产，买家可以发现并购买它们。Sui Kiosk 默认支持列出项目，具有三个主要功能：

kiosk::list- 以固定价格列出待售资产 kiosk::delist- 删除现有列表 kiosk::purchase- 购买待售资产 网络上的任何人都可以购买 Sui Kiosk 列出的商品。要了解有关购买流程的更多信息，请参阅购买部分。要了解有关资产状态以及可以对列出的项目执行哪些操作的更多信息，请参阅资产状态部分。

# [列出信息亭中的商品](#列出信息亭中的商品)

作为信息亭所有者，您可以使用该kiosk::list功能列出您添加到信息亭的任何资产。包括要销售的商品和标价作为参数。Sui 上的所有列表均采用 SUI 代币。当您列出商品时，Sui 会发出一个kiosk::ItemListed事件，其中包含 Kiosk ID、商品 ID、商品类型和标价。

# [使用 Kiosk SDK 列出项目](#使用-kiosk-sdk-列出项目)

```

    
    
    import { list } from '@mysten/kiosk';
    let tx = new TransactionBlock();
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    let itemId = tx.pure('<ID>', 'address');
    let itemType = 'ITEM_TYPE';
    let price = '<price>'; // in MIST (1 SUI = 10^9 MIST)
    list(tx, itemType, kioskArg, capArg, itemId, price);
    
```

# [使用可编程事务块列出项目](#使用可编程事务块列出项目)

```

    
    
    let tx = new TransactionBlock();
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    let itemId = tx.pure('<ID>', 'address');
    let itemType = 'ITEM_TYPE';
    let priceArg = tx.pure('<price>', 'u64'); // in MIST (1 SUI = 10^9 MIST)
    tx.moveCall({
    target: '0x2::kiosk::list',
    arguments: [ kioskArg, capArg, itemId, priceArg ],
    typeArguments: [ itemType ]
    });
    
```

# [使用 Sui CLI 列出项目](#使用-sui-cli-列出项目)

```

    
    
    sui client call \
    --package 0x2 \
    --module kiosk \
    --function list \
    --args "<KIOSK_ID>" "<CAP_ID>" "<ITEM_ID>" "<PRICE>" \
    --type-args "ITEM_TYPE" \
    --gas-budget 1000000000
    
```

# [删除项目](#删除项目)

作为信息亭所有者，您可以使用kiosk::delist来删除当前列出的任何资产。指定要除名的项目作为参数。

当您下架某个商品时，Sui 会将列出该商品所收取的汽油费返还给信息亭所有者。

当您删除项目时，Sui 会发出一个kiosk::ItemDelisted事件，其中包含 Kiosk ID、项目 ID 和项目类型。

# [使用 Kiosk SDK 删除商品](#使用-kiosk-sdk-删除商品)

```

    
    
    import { delist } from '@mysten/kiosk';
    let tx = new TransactionBlock();
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    let itemId = tx.pure('<ID>', 'address');
    let itemType = 'ITEM_TYPE';
    delist(tx, itemType, kioskArg, capArg, itemId);
    
```

# [使用可编程交易块删除项目](#使用可编程交易块删除项目)

```

    
    
    let tx = new TransactionBlock();
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    let itemId = tx.pure('<ID>', 'address');
    let itemType = 'ITEM_TYPE';
    tx.moveCall({
    target: '0x2::kiosk::delist',
    arguments: [ kioskArg, capArg, itemId ],
    typeArguments: [ itemType ]
    });
    
```

# [使用 Sui CLI 删除项目](#使用-sui-cli-删除项目)

```

    
    
    sui client call \
    --package 0x2 \
    --module kiosk \
    --function delist \
    --args "<KIOSK_ID>" "<CAP_ID>" "<ITEM_ID>" \
    --type-args "ITEM_TYPE" \
    --gas-budget 1000000000
    
```

# [从售货亭购买商品](#从售货亭购买商品)

任何在 Sui 网络上有地址的人都可以购买 Sui Kiosk 列出的商品。要购买商品，您可以使用该kiosk::purchase功能。指定要购买的商品并支付信息亭所有者设定的标价。

您可以通过事件发现网络上列出的项目kiosk::ItemListed。

当您使用该kiosk::purchase函数时，它会返回购买的资产以及TransferRequest与该资产关联的类型。要完成购买，您必须满足应用于资产的 TransferPolicy 中定义的条款。要了解更多信息，请参阅 TransferPolicy 主题。

# [从售货亭借物品](#从售货亭借物品)

作为信息亭所有者，您可以访问放置或锁定在信息亭中的资产，而无需从信息亭中取出资产。您始终可以一成不变地借用资产。是否可以可变地借用资产取决于资产的状态。例如，您无法借用列出的资产，因为您无法在列出时对其进行修改。可用的功能包括：

  * kiosk::borrow- 返回对资产的不可变引用
  * kiosk::borrow_mut- 返回对资产的可变引用
  * kiosk::borrow_val- PTB 友好版本borrow_mut，允许您获取资产并将其放回同一交易中。

# [不可变的借用](#不可变的借用)

您始终可以从自助服务终端一成不变地借用资产。您可以使用该 kiosk::borrow功能借用资产，但是无法在可编程交易块中使用引用。要访问资产，您必须使用已发布的模块（函数）。

# [使用 Sui Move 永久借用资产](#使用-sui-move-永久借用资产)

```

    
    
    module examples::immutable_borrow
    use sui::object::ID;
    use sui::kiosk::{Self, Kiosk, KioskOwnerCap};
      public fun immutable_borrow_example<T>(self: &Kiosk, cap: &KioskOwnerCap, item_id: ID): &T {
        kiosk::borrow(self, cap, item_id)
      }
    }
    
```

# [使用borrow_mut进行可变借用](#使用borrow_mut进行可变借用)

如果资产未列出，您可以从自助服务终端可变地借用该资产。您可以使用该kiosk::borrow_mut函数可变地借用资产。但是，无法在 PTB 内使用引用，因此要访问可变借用的资产，您必须使用已发布的模块（函数）。

# [使用 Sui Move 可变借用资产](#使用-sui-move-可变借用资产)

```

    
    
    module examples::mutable_borrow
    use sui::object::ID;
    use sui::kiosk::{Self, Kiosk, KioskOwnerCap};
      public fun mutable_borrow_example<T>(
        self: &mut Kiosk, cap: &KioskOwnerCap, item_id: ID
      ): &mut T {
        kiosk::borrow_mut(self, cap, item_id)
      }
    }
    
```

# [可变借用borrow_val](#可变借用borrow_val)

您可以使用 PTB 友好的 kiosk::borrow_val 函数。它允许您取出资产并将其放回同一交易中。为了确保资产被放回自助服务终端，该功能会向呼叫者“强制”“烫手山芋”。

borrow_val使用 Kiosk SDK进行可变借用# Kiosk SDK 提供了一个带有借用逻辑的函数，您可以在 PTB 中使用：（borrowValue和returnValue）。

```

    
    
    import { borrowValue, returnValue } from '@sui/kiosk-sdk';
    let tx = new TransactionBuilder();
    let itemType = 'ITEM_TYPE';
    let itemId = tx.pure('<ITEM_ID>', 'address');
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    let [item, promise] = borrowValue(tx, itemType, kioskArg, capArg, itemId);
    // freely mutate or reference the `item`
    // any calls are available as long as they take a reference
    // `returnValue` must be explicitly called
    returnValue(tx, itemType, kioskArg, item, promise);
    
```

# [borrow_val使用可编程事务块进行可变借用](#borrow_val使用可编程事务块进行可变借用)

```

    
    
    let tx = new TransactionBuilder();
    let itemType = 'ITEM_TYPE';
    let itemId = tx.pure('<ITEM_ID>', 'address');
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    let [item, promise] = tx.moveCall({
    target: '0x2::kiosk::borrow_val',
    arguments: [ kioskArg, capArg, itemId ],
    typeArguments: [ itemType ],
    });
    // freely mutate or reference the `item`
    // any calls are available as long as they take a reference
    // `returnValue` must be explicitly called
    tx.moveCall({
    target: '0x2::kiosk::return_val',
    arguments: [ kioskArg, item, promise ],
    typeArguments: [ itemType ],
    });
    
```

# [从已完成的销售中提取收益](#从已完成的销售中提取收益)

当有人购买商品时，Sui 将销售收益存储在信息亭中。作为自助终端所有者，您可以随时通过调用该kiosk::withdraw功能提取收益。该功能很简单，但由于它是 PTB 友好的，因此目前在 Sui CLI 中不支持。

# [使用 Kiosk SDK 提取收益](#使用-kiosk-sdk-提取收益)

```

    
    
    import { withdrawFromKiosk } from '@mysten/kiosk';
    let tx = new TransactionBlock();
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    // The amount can be `null` to withdraw everything or a specific amount
    let amount = '<amount>';
    let withdrawAll = null;
    let coin = withdrawFromKiosk(tx, kioskArg, capArg, amount);
    
```

# [使用可编程交易块提取收益](#使用可编程交易块提取收益)

```

    
    
    let tx = new TransactionBlock();
    let kioskArg = tx.object('<ID>');
    let capArg = tx.object('<ID>');
    // because the function uses an Option<u64> argument,
    // constructing is a bit more complex
    let amountArg = tx.moveCall({
    target: '0x1::option::some',
    arguments: [ tx.pure('<amount>', 'u64') ],
    typeArguments: [ 'u64' ],
    });
    // alternatively
    let withdrawAllArg = tx.moveCall({
    target: '0x1::option::none',
    typeArguments: [ 'u64' ],
    });
    let coin = tx.moveCall({
    target: '0x2::kiosk::withdraw',
    arguments: [ kioskArg, capArg, amountArg ],
    typeArguments: [ 'u64' ],
    });
    
```

[ ](../nft/05.kiosk.html "Previous chapter") [ ](../nft/07.kiosk-rule.html "Next chapter")

[ ](../nft/05.kiosk.html "Previous chapter") [ ](../nft/07.kiosk-rule.html "Next chapter")

# 07.kiosk-rule.html
# sui move book

[ ](../print.html "Print this book")

# [](#)

[ ](../nft/06.kiosk-sdk.html "Previous chapter") [ ](../nft/08.market.html "Next chapter")

[ ](../nft/06.kiosk-sdk.html "Previous chapter") [ ](../nft/08.market.html "Next chapter")

# 08.market.html
# sui move book

[ ](../print.html "Print this book")

[ ](../nft/07.kiosk-rule.html "Previous chapter") [ ](../lsd/index.html "Next chapter")

[ ](../nft/07.kiosk-rule.html "Previous chapter") [ ](../lsd/index.html "Next chapter")

# index.html
# sui move book

[ ](../print.html "Print this book")

# [Sui 经济 模型和 LSD 介绍](#sui-经济-模型和-lsd-介绍)

[ ](../nft/08.market.html "Previous chapter") [ ](../lsd/01.intor.html "Next chapter")

[ ](../nft/08.market.html "Previous chapter") [ ](../lsd/01.intor.html "Next chapter")

# 01.intor.html
# sui move book

[ ](../print.html "Print this book")

# [什么是流动性质押LD （Liquid Staking） ？](#什么是流动性质押ld-liquid-staking-)

质押Token是Token持有者把自己的资产锁定在链上一定时间，以保护网络并获得奖励的过程。然而，传统的质押不够灵活，可能会阻止Token持有者将其资产用于其他目的。现在流动性质押的作用是通过允许Token持有者质押他们的资产同时仍然能够随心所欲地使用所抵押的资产来解决其他问题。这能为Token持有者提供质押奖励和网络安全的好处，而无需放弃对其资产的控制。

简单来说，流动性质押是Token持有者获得奖励和支持网络的一种方式，同时仍然可以灵活地使用其资产。

# [什么是流动质押衍生品（LSD）？](#什么是流动质押衍生品lsd)

对于那些想要质押Token但又不想被束缚的人来说，流动性质押衍生品可以改变游戏规则。这些衍生品代表了Token持有者的质押资产，并确认了质押者对质押池的参与。令人兴奋的是，这些Token可用于去中心化金融世界中的借代、交易和抵押品。这意味着您可以获得质押Token的好处，而不必放弃使用权。这是一个双赢的局面，提供了前所未有的灵活性和效率。

衍生品可能听起来很复杂，但它们实际上是金融中一个非常普遍的概念。让我用简单的术语向您解释一下，就相当于是两个实体之间的合同，允许评估标的资产的未来价值。

在加密货币的世界中，衍生品可用于在特定日期以合约方式推测加密货币的未来价值。如果加密货币的价值发生变化，这将提供潜在利润的机会。流动性质押衍生品更进了一步，让参与者可以享受质押Token的好处，同时也能够参与衍生品交易。

这是通过令牌化实现的，它简化了流程并使其更易于访问。这听起来很厉害，能让人获得大量回报，但仍然可能让一些人感到困惑，让我们进一步更好地理解它

# [LSD 是如何工作的？](#lsd-是如何工作的)

众所周知，“抵押”您的Token，这意味着您将它们存放一段时间，通常是为了帮助提高区块链网络的安全性，但如前所述，缺点是您不能在需要时使用这些Token，这很不方便。流动性质押让您可以在 DeFi世界的其他地方使用您的 质押过的Token，同时仍然可以通过质押获得奖励。这是通过标记化完成的，即通过使用具有流动性的质押衍生Token。因此，您质押的Token不仅可以闲置，还可以用于其他方面，您仍然可以通过帮助保持网络稳定和安全来获得奖励。这可以激励更多的人投入他们的Token并参与 DeFi 的世界。

[ ](../lsd/index.html "Previous chapter") [ ](../lsd/02.economic.html "Next chapter")

[ ](../lsd/index.html "Previous chapter") [ ](../lsd/02.economic.html "Next chapter")

# 02.economic.html
# sui move book

[ ](../print.html "Print this book")

# [](#)

  * https://docs.sui.io/learn/tokenomics

# [SUI 总量](#sui-总量)

  * 总量 100亿 总量恒定
  * 质押奖励 10% 10亿
  * 初始每天挖矿奖励110万SUI 每 90 天递减 10%的总量 ，比如第一个90天 每天奖励 110万 ，主网已经上线超过了100天， 目前每天奖励100万SUI
  * 目前总的已经质押的74亿Token 大部分都是锁仓的，不会用于流通，具体的流动可以查看 https://sui.io/token-schedule
  * 锁仓的代币产生的收益也是锁仓的，不会流通出来
  * 可以理解SUI 一直总量通缩模型，因为有存储基金存在

# [存储基金](#存储基金)

Sui 包含一个高效且可持续的经济机制来为数据存储融资，鉴于 Sui 能够存储任意大量链上数据，这一点非常重要。

从财务角度来看，链上数据存储带来了严峻的跨期挑战：今天处理数据并将数据写入存储的验证者可能与未来需要存储该数据的验证者不同。如果用户仅在写入时为计算能力支付费用，那么未来的用户将需要补贴过去用户的存储费用并支付不成比例的高额费用。如果不加以解决，这种负网络外部性可能会在未来给隋带来沉重的负担。

Sui 的经济设计包括一个存储基金，它将过去交易的存储费用重新分配给未来的验证者。当用户在 Sui 上进行交易时，他们需要预先支付计算和存储费用。存储费用存入存储基金，用于调整分配给验证者的未来权益奖励相对于与他们抵押 SUI 的用户的份额。这一设计旨在为未来的 Sui 验证者提供可行的商业模式。

# [存储基金奖励](#存储基金奖励)

Sui 的委托权益证明机制将总权益计算为用户权益加上存储基金中存入的 SUI 代币之和。因此，存储基金根据其相对于总股权的规模获得总体股权奖励的一定比例份额。这些股权奖励大部分是股份 – 支付给当前验证者以补偿存储成本，而剩余的 奖励用于对基金进行再投资。换句话说，过去交易提交的存储费用所产生的权益奖励将支付给当前的验证者，以补偿他们的数据存储成本。当链上存储要求很高时，验证者会获得大量额外奖励以补偿其存储成本。当存储要求较低时反之亦然。

## [更具体地说，存储基金具有三个主要特点：](#更具体地说存储基金具有三个主要特点)

  * 存储基金由过去的交易提供资金，并作为在不同时期转移天然气费用的工具。这确保了未来验证者的存储成本可以由最初创建这些存储需求的过去用户得到补偿。 存储基金仅支付其资本回报，不分配本金。也就是说，在实践中，验证者就好像能够借用存储基金的 SUI 作为额外权益，并保留大部分权益奖励（一股 ）。但请注意，验证者不会直接从存储基金接收资金。这保证了基金永远不会损失资本并可以无限期生存。这一功能进一步得到了共享的支持 的股权奖励再投资于该基金。
  * 存储基金包括一个删除选项，用户只要删除之前存储的链上数据，就可以获得存储费回扣。请注意，如果用户删除数据，他们将获得最初支付的存储费的部分退款。这一功能的合理性在于，存储费用是为了支付整个数据生命周期的存储费用而存在的。一旦数据被删除，就没有理由继续收取存储费用，因此这些费用会被退还。 >> 重要提示：删除选项不应与删除过去的交易相混淆。Sui 上的活动在每个 epoch 边界处最终确定，过去的交易是不可变的且永远无法逆转。例如，可以删除的数据类型是与不再存在的对象相对应的数据，例如 NFT 的元数据、已兑换的门票、已结束的拍卖等。

# [存储基金机制](#存储基金机制)

存储基金的规模在每个时期都是固定的，其规模在时期边界根据整个时期累积的净流入而变化。流入和流出对应于：

  * 来自为当前时期执行的交易支付的存储费的流入。
  * 基金收益再投资新本金的流入。具体来说，份额
  * 存储基金中累积的、未支付给验证者的权益奖励的比例。
  * 支付给删除与过去交易相关的数据的用户的存储费回扣的流出。
  * 回扣功能的关键特性是，在单笔交易层面，它限制存储资金流出始终小于原始存储流量。这种机制保证存储资金永远不会耗尽，并且其大小与存储中保存的数据量一致。

# [存储基金激励](#存储基金激励)

存储基金为隋经济引入了各种理想的激励措施：

当存储此类数据的成本超过在链上维护该数据所获得的价值时，其机制会激励用户删除数据并获得存储费用回扣。这引入了一种有用的基于市场的机制，当用户保留存储变得不经济时，可以释放存储。 它对 SUI 代币造成通货紧缩压力，因为活动增加导致存储需求更大，并导致更多 SUI 从流通中消失。 它具有资本效率，因为它在经济上相当于用户通过按周期付费模式支付存储费用的租赁模式。

# [SUI的主要用途](#sui的主要用途)

  * PoS：用于参与委托权益证明机制
  * Gas机制：用于支付网络交易和存储费用
  * 提供流动性：在Sui经济中提供链上原生流动性
  * 社区治理：有可能影响Sui未来的治理

质押流程

# [Q1：什么是质押者？](#q1什么是质押者)

拥有Sui地址的任何人都可以通过将SUI质押到他们选择的一个或多个验证节点来进行质押，质押者包括质押SUI的验证节点或第三方SUI持有者。

# [Q2：质押的SUI去哪了？](#q2质押的sui去哪了)

请放心，它们被安全地锁定在您的地址里！与其他网络中现有的流动性质押解决方案不同，在那些方案中，质押者需要将其质押的token控制权移交给第三方的流动性质押智能合约。而Sui允许SUI持有者直接将其SUI质押到选择的验证节点，同时保留其质押token的完全控制权。质押token受到Sui协议层的保护，不受第三方智能合约的漏洞影响。

# [Q3：什么是质押池？](#q3什么是质押池)

每个Sui验证节点都会维护自己的质押池，以跟踪质押的数量并累积质押奖励。验证节点池与每个epoch边界计算的兑换率时间序列一起运作。这些兑换率决定了过去的每个SUI质押者在将来可以提取的SUI数量。重要的是，随着更多奖励存入质押池，兑换率会上升，而且SUI存入质押池的时间越长，累积的奖励越多。 每个验证节点都有一个与其特定质押池相对应的兑换率时间序列，在质押池对象内部链上存储。从SUI质押者的角度来看，可以通过以下共识跟踪其质押的价值。 E’时的SUI = (E时存入的SUI) * (E’时的兑换率 / E时的兑换率) 在概念上，质押池的运作方式与流动性池完全相同。当SUI在epoch E存入质押池时，根据epoch E的兑换率，将其转化为流动性token。随着质押池获得奖励，兑换率会上升。在epoch E’，这些流动性token的价值更高，可以转化为更多的SUI。 Sui质押池与典型的流动性池之间唯一的区别在于，在Sui中，流动性token不存在。相反，全局兑换率表用于跟踪计算。这种设计的一个优点是，由于质押池中的所有SUI都是相同的，无论它们最初是作为新的质押还是作为质押奖励存入，所有SUI都立即视为质押，因此奖励立即进行复利计算。 质押池是一个系统级智能合约（staking_pool.move），也是Sui框架的一部分。

# [Q4：SUI质押的发展经历了哪些阶段?](#q4sui质押的发展经历了哪些阶段)

# [质押v1：[原始设计，已弃用]](#质押v1原始设计已弃用)

这个设计在测试网第二阶段使用，但现在已经被弃用，移除了两个主要的实现： 此前，质押流程分为两个阶段。首先，质押者存入SUI后，立即获得一个包含锁定SUI的StakedSUI对象。其次，在epoch结束时，一旦质押池的兑换率更新，用户获得一个包含用户池token的Delegation对象。Delegation对象必须等到epoch关闭，因为在epoch内无法预先知道结束时期的兑换率，它取决于整个epoch内收集的Gas费的数量。这种方法在epoch边界时需要重新配置非常多的交易，因此在质押v2中已经移除了Delegation对象（见下文）。 此前，当执行质押取回时，取回的质押进入待定质押状态，并且在epoch边界关闭后进行处理。这样做的原因是，由于当前epoch的质押奖励是在整个epoch内确定的，因此在epoch仍然活跃时无法完全预测到结束epoch时的兑换率。因此，此设计在处理具有更新兑换率的取回之前需要等待epoch关闭。这种情况已不再存在，取回将立即以前一个epoch的兑换率进行处理。

# [质押v2：[当前主网设计]](#质押v2当前主网设计)

两个主要变化是： 质押池的核算已经简化。与之前一样，当用户质押SUI时，这些对象被包装成StakedSUI对象。但是，质押池不再通过Delegation对象来实现每个用户对质押池的相对所有权。相反，核算是直接通过StakedSUI对象的时间戳（确定存款发生的时间点）和存款epoch与取回epoch之间的兑换率变化来进行的。每个质押池的数据结构包含一个该池的兑换率时间序列。这些兑换率可以用于确定该池中任何质押者的取回情况。 质押取回将立即根据上一个epoch的兑换率进行处理，无需等待当前epoch关闭。取回包括用户存入的原始质押和累积到上一个epoch的所有质押奖励。这种方法的缺点是，在取回的epoch内，质押者不会获得其质押奖励。直到epoch关闭，由于无法预先知道在当前epoch内将累积多少质押奖励，因此无法将其包含在取回中。因此，任何用户都可以立即取回他们的质押并获得： E’时取回的SUI = (E时存入的SUI ) * (E’-1时的兑换率 /E时的兑换率 )

# [质押v3：[未来更新]](#质押v3未来更新)

这是最终将推向主网的长期解决方案。 质押v2设计的主要挑战是无法处理解除绑定（或冷却）期的问题，这对于网络安全至关重要。通过修改Sui如何处理取回请求，将其分为两个步骤来实现这一点： 在第一笔交易中，质押者将提交取回请求并获得一个WithdrawalReceipt。在此时，质押者不会收到任何SUI。 在第二笔交易中，一旦预定的解除绑定期过去，质押者可以提交WithdrawalReceipt并获得他们的SUI本金和累积奖励。 重要的是，除了启用解除绑定期，此设计还可以让用户在取回WithdrawalReceipt之后获得他们应得的全部奖励，因为必须在提交取回请求的epoch结束时进行取回。这种设计不会遇到质押v1中引起非常大的重新配置交易的挑战，因为WithdrawalReceipt对象可以在任何时候兑换（一旦解除绑定期结束），并且不依赖于epoch边界。

# [Q5：我的质押存入请求何时生效？](#q5我的质押存入请求何时生效)

一旦质押存入请求提交，它立即进入质押池中的待定状态。Sui钱包将反映用户账户的任何待定质押存入请求。然而，待定质押存入请求要到请求所在的epoch结束时才会生效。

# [Q6：我的解除质押请求何时生效？](#q6我的解除质押请求何时生效)

解除质押或取回请求一旦收到，将立即进行处理。质押者将获得最初存入的SUI以及累积到上一个epoch边界的所有质押奖励。换句话说，它们不包括当前epoch的质押奖励。有关此实现的更多详细信息，请参阅质押v2。请注意，在未来，一旦实施了质押v3，解除质押请求将不会立即处理。

# [Q7：如何计算每个验证者池的兑换率？](#q7如何计算每个验证者池的兑换率)

每个验证节点池的兑换率在每个epoch边界处按以下方式计算： E+1时兑换率 = （1 + （E时质押奖励 / E时质押金额））*（E时兑换率） 重要的是，epoch E期间质押者获得的质押奖励是该epoch内验证节点池获得的总质押奖励的一个子集。换句话说，验证节点池获得的总质押奖励可以分为三个独立的部分，取决于它们是由谁获得的： 质押奖励 = 质押者奖励 + 验证节点佣金 + 存储基金奖励 普通的SUI质押者只获得质押者奖励。与此同时，验证节点获得对这些奖励收取的佣金（验证节点佣金）以及归属于存储基金的奖励。 验证节点池的兑换率仅通过质押者奖励的金额进行更新，以便完全追踪SUI质押者获得的奖励。然而，这种计算方法还使Sui能够通过更新的兑换率，以额外的StakedSUI对象的形式向验证节点提供验证节点佣金和存储基金奖励，从而跟踪验证节点获得的奖励

# [Q8：相对于第三方SUI持有者，验证节点的质押过程有何不同？](#q8相对于第三方sui持有者验证节点的质押过程有何不同)

过程是相同的。将SUI一起质押的验证节点将遵循，与该验证节点一起质押的任何第三方SUI持有者相同的过程。

# [Q9：相对于SUI质押者，验证节点的质押奖励计算有何不同？](#q9相对于sui质押者验证节点的质押奖励计算有何不同)

在给定的验证节点质押池中，所有质押者通过池子的兑换率升值获得相同比例的奖励。此外，由于验证节点在管理质押上赚取佣金和存储基金奖励，验证节点在每个epoch结束时会按照这些金额的比例获得额外的StakedSUI对象。

# [质押奖励](#质押奖励)

## [Q1：质押奖励从哪里来？](#q1质押奖励从哪里来)

  * 质押奖励来自于当前epoch内获得的交易gas费以及在epoch结束时释放的质押津贴。
  * 质押奖励 = 质押津贴 + gas费 质押津贴旨在在网络的早期阶段进行补贴，资金来源是10％的SUI。一旦这个分配用尽，质押奖励的整体将由通过常规网络操作收集到的gas费组成。

## [Q2：质押奖励会自动复利吗？](#q2质押奖励会自动复利吗)

是的！请参阅上面“Q3：什么是质押池”的回答。

## [Q3：主网上会有多少质押奖励？](#q3主网上会有多少质押奖励)

质押奖励由gas费和质押津贴组成。每个时epoch分布的总金额如下确定： 质押津贴：每个epoch分布的金额在epoch开始之前根据预定的时间表确定。 gas费：每个epoch的金额取决于整个epoch内获得的总gas费。每个Sui交易根据两个变量支付gas费，即执行的gas单位和gas价格： gas费 = gas价格 * gas单位 所收集的gas费总额对应于在epoch内处理的所有交易的gas费总和。在常规市场条件下，我们预计绝大多数交易的gas价格将等于参考gas价格。将来，Sui将引入拥堵定价机制，以便在网络拥堵时，gas价格将高于参考gas价格，因为用户实际上会向验证节点付小费以换取优先权。

## [质押限制](#质押限制)

## [Q1：我可以从活跃的验证节点质押中解除一部分吗质押？](#q1我可以从活跃的验证节点质押中解除一部分吗质押)

不支持这个。每个StakedSUI对象的解除质押要么全部解除，要么不解除。 然而，用户可以将任意数量的SUI对象质押给任何验证节点。因此，如果他们从一个验证节点解除了一部分SUI对象的质押，实际上可以从验证节点部分解除质押。由于StakedSUI对象可以分成多个对象，如果质押者首先将一个StakedSUI对象分成几个对象，然后解除一些对象的质押，那么质押者始终可以有效地解除一部分质押。

## [Q2：单个验证节点的最小质押金额是多少？](#q2单个验证节点的最小质押金额是多少)

最小的质押金额是1个SUI。

## [Q3：验证节点的质押与共识中的投票权有什么关系？](#q3验证节点的质押与共识中的投票权有什么关系)

按照惯例，无论质押的数量如何，总投票权始终为10,000，因此法定门槛为6,667（2/3的比例）。每个验证节点的共识投票权与其质押成比例，但有一个例外：单个验证节点的投票权上限为1,000（总投票权的10%）。

## [Q4：单个验证节点的最大质押金额是多少？](#q4单个验证节点的最大质押金额是多少)

没有限制。然而，在共识中，单个验证节点的投票权被设定上限为10%。如果一个验证节点积累了超过总质押的10%，那么该验证节点的投票权将保持在10%，其余的投票权将分散到其余的验证节点集。 类似地，验证节点的质押奖励份额也将使用相同的10%上限来计算管理质押的金额（请参阅质押奖励计算）。换句话说，一旦一个验证节点积累了超过总质押的10%，每个质押的SUI奖励将开始下降，因为质押池不再增加其获得的质押奖励的数量。

# [质押奖励计算](#质押奖励计算)

温馨提示：公式太多，感兴趣的建议认真阅读。

# [验证节点](#验证节点)

## [Q1：什么是参考gas价格，验证节点需要什么时候参与？](#q1什么是参考gas价格验证节点需要什么时候参与)

Sui的设计使得终端用户可以在常规网络操作期间gas价格保持稳定和可预测。这是通过在每个epoch开始时由验证节点设置网络的参考gas价格来实现的。 在操作上，这是通过“gas价格调查”来实现的，步骤如下： 在每个epoch E期间，每个验证节点都会提交他们认为下一个epoch E+1的最佳参考gas价格。 在epoch边界，当Sui从epoch E过渡到epoch E+1时，网络会观察验证节点集合中的气价gas价格，并将按照2/3处的投票加权作为下一个epoch的参考gas价格。因此，每个epoch的参考gas价格在整个epoch内都是恒定的，在epoch更改时才会更新。 提交gas价格调查的报价过程非常简单。每个验证节点都拥有一个包含他们参考gas价格的对象。如果验证节点希望更改他们的报价，那么他们只需更新该对象中的值。验证节点可以通过转让他们的操作能力对象来将设置gas价格报价的能力委托给其他账户。

## [Q2：统计规则是什么样子，验证节点需要什么时候参与？](#q2统计规则是什么样子验证节点需要什么时候参与)

Sui的设计旨在鼓励和强制执行验证节点集合的社区监控。这是通过统计规则实现的，每个验证节点都会监控和评分其他每个验证节点，以确保每个人都高效运作，考虑到网络的最佳利益。那些不合规的验证节点会被罚款，其质押奖励会被削减。 协议规定仅在epoch边界计算全局统计规则分数，因此依赖于验证节点的积极监控，在检测到其他验证节点行为变化时更改其分数。一般来说，统计规则的默认选项应始终为所有验证节点的得分为1，并且只有在确定操作不当时才更改为0。实际上，统计规则由一组每个验证节点拥有的对象组成，这些对象的默认分数为1，因此验证节点通常会被动地只在需要时更新与其他验证节点的分数相对应的对象。与提交gas价格报价类似，验证节点也可以通过转让他们的操作能力对象将其参与统计规则的权力委托给其他账户。

## [Q3：验证节点在统计规则中分配0分是根据什么标准？](#q3验证节点在统计规则中分配0分是根据什么标准)

统计规则应该通过社会均衡来实施。验证节点集合应积极监控自己，如果一个验证节点显然表现不佳，那么其他验证节点应该给该验证节点打0分并削减其奖励。未来，随着Sui网络的成熟，我们预计社区将启动公共仪表板来跟踪验证节点的性能，并可用作进一步了解验证节点运营的信号。

## [Q4：是否可以将多个验证节点打0分？](#q4是否可以将多个验证节点打0分)

可以。通过统计规则，每个验证节点都会为其他每个验证节点评分，没有关于每个验证节点可以提交多少个0分或1分的限制。

[ ](../lsd/01.intor.html "Previous chapter") [ ](../lsd/03.api.html "Next chapter")

[ ](../lsd/01.intor.html "Previous chapter") [ ](../lsd/03.api.html "Next chapter")

# 03.api.html
# sui move book

[ ](../print.html "Print this book")

# [代码地址](#代码地址)

  * https://github.com/MystenLabs/sui/tree/main/crates/sui-framework/packages/sui-

# [StakingPool](#stakingpool)

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      struct StakingPool has key, store {
        id: UID,
        /// The epoch at which this pool became active.
        /// The value is `None` if the pool is pre-active and `Some(<epoch_number>)` if active or inactive.
        activation_epoch: Option<u64>,
        /// The epoch at which this staking pool ceased to be active. `None` = {pre-active, active},
        /// `Some(<epoch_number>)` if in-active, and it was de-activated at epoch `<epoch_number>`.
        deactivation_epoch: Option<u64>,
        /// The total number of SUI tokens in this pool, including the SUI in the rewards_pool, as well as in all the principal
        /// in the `StakedSui` object, updated at epoch boundaries.
        sui_balance: u64,
        /// The epoch stake rewards will be added here at the end of each epoch.
        rewards_pool: Balance<SUI>,
        /// Total number of pool tokens issued by the pool.
        pool_token_balance: u64,
        /// Exchange rate history of previous epochs. Key is the epoch number.
        /// The entries start from the `activation_epoch` of this pool and contains exchange rates at the beginning of each epoch,
        /// i.e., right after the rewards for the previous epoch have been deposited into the pool.
        exchange_rates: Table<u64, PoolTokenExchangeRate>,
        /// Pending stake amount for this epoch, emptied at epoch boundaries.
        pending_stake: u64,
        /// Pending stake withdrawn during the current epoch, emptied at epoch boundaries.
        /// This includes both the principal and rewards SUI withdrawn.
        pending_total_sui_withdraw: u64,
        /// Pending pool token withdrawn during the current epoch, emptied at epoch boundaries.
        pending_pool_token_withdraw: u64,
        /// Any extra fields that's not defined statically.
        extra_fields: Bag,
      }
    }
```

```

# [ADD](#add)

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      public entry fun request_add_stake(
        wrapper: &mut SuiSystemState,
        stake: Coin<SUI>,
        validator_address: address,
        ctx: &mut TxContext,
      ) {
        let staked_sui = request_add_stake_non_entry(wrapper, stake, validator_address, ctx);
        transfer::public_transfer(staked_sui, tx_context::sender(ctx));
      }
    }
```

```

# [](#)

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      public entry fun request_withdraw_stake(
        wrapper: &mut SuiSystemState,
        staked_sui: StakedSui,
        ctx: &mut TxContext,
      ) {
        let withdrawn_stake = request_withdraw_stake_non_entry(wrapper, staked_sui, ctx);
        transfer::public_transfer(coin::from_balance(withdrawn_stake, ctx), tx_context::sender(ctx));
      }
    }
```

```

[ ](../lsd/02.economic.html "Previous chapter") [ ](../lsd/04.ecosystem.html "Next chapter")

[ ](../lsd/02.economic.html "Previous chapter") [ ](../lsd/04.ecosystem.html "Next chapter")

# 04.ecosystem.html
# sui move book

[ ](../print.html "Print this book")

# [借贷协议](#借贷协议)

  * https://app.naviprotocol.io/
  * https://omnilending.omnibtc.finance/lending
  * https://app.scallop.io/

# [swap](#swap)

  * https://app.cetus.zone/
  * https://www.app.kriya.finance/
  * https://suiswap.app/
  * https://sui-deepbook.com/

# [](#)

  * https://typus.finance/vault/
  * https://abex.fi/ 
  * https://app.bucketprotocol.io/

[ ](../lsd/03.api.html "Previous chapter")

[ ](../lsd/03.api.html "Previous chapter")
