
# index.html
# Sui Move导论

[ ](../print.html "Print this book")

[ ](../unit-one/lessons/1_配置环境.html "Next chapter")

[ ](../unit-one/lessons/1_配置环境.html "Next chapter")

# 1_配置环境.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [配置开发环境](#配置开发环境)

欢迎来到Sui Move的导论课。在第一单元，我们会带你过一遍配置开发环境的流程，创建运行一个Hello World的项目来体验Sui开发。

## [本地安装 Sui Binaries](#本地安装-sui-binaries)

[参考页面](https://docs.sui.io/build/install#install-sui-binaries)

  1. [安装依赖](https://docs.sui.io/build/install#prerequisites) (取决于操作系统) [Rust备选安装方法](https://www.cnblogs.com/hustcpp/p/12341098.html) [Homebrew备选安装方法](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/) [推荐Rust入门课](https://www.bilibili.com/video/BV1hp4y1k7SV)

  2. 安装 Sui binaries

`cargo install --locked --git https://github.com/MystenLabs/sui.git --branch devnet sui`

  3. 检验 binaries 是否安装成功:

`sui --version`

如果 sui binaries 安装成功，你将在终端看到版本信息。




## [使用预先安装有 Sui Binaries 的 Docker 镜像](#使用预先安装有-sui-binaries-的-docker-镜像)

  1. [安装 Docker](https://docs.docker.com/get-docker/)

  2. Pull 为 Sui Move 导论课预制的 Docker 镜像

`docker pull mysten/sui-tools:devnet`

  3. 启动并进入 Docker 容器的 shell:

`docker run --name suidevcontainer -itd mysten/sui-tools:devnet` `docker exec -it suidevcontainer bash`




## [为 VS Code 配置 Sui Move Analyzer 插件](#为-vs-code-配置-sui-move-analyzer-插件)

  1. 使用cargo安装: `cargo install --git https://github.com/movebit/move --branch move-analyzer2-release move-analyzer`

  2. 从 VS Marketplace 安装 [sui-move-analyzer 插件](https://marketplace.visualstudio.com/items?itemName=MoveBit.sui-move-analyzer)




## [Sui CLI 基础用法](#sui-cli-基础用法)

[参考页面](https://docs.sui.io/build/cli-client)

### [管理网络](#管理网络)

  * 切换网络: `sui client switch --env [network alias]`
  * 默认网络别名: 
    * 本地网 localnet: http://0.0.0.0:9000
    * 开发网 devnet: https://fullnode.devnet.sui.io:443
  * 列出当前所有网络别名: `sui client envs`
  * 添加新的网络别名: `sui client new-env --alias <ALIAS> --rpc <RPC>`



### [查询启用地址和 Gas Objects](#查询启用地址和-gas-objects)

  * 查询当前保存了密钥的地址: `sui client addresses`
  * 查询当前启用的地址: `sui client active-address`
  * 列出所拥有的 gas objects: `sui client gas`



## [获得开发网 Devnet 的 Sui Tokens](#获得开发网-devnet-的-sui-tokens)

  1. [加入 Sui Discord](https://discord.gg/sui)
  2. 完成身份验证步骤
  3. 进入 #devnet-faucet 频道
  4. 输入 `!faucet <WALLET ADDRESS>`



如果使用Discord不方便或faucet故障，可以直接在终端输入指令

```

    
    
    curl --location --request POST 'https://faucet.devnet.sui.io/gas' \
    --header 'Content-Type: application/json' \
    --data-raw '{"FixedAmountRequest":{"recipient":"<WALLET ADDRESS>"}}'
    
```

## [获得测试网 Testnet 的 Sui Tokens](#获得测试网-testnet-的-sui-tokens)

  1. [加入 Sui Discord](https://discord.gg/sui)
  2. 完成身份验证步骤
  3. 进入 #testnet-faucet 频道
  4. 输入 `!faucet <WALLET ADDRESS>`

[ ](../../unit-one/index.html "Previous chapter") [ ](../../unit-one/lessons/2_sui项目结构.html "Next chapter")

[ ](../../unit-one/index.html "Previous chapter") [ ](../../unit-one/lessons/2_sui项目结构.html "Next chapter")

# 2_sui项目结构.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Sui 项目结构](#sui-项目结构)

## [Sui Module 与 Package](#sui-module-与-package)

  * 一个 Sui module 是一系列函数 functions 和类型 types 打包后的集合，被开发者发布到一个特定的地址下

  * Sui 的标准库发布在 `0x2` 地址下，而用户发布 modules 的地址则是 Sui Move 虚拟机分配的伪随机地址

  * Module 开头是 `module` 关键词，后面跟着 module 名和花括号 {} ，module 的内容放置在花括号内。


```

    
    
    module hello_world {
      // module 内容
    }
    
```

  * 已发布的 modules 在 Sui 里是 immutable objects; 意味着不能被更改、转移或删除。因为不可变的特性，该 object 不再由某个人拥有，因此可以被任何人使用。

  * Move package 是一系列 modules 的集合，外加上 Move.toml 的配置文件




## [初始化一个 Sui Move Package](#初始化一个-sui-move-package)

使用下面的 Sui CLI 指令来快速创建一个 Sui package 框架:

`sui move new <PACKAGE NAME>`

在我们这个单元的例子，会创建一个 Hello World 项目:

`sui move new hello_world`

这条指令创建了: 

  * 项目根文件夹 `hello_world`
  * `Move.toml` 配置文件
  * 用于存放 Sui Move 智能合约的 `sources` 子文件夹



### [`Move.toml` 配置结构](#movetoml-配置结构)

`Move.toml` 是一个package的配置文件，会被自动创建于项目的根目录。

`Move.toml` 包含三个部分:

  * `[package]` 声明了该 package 的命名和版本数
  * `[dependencies]` 声明了该 package 依赖的其他 packages, 包括 Sui 标准库和其他第三方依赖库
  * `[addresses]` 声明了该 packages 源代码中地址的别名



#### [示例 `Move.toml` 文件](#示例-movetoml-文件)

这是使用 Sui CLI 命令生成 `hello_world` package 时自动生成的配置文件 `Move.toml`:

```

    
    
    [package]
    name = "hello_world"
    version = "0.0.1"
    edition = "2024.beta"
    
    [dependencies]
    Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "framework/testnet" }
    
    [addresses]
    hello_world = "0x0"
    
```

我们可以看到，在这里 Sui 标准库使用了一个 Github 仓库来声明，但其实也可以使用本地 binary 文件的相对路径或绝对路径来声明，比如:

```

    
    
    [dependencies]
    Sui = { local = "../sui/crates/sui-framework/packages/sui-framework" } 
    
```

### [Sui Module 和 Package 的命名](#sui-module-和-package-的命名)

  * Sui Move 的 module 和 package 命名跟随 [Rust 命名规范](https://rust-lang.github.io/api-guidelines/naming.html)

  * Sui Move module 和 package 命名通常使用蛇形命名法 snake casing, i.e. this_is_snake_casing.

  * Sui module 名称会使用 Rust 路径分隔符 `::` 来分开 package 名和 module 名, 比如:

    1. `unit_one::hello_world` - `hello_world` module in `unit_one` package
    2. `capy::capy` - `capy` module in `capy` package
  * 有关 Move 命名约定的更多信息, 请查看 _Move Book_ 中的[风格部分](https://move-language.github.io/move/coding-conventions.html#naming)


[ ](../../unit-one/lessons/1_配置环境.html "Previous chapter") [ ](../../unit-one/lessons/3_定制类型与能力.html "Next chapter")

[ ](../../unit-one/lessons/1_配置环境.html "Previous chapter") [ ](../../unit-one/lessons/3_定制类型与能力.html "Next chapter")

# 3_定制类型与能力.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [定制类型与能力](#定制类型与能力)

在这一节，我们会逐步创建示例智能合约 Hello World, 过程中会遇到一些像是定制类型与能力之类的基础概念，一道讲解。

## [初始化 Package](#初始化-package)

(如果你跳过了前面的章节) 在[安装 Sui binaries](https://github.com/sui-foundation/sui-move-intro-course/blob/main/unit-one/lessons/2_set_up_environment.md#install-sui-binaries-locally)之后，你可以使用下面的命令行指令来初始化一个名为 Hello World 的 Sui package:

`sui move new hello_world`

## [创建智能合约源文件](#创建智能合约源文件)

选择一个代码编辑器在 `sources` 子文件夹内创建一个名为 `hello.move` 的 Move 智能合约源代码。

然后在内创建一个格式如下的空 module:

```

    
    
    module hello_world::hello {
      // module contents
      
    }
    
```

### [引入声明 Import Statements](#引入声明-import-statements)

在 Move 中可以通过地址来直接引入 modules, 但为了让代码易于阅读，更建议使用关键词 `use` 来管理引入操作。

```

    
    
      use <Address/Alias>::<ModuleName>;
    
```

在我们的示例代码中，需要引入以下 modules:

```

    
    
      use std::string;
      use sui::object::{Self, UID};
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
    
```

## [定制类型](#定制类型)

Sui Move 中的 structure 是一种包含 key-value 键值对的定制类型，key是一项属性的命名，value是对应存储的值。Structure 使用关键词 `struct` 声明。一个 structure 最多拥有 4 项能力。

### [能力](#能力)

在 Sui Move 中，能力属于关键词，定义了类型数据在编译时的行为。

在 Sui Move 的编程语言中，能力特性至关重要，定义了 object 的行为。能力特性之间的每种独特组合都是独有的设计模式。我们会在课程中学习如何在 Sui Move 中使用能力特性。

但现在，只需要知道 Sui Move 中有四类能力特性:

  * **copy** : 值可以被复制
  * **drop** : 在作用域范围结束后值可以被丢弃
  * **key** : 在全局存储操作中值可以被用为key键
  * **store** : 值可以在全局存储中被存储



#### [资产](#资产)

在 Sui Move 中拥有 `key` 和 `store` 能力特性的定制类型被视为**资产** ，资产可以在全局存储中存储，也可以在不同账号之间转移。

[Move 能力类型说明](https://move-language.github.io/move/abilities.html)

### [Hello World 定制类型](#hello-world-定制类型)

在 Hello World 示例中定义的 object 如下:

```

    
    
      /// 包含了任意字符串的 object
      public struct HelloWorldObject has key, store {
        id: UID,
        /// 被 object 包含的字符串
        text: string::String
      }
    
```

这里的 UID 是一种 Sui Framework 类型 (sui::object::UID), 它定义了一个 object 的全局唯一ID. 每个定制类型都需要有一项 ID 属性。

[ ](../../unit-one/lessons/2_sui项目结构.html "Previous chapter") [ ](../../unit-one/lessons/4_函数.html "Next chapter")

[ ](../../unit-one/lessons/2_sui项目结构.html "Previous chapter") [ ](../../unit-one/lessons/4_函数.html "Next chapter")

# 4_函数.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [函数](#函数)

在这一节，我们会介绍 Sui Move 的函数，并且写下我们的第一个 Sui Move 函数，作为 Hello World 项目示例的一部分。

## [函数可见性](#函数可见性)

Sui Move 函数有三种可见性:

  * **private** : 作为函数可见性的默认设置；只允许同一 module 内的函数获取
  * **public** : 该函数既可以被同一 module 内的函数获取，也可以被其他 module 定义的函数获取
  * **public(friend)** : 该函数既可以被同一 module 内的函数获取，同时也可以被[该 module 的 friends 清单](https://diem.github.io/move/friends.html)上包含的 module 所定义的函数获取。



## [返回值](#返回值)

函数的返回类型在函数签名中的函数参数之后用冒号分隔指定。

函数的最后一行（执行时）没有分号即为返回值。

示例：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      public fun addition (a: u8, b: u8): u8 {
        a + b  
      }
    }
```


```

## [交易上下文 (Transaction Context)](#交易上下文-transaction-context)

通过交易直接调用的函数通常将 `TxContext` 实例作为最后一个参数。这是由 Sui Move VM 设置的特殊参数，不需要由调用函数的用户指定。

`TxContext` 对象包含关于调用 entry 函数的交易的[基本信息](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/sui-framework/sources/tx_context.move)，如发送者地址、签名者地址、交易的 epoch 等。

## [创建 `mint` 函数](#创建-mint-函数)

我们可以用如下方式定义 Hello World 示例项目中的 mint 函数:

```

    
    
      public entry fun mint(ctx: &mut TxContext) {
        let object = HelloWorldObject {
          id: object::new(ctx),
          text: string::utf8(b"Hello World!")
        };
        transfer::transfer(object, tx_context::sender(ctx));
      }
    
```

这个函数创建了 `HelloWoirldObject` 定制类型的一个新实例，然后使用 Sui 系统中的转移函数 transfer 将其发送给交易的调用者。

[字符编码：ASCII，Unicode 和 UTF-8](https://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)

[ ](../../unit-one/lessons/3_定制类型与能力.html "Previous chapter") [ ](../../unit-one/lessons/5_合约部署.html "Next chapter")

[ ](../../unit-one/lessons/3_定制类型与能力.html "Previous chapter") [ ](../../unit-one/lessons/5_合约部署.html "Next chapter")

# 5_合约部署.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [部署智能合约与 Hello World Demo 项目](#部署智能合约与-hello-world-demo-项目)

## [完整的 Hello World 示例项目](#完整的-hello-world-示例项目)

完整的 Hello World 项目可以在[这里](https://github.com/sui-foundation/sui-move-intro-course/tree/main/unit-one/example_projects/hello_world)被找到。

## [部署智能合约](#部署智能合约)

我们使用 Sui CLI 将 package 部署到 Sui 网络。你可以选择部署到 Sui 的 devnet 开发网，testnet 测试网或者本地节点。只要将 Sui CLI 设置到对应网络，并且拥有足够支付 gas 的 tokens 即可。

部署 package 的 Sui CLI 指令如下:

```

    
    
    sui client publish --gas-budget <gas_budget> [需要发布的 package 的绝对文件路径]
    
```

对于 `<gas_budget>`，我们可以使用像 20000000 这样的标准值。

如果未提供 `package` 的绝对路径，则默认为在 `.` 或当前目录下。

如果合约部署成功，输出信息会跟下面相似:

![发布输出](../images/publish.png)

在 `Published Objects` 下面的是刚才发布的 Hello World package 智能合约的 object ID. 

让我们使用 export 指令将该 object ID 的值传递给一个变量。

```

    
    
    export PACKAGE_ID=<在先前输出信息中的 package object ID>
    
```

## [在交易中调用函数](#在交易中调用函数)

接下来，我们通过调用刚才部署的智能合约中的 `mint` 函数来 mint 一个 Hello World object.

我们能够做这种操作是因为 `mint` 是一个 entry 函数。

完成该操作的 Sui CLI 指令是:

```

    
    
    sui client call --function mint --module hello_world --package $PACKAGE_ID --gas-budget 10000000
    
```

如果 `mint` 函数被成功调用，一个 Hello World object 会被创建和转移，console 中输出的信息会与下面相似:

![Mint 输出](../images/mint.png)

在 `Created Objects` 下面的是 Hello World object 的 object ID. 

## [使用 Sui Explorer 查看 Object](#使用-sui-explorer-查看-object)

可以使用 [Sui Explorer](https://explorer.sui.io/) 来察看我们刚才创建和转移的 Hello World object. 

从右上角的下拉菜单中选择正在使用的网络。

如果你使用的是本地开发节点，选择 `Custom RPC URL` 然后输入:

```

    
    
    http://127.0.0.1:9000
    
```

根据先前交易输出信息中的 object ID 进行搜索，你会在 explorer 中看到 object 的详情:

![Explorer 输出](../images/explorer.png)

你应该能看到这个 object 的属性中包含 "Hello World!" 字符串。

做得很棒！本课程的第一单元到这里完结。

[ ](../../unit-one/lessons/4_函数.html "Previous chapter") [ ](../../unit-two/index.html "Next chapter")

[ ](../../unit-one/lessons/4_函数.html "Previous chapter") [ ](../../unit-two/index.html "Next chapter")

# index.html
# Sui Move导论

[ ](../print.html "Print this book")

[ ](../unit-one/lessons/5_合约部署.html "Previous chapter") [ ](../unit-two/lessons/1_使用sui_objects.html "Next chapter")

[ ](../unit-one/lessons/5_合约部署.html "Previous chapter") [ ](../unit-two/lessons/1_使用sui_objects.html "Next chapter")

# 1_使用sui_objects.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [使用 Sui Objects](#使用-sui-objects)

## [介绍](#介绍)

Sui Move 是一门完全以 object 为中心的编程语言。在Sui上交易的输入与输出都可以是对 objects 的操作。 我们之前就已经在[第一单元的第四课中](../../unit-one/lessons/4_%E5%AE%9A%E5%88%B6%E7%B1%BB%E5%9E%8B%E4%B8%8E%E8%83%BD%E5%8A%9B.html#%E5%AE%9A%E5%88%B6%E7%B1%BB%E5%9E%8B%E4%B8%8E%E8%83%BD%E5%8A%9B)简单接触过这个概念，Sui objects 是 Sui 存储中的基本单元，所有都会使用 `struct` 关键词开头。

看一个记录学生成绩报告单的例子:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public struct Transcript {
      history: u8,
      math: u8,
      literature: u8,
    }
    }
```


```

上面定义的只是一个常规的 Move struct, 但还不是一个 Sui object. 要使一个定制的 Move 类型实例成为全局存储的 Sui object, 我们还需要添加 `key` 能力以及在 struct 定义时内部添加全局唯一的 `id: UID` 属性。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    use sui::object::{UID};
    public struct TranscriptObject has key {
      id: UID,
      history: u8,
      math: u8,
      literature: u8,
    }
    }
```


```

## [创建一个 Sui Object](#创建一个-sui-object)

创建一个 Sui object 需要一个唯一ID, 我们可以根据当前 `TxContext` 中的信息，使用 `sui::object::new` 函数来创建一个新的 ID. 

在 Sui 当中，每个 object 都必须拥有一个所有者，这个所有者可以是地址，别的 object, 或者就被所有人共享。在我们的例子中，我们想让新建的 `transcriptObject` 属于交易发起者。这可以先使用 `tx_context::sender` 函数获得当前 entry 函数调用者也就是交易发起者 sender 的地址，然后用 Sui framework 中的 `transfer` 函数转移所有权。

在下一节，我们会更深入探讨 object 的所有权。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    use sui::object::{Self};
    use sui::tx_context::{Self, TxContext};
    use sui::transfer;
    public entry fun create_transcript_object(history: u8, math: u8, literature: u8, ctx: &mut TxContext) {
     let transcriptObject = TranscriptObject {
      id: object::new(ctx),
      history,
      math,
      literature,
     };
     transfer::transfer(transcriptObject, tx_context::sender(ctx))
    }
    }
```


```

_💡注意: Move 支持属性的punning简化，当属性名与绑定的变量名一致的时候，就可以省略属性值的传递。_

[ ](../../unit-two/index.html "Previous chapter") [ ](../../unit-two/lessons/2_所有权.html "Next chapter")

[ ](../../unit-two/index.html "Previous chapter") [ ](../../unit-two/lessons/2_所有权.html "Next chapter")

# 2_所有权.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Sui Objects 所有权的类型](#sui-objects-所有权的类型)

Sui 中的每个 object 都有所有者的属性来声明所有权。在 Sui Move 中总共有四种类型的所有权。

  * 被拥有 
    * 被一个地址拥有
    * 被另一个 object 拥有
  * 共享 
    * 不可变的共享
    * 可变的共享



## [被拥有的 Objects](#被拥有的-objects)

前两种所有权类型都属于被拥有的 Objects. 在 Sui 中，和共享的 objects 处理方式不同，被拥有的 objects 不需要按全局排序。

### [被一个地址拥有](#被一个地址拥有)

还是看之前成绩记录单 `transcript` 的例子，这种类型的所有权是很符合直觉的。就像下面这行代码示例那样，这个 object 在创建后被转移到了一个地址，那么该 object 就被该地址所有。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      transfer::transfer(transcriptObject, tx_context::sender(ctx)) // where tx_context::sender(ctx) is the recipient
    }
```


```

这段代码中 `transcriptObject` 创建后被转移到了交易发起者的地址。

### [被另一个 object 拥有](#被另一个-object-拥有)

要使一个 object 被另一个 object 拥有，可以使用 `dynamic_object_field`. 这个功能我们会在未来的章节中探讨。简单来说，当一个 object 被另一个 object 拥有时，我们可以将其称为 子object. 一个 子object 同样可以在全局存储中使用 object ID 进行查询。

## [共享的 Objects](#共享的-objects)

## [不可变的共享 Objects](#不可变的共享-objects)

在 Sui 中确定的 objects 不能再被任何人改变，也因此可以被认为没有唯一的所有者，是共享的。在 Sui 中所有已发布的 packages 和 modules 都属于不可变的 objects. 

要手动让一个 object 不可变，可以调用下面这个特殊函数:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      transfer::freeze_object(obj);
    }
```


```

## [可变的共享 Objects](#可变的共享-objects)

Sui 里头的共享 objects 可以被任何人读和改。和被拥有的 objects 不一样，共享的 object 交易需要通过共识层协议得到的全局顺序。

要创建一个共享的 object, 可以调用这个方法:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      transfer::share_object(obj);
    }
```


```

一旦一个 object 成为了共享的，就会保持可变的状态。任何人都可以通过发起交易去改变这个 object.

[ ](../../unit-two/lessons/1_使用sui_objects.html "Previous chapter") [ ](../../unit-two/lessons/3_参数传递与删除 Object.html "Next chapter")

[ ](../../unit-two/lessons/1_使用sui_objects.html "Previous chapter") [ ](../../unit-two/lessons/3_参数传递与删除 Object.html "Next chapter")

# 3_参数传递与删除 Object.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [参数传递与删除 Object](#参数传递与删除-object)

## [参数传递 (使用 `value`, `ref` 和 `mut ref`)](#参数传递-使用-value-ref-和-mut-ref)

如果你已经熟悉 Rust 语言，你应该也会熟悉 Rust 的所有权概念。有几个拓展视频: [所有权规则、内存与分配](https://www.bilibili.com/video/BV1hp4y1k7SV?p=16), [所有权与函数](https://www.bilibili.com/video/BV1hp4y1k7SV?p=17), [引用与借用](https://www.bilibili.com/video/BV1hp4y1k7SV?p=18)。 与 Solidity 对比起来，move 语言的一个优点在于，你根据函数的接口就可以判断出这个函数调用会对你的资产做什么操作。看几个例子:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    use sui::object::{Self};
    // 你被许可获取分数但不能修改它
    public fun view_score(transcriptObject: &TranscriptObject): u8{
      transcriptObject.literature
    }
    // 你被允许查看和编辑分数，但不能删除它
    public entry fun update_score(transcriptObject: &mut TranscriptObject, score: u8){
      transcriptObject.literature = score
    }
    // 你被允许对分数做任何的操作，包括查看、编辑、删除整个 transcript 
    public entry fun delete_transcript(transcriptObject: TranscriptObject){
      let TranscriptObject {id, history: _, math: _, literature: _ } = transcriptObject;
      object::delete(id);
    }
    }
```


```

## [删除 Object 与 解包 Struct](#删除-object-与-解包-struct)

上面 `delete_transcript` 方法的例子展现了如何在 Sui 上删除一个 object.

  1. 要删除一个 object, 你首先要解包这个 object 并且获取它的 object ID. 解包的操作只能够在定义了这个 object 的 module 内进行。这是为了遵守 Move 的专用结构操作规则:


  * struct 类型只能在定义了该 struct 的 module 内创建("打包") 或 销毁("解包")
  * struct 的属性也只能在定义了该 struct 的 module 内获取



根据这些规则，如果你想要在定义了该 struct 的 module 之外调整你的 struct, 就需要为这些操作提供 public methods.

  1. 在解包 struct 获取它的 ID 之后，可以通过调用 framework 里头的 `object::delete` 方法处理它的 object ID 来实现删除。



_💡注意: 在上面示例中使用了下划线`_` 来标注未使用的变量或参数，可以在传入后立即消耗掉它们。_

**这里能找到对应这里的处于开发进展中版本的代码:[WIP transcript.move](../example_projects/transcript/sources/transcript_1.move_wip)**

[ ](../../unit-two/lessons/2_所有权.html "Previous chapter") [ ](../../unit-two/lessons/4_object_wrapping.html "Next chapter")

[ ](../../unit-two/lessons/2_所有权.html "Previous chapter") [ ](../../unit-two/lessons/4_object_wrapping.html "Next chapter")

# 4_object_wrapping.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Object Wrapping](#object-wrapping)

在 Sui Move 中有许多种方法可以将一个 object 嵌套在另一个 object 内。我们接下来介绍的第一种方法是 object wrapping.

继续看我们成绩记录单的例子。我们定义一个新的 `WrappableTranscript` 类型，以及关联的封装类型 `Folder`. 

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public struct WrappableTranscript has store {
      history: u8,
      math: u8,
      literature: u8,
    }
    public struct Folder has key {
      id: UID,
      transcript: WrappableTranscript,
    }
    }
```


```

在上面的例子中，`Folder` 封装了 `WrappableTranscript`, 并且 `Folder` 是可以根据其 id 查询地址等信息的，因为其具有 `key` 的能力。

## [Object Wrapping 特性](#object-wrapping-特性)

如果一个 struct 类型能被嵌入到一个 Sui object struct 中，这个 Sui object struct 通常具有 `key` 能力，被嵌入的 struct 必须具有 `store` 能力。

当一个 object 被封装后，封装起来的 object 就不再能单独根据 object ID 来获取。它会变成 封装 object 的一部分。更重要的是，被封装的 object 不能被作为参数进行传递，而只能通过 封装 object 进行获取。

由于这个特性，object wrapping 可以被用作为限制一个 object 在特定的合约调用之外不能被获取的方法。对更多关于 Object wrapping 的信息，可以看[这里](https://docs.sui.io/devnet/build/programming-with-objects/ch4-object-wrapping)。

[ ](../../unit-two/lessons/3_参数传递与删除 Object.html "Previous chapter") [ ](../../unit-two/lessons/5_object_wrapping_例子.html "Next chapter")

[ ](../../unit-two/lessons/3_参数传递与删除 Object.html "Previous chapter") [ ](../../unit-two/lessons/5_object_wrapping_例子.html "Next chapter")

# 5_object_wrapping_例子.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Object Wrapping 例子](#object-wrapping-例子)

我们来对成绩记录单的例子实现 object wrapping, 让 `WrappableTranscript` 被 `Folder` object 封装起来，使得 `Folder` object 可以被解包，因而内部的成绩记录单只能被特定的地址/观察者获取。

## [修改 `WrappableTranscript` 和 `Folder`](#修改-wrappabletranscript-和-folder)

首先，需要对我们上一节中定制的类型 `WrappableTranscript` 和 `Folder` 做一些调整。

  1. 给 `WrappableTranscript` 类型定义加上 `key` 的能力，可以成为资产和被转移。



回想一下，在 Sui Move 中具备了 `key` 和 `store` 能力的定制类型可以认为是资产。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public struct WrappableTranscript has key, store {
        id: UID,
        history: u8,
        math: u8,
        literature: u8,
    }
    }
```


```

  1. 我们需要为 `Folder` struct 增加一个额外的属性 `intended_address` 用来声明内部被封装起来的成绩记录单的目标观察者的地址。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public struct Folder has key {
      id: UID,
      transcript: WrappableTranscript,
      intended_address: address
    }
    }
```


```

## [请求 Transcript 方法](#请求-transcript-方法)

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public entry fun request_transcript(transcript: WrappableTranscript, intended_address: address, ctx: &mut TxContext){
      let folderObject = Folder {
        id: object::new(ctx),
        transcript,
        intended_address
      };
      //We transfer the wrapped transcript object directly to the intended address
      transfer::transfer(folderObject, intended_address)
    }
    }
```


```

这个方法简易地把一个 `WrappableTranscript` object 封装到一个 `Folder` object 里头，然后将被封装的成绩记录单转移到目标地址。

## [解封装 Transcript 方法](#解封装-transcript-方法)

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public entry fun unpack_wrapped_transcript(folder: Folder, ctx: &mut TxContext){
      // Check that the person unpacking the transcript is the intended viewer
      assert!(folder.intended_address == tx_context::sender(ctx), 0);
      let Folder {
        id,
        transcript,
        intended_address:_,
      } = folder;
      transfer::transfer(transcript, tx_context::sender(ctx));
      // Deletes the wrapper Folder object
      object::delete(id)
      }
    }
```


```

如果这个方法的调用者就是成绩记录单的目标观察者，就会从 `Folder` wrapper object 中解封装出 `WrappableTranscript` object, 然后将其发送给方法的调用者。

_问题: 我们为什么需要在这里手动删除 wrapper object? 如果我们不删除会怎样?_

### [Assert](#assert)

我们使用 `assert!` 语法来判定在交易中解包出成绩记录单的地址与 `Folder` wrapper object 中的 `intended_address` 属性是一致的。

这个 `assert!` 宏会以下面的格式输入两个参数:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    assert!(<bool expression>, <code>)
    }
```


```

这里的布尔表达式必须判断为真，否则就会弹出错误码 `<code>` 并中止。

### [定义错误码](#定义错误码)

上面是用了默认的0来作为错误码，但其实我们也可以用下面的方式来定义其他错误码:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      const ENotIntendedAddress: u64 = 1;
    }
```


```

这个错误码会在应用层上被消耗掉并进行合适的处理。

**这里能找到对应这里的第二版处于开发进展中版本的代码:[WIP transcript.move](../example_projects/transcript/sources/transcript_2.move_wip)**

[ ](../../unit-two/lessons/4_object_wrapping.html "Previous chapter") [ ](../../unit-two/lessons/6_capability_设计模式.html "Next chapter")

[ ](../../unit-two/lessons/4_object_wrapping.html "Previous chapter") [ ](../../unit-two/lessons/6_capability_设计模式.html "Next chapter")

# 6_capability_设计模式.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Capability 设计模式](#capability-设计模式)

现在我们已经有了一个基础的成绩记录单发布系统，还可以给我们的智能合约添加获取权限。

在 Sui 这种以 object 为中心的模型中，Capability 是一种常用的调整获取权限的设计模式。看一下我们该如何定义 capability object:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      // Type that marks the capability to create, update, and delete transcripts
      public struct TeacherCap has key {
        id: UID
      }
    }
```


```

我们可以通过定义一个新的 `TeacherCap` struct 来标记能够对成绩记录单实现特许操作的 capability 能力。如果想让这种 capability 是不可被转移的，只需要简单地在定义这个 struct 时不加上 `storage` 的能力即可。

_💡注意: 这种方法也同样适用于在 Move 里头实现 soulbound tokens (SBT). 只需要简单地定义一个具有`key` 能力但没有 `store` 能力的 struct 即可。_

## [传递和消耗 Capability Objects](#传递和消耗-capability-objects)

接下来，我们要修改这个函数方法使其只能被拥有 `TeacherCap` capability object 的人调用。修改后的函数会将 capability 作为额外的参数传入，并且立即消耗它。

比如说，对这个 `create_wrappable_transcript_object` 函数方法，我们可以做出下面的修改:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      public entry fun create_wrappable_transcript_object(_: &TeacherCap, history: u8, math: u8, literature: u8, ctx: &mut TxContext) {
        let wrappableTranscript = WrappableTranscript {
          id: object::new(ctx),
          history,
          math,
          literature,
        };
        transfer::transfer(wrappableTranscript, tx_context::sender(ctx))
      }
    }
```


```

我们传入了一个 `TeacherCap` capability object 的引用，然后使用 `_` 将其标注为未使用的变量或参数消耗掉。因为我们只传进了该 object 的引用，消耗掉这个引用对原始的 object 不造成任何影响。

_问题: 如果尝试将`TeacherCap` 的值传入会发生什么呢?_

这个改动使得只有一个拥有了唯一 `TeacherCap` object 的地址能够调用该函数方法，对该函数方法实现了获取权限控制。

我们对合约中的其他函数方法进行相似的改动，对成绩记录单实现专有操作。

## [初始化函数](#初始化函数)

Module 的初始化函数会在发布之后被立即调用。这可以用来初始化智能合约的初始状态，通常被用来分发最初的 capability objects.

在我们的例子中，可以像下面那样定义 `init` 初始化函数:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// Module initializer is called only once on module publish.
      fun init(ctx: &mut TxContext) {
        transfer::transfer(TeacherCap {
          id: object::new(ctx)
        }, tx_context::sender(ctx))
      }
    }
```


```

这会在 module 刚完成发布后创建一份 `TeacherCap` object 的拷贝，并且将其发送给发布合约者的地址。

下面是使用[Sui Explorer](../../unit-one/lessons/6_hello_world.html#viewing-the-object-with-sui-explorer)看到的发布合约交易产生的效果:

![Publish Output](../images/publish.png)

其中在上面交易中创建的第二个 object 是 `TeacherCap` object 的实例，会被发送到发布者的地址:

![Teacher Cap](../images/teachercap.png)

_问题: 那么第一个被创建的 object 是什么?_

## [添加更多教师或管理员](#添加更多教师或管理员)

要向更多地址提供管理权限，我们可以像下面那样定义函数去创建和分发更多 `TeacherCap` objects:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      public entry fun add_additional_teacher(_: &TeacherCap, new_teacher_address: address, ctx: &mut TxContext){
        transfer::transfer(
          TeacherCap {
            id: object::new(ctx)
          },
        new_teacher_address
        )
      }
    }
```


```

这个函数是复用 `TeacherCap` 来管理权限，但如果有需要，你也可以定义一个新的 capability struct 来定义超级管理员权限。

**这里能找到对应这里的第三版处于开发进展中版本的代码:[WIP transcript.move](../example_projects/transcript/sources/transcript_3.move_wip)**

[ ](../../unit-two/lessons/5_object_wrapping_例子.html "Previous chapter") [ ](../../unit-two/lessons/7_events.html "Next chapter")

[ ](../../unit-two/lessons/5_object_wrapping_例子.html "Previous chapter") [ ](../../unit-two/lessons/7_events.html "Next chapter")

# 7_events.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Events](#events)

Events 对 Sui Move 智能合约很重要，是索引器 indexers 追踪链上行为时的主要方法。对比服务器后端，你可以将 events 理解为服务器日志 logging, 而 indexers 则是解析器 parsers. 

Sui 上面的 events 也是以 objects 的形式表示。在 Sui 中有几种系统层级的 events, 包括 Move event, Publish event, Transfer object event 等。要看系统层级 event 类型的完整清单，可以参考这份 [Sui Events API 页面](https://docs.sui.io/build/event_api)。

交易产生的 event 详情可以在 [Sui Explorer](https://explorer.sui.io/) 下面的 `Events` 标签页查看:

![Event Tab](../images/eventstab.png)

## [定制化 Events](#定制化-events)

开发者也可以在 Sui 上根据需要自己定义 events. 可以像下面那样定制一个 event 来标记一次请求成绩记录单的事件。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// Event marking when a transcript has been requested
      public struct TranscriptRequestEvent has copy, drop {
        // The Object ID of the transcript wrapper
        wrapper_id: ID,
        // The requester of the transcript
        requester: address,
        // The intended address of the transcript
        intended_address: address,
      }
    }
```


```

表示 event 的类型具有 `copy` 和 `drop` 的能力。Event objects 不代表资产，我们只对其包含的数据感兴趣。所以，它们可以被复制，在作用域结束后被销毁。

要在 Sui 上发出一个 event 信号，你只需要使用[`sui::event::emit` 函数方法](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/event.md#function-emit)。

调整一下我们的 `request_transcript` 函数方法去发出这一 event 信号:

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      public entry fun request_transcript(transcript: WrappableTranscript, intended_address: address, ctx: &mut TxContext){
        let folderObject = Folder {
          id: object::new(ctx),
          transcript,
          intended_address
        };
        event::emit(TranscriptRequestEvent {
          wrapper_id: object::uid_to_inner(&folderObject.id),
          requester: tx_context::sender(ctx),
          intended_address,
        });
        //We transfer the wrapped transcript object directly to the intended address
        transfer::transfer(folderObject, intended_address);
      }
    }
```


```

在 Sui explorer 中，我们可以看到发出 event 的信息会像下面那样陈列。我们在 `TranscriptRequestEvent` event 中定义的三条数据属性都被展示了出来:

![Custom Event](../images/customevent.png)

**这里能找到这个成绩记录单示例项目的完整代码:[transcript.move](../example_projects/transcript/sources/transcript.move)**

尝试使用 Sui CLI 客户端去创建，请求和解包成绩记录单，然后用 Sui explorer 去检验结果。

第二单元到这里结束。如果你完整跟到这里，干得不错！

[ ](../../unit-two/lessons/6_capability_设计模式.html "Previous chapter") [ ](../../unit-three/index.html "Next chapter")

[ ](../../unit-two/lessons/6_capability_设计模式.html "Previous chapter") [ ](../../unit-three/index.html "Next chapter")

# index.html
# Sui Move导论

[ ](../print.html "Print this book")

[ ](../unit-two/lessons/7_events.html "Previous chapter") [ ](../unit-three/lessons/1_sui_framework.html "Next chapter")

[ ](../unit-two/lessons/7_events.html "Previous chapter") [ ](../unit-three/lessons/1_sui_framework.html "Next chapter")

# 1_sui_framework.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Sui Framework](#sui-framework)

智能合约的一个常见用例是发行自定义同质化代币（例如以太坊上的 ERC-20 代币）。让我们来看看如何使用 Sui 框架在 Sui 上完成这项工作，以及一些经典同质化代币的变体。

## [Sui Framework](#sui-framework-1)

[Sui Framework](https://github.com/MystenLabs/sui/tree/main/crates/sui-framework/docs)是Sui对Move VM功能的具体实现。 它包含 Sui 的原生 API，Move 标准库的实现，以及 Sui 特定的操作，例如[密码原语](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/groth16.md)和 Sui 在Framework级别的[数据结构](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/url.md)的实现。

Sui 中自定义同质化代币的实现将大量使用 Sui Framework中的一些库。

## [**`sui::coin`**](#suicoin)

我们将在 Sui 上实现自定义同质化代币使用主要的库是 [sui::coin](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/coin.md) 模块。

我们将在同质化代币示例中直接使用的资源或方法是：

资源：[Coin](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/coin.md#resource-coin)

资源：[TreasuryCap](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/coin.md#resource-treasurycap)

资源：[CoinMetadata](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/coin.md#resource-coinmetadata)

方法：[coin::create_currency](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/coin.md#function-create_currency)

介绍一些新概念后，我们将更深入地重新审视以上内容。

[ ](../../unit-three/index.html "Previous chapter") [ ](../../unit-three/lessons/2_intro_to_generics.html "Next chapter")

[ ](../../unit-three/index.html "Previous chapter") [ ](../../unit-three/lessons/2_intro_to_generics.html "Next chapter")

# 2_intro_to_generics.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Generics 范型](#generics-范型)

Generics 在计算机术语中被称为范型，引用 [Rust Book](https://doc.rust-lang.org/stable/book/ch10-00-generics.html) 对于泛型的定义： _泛型是具体类型或其他属性的抽象替代品_ 。范型使得在编写 Sui Move 代码时提供更强的灵活性，并避免逻辑重复。

实际上，泛型允许我们只编写单个函数，写一套逻辑，而应用于任何类型上。所以这种函数也被称为模板 ——个可以应用于任何类型的模板处理程序。

范型是 Sui Move 中的一个关键概念，理解并对其工作原理保持直觉非常重要，因此请花点时间阅读本节并充分理解每个部分。

## [**范型用法**](#范型用法)

### [在架构中使用范型](#在架构中使用范型)

看一个基本示例，了解如何使用 Generics 创建一个可以容纳 Sui Move 中任何类型的容器 `Box` 。

首先，在没有范型的情况下，我们可以定义一个包含 `u64` 类型的 `Box`，如下所示：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module Storage {
      public struct Box {
        value: u64
      }
    }
    }
```


```

但是，这种类型只能保存 u64 类型的值，为了能够存储其他类型显然我们不能把所有类型的box都枚举完，所以这个时候就需要使用泛型。 代码将修改如下：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module Storage {
      public struct Box<T> {
        value: T
      }
    }
    }
```


```

#### [能力限制](#能力限制)

我们可以添加条件去强制传递给泛型的类型必须具有某些能力。 语法如下所示：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module Storage {
      // T must be copyable and droppable 
      public struct Box<T: store + drop> has key, store {
        value: T
      }
    }
    }
```


```

💡这里需要注意的是，由于外部容器类型，上例中的内部类型 T 必须满足一定的能力约束。 在这个例子中，`T` 必须有 `store`，因为 `Box` 有 `store` 和 `key`。 但是，`T` 也可以具有容器没有的能力，如本例中的 `drop`。

直觉是，如果允许容器包含一个不遵循它所遵循的相同规则的类型，容器将违反其自身的能力。 如果盒子里的东西不能被储存，那盒子怎么能被储存呢？

我们将在下一节中看到，在某些情况下，可以使用一种称为`phantom` 的特殊关键字来绕过此规则。

_💡有关泛型类型的一些示例，请参阅`example_projects`下的_ [泛型项目](../example_projects/generics) _。_

### [在函数中使用Generics](#在函数中使用generics)

要编写一个返回 Box 实例的函数，该实例可以为 value 字段接受任何类型的参数，我们还必须在函数定义中使用泛型。 该函数可以定义如下：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public fun create_box<T>(value: T): Box<T> {
        Box<T> { value }
      }
    }
```


```

如果我们想限制函数只接受特定类型的 value，我们只需在函数签名中指定该类型，如下所示：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public fun create_box(value: u64): Box<u64> {
        Box<u64>{ value }
      }
    }
```


```

这将只接受 u64 类型的输入，为了使用 `create_box` 的方法，同时仍然使用同样的泛型 Box 结构。

#### [使用Generics调用函数](#使用generics调用函数)

要调用带有包含泛型的签名的函数，我们必须在方括号中指定类型，如以下语法所示：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    // value will be of type Storage::Box<bool>
      let bool_box = Storage::create_box<bool>(true);
    // value will be of the type Storage::Box<u64>
      let u64_box = Storage::create_box<u64>(1000000);
    }
```


```

#### [使用运用Sui CLI的Generics调用函数](#使用运用sui-cli的generics调用函数)

要从 Sui CLI 调用其签名中带有泛型的函数，您必须使用标志 `--type-args` 定义参数的类型。

以下示例调用 `create_box` 函数创建一个盒子，其中包含 `0x2::sui::SUI` 类型的硬币：

```

    
    
    sui client call --package $PACKAGE --module $MODULE --function "create_box" --args $OBJECT_ID --type-args "0x2::coin::Coin<0x2::sui::SUI>" --gas-budget 10000
    
```

## [高级 Generics 语法](#高级-generics-语法)

有关 Sui Move 中涉及使用的更多的泛型高级语法，例如多个泛型类型，请参阅 [Move Book 中关于 Generic 的部分](https://move-book.com/advanced-topics/understanding-generics.html)。

但是对于我们当前关于同质化代币的课程，您已经足够了解泛型是如何运行的。

[ ](../../unit-three/lessons/1_sui_framework.html "Previous chapter") [ ](../../unit-three/lessons/3_witness_design_pattern.html "Next chapter")

[ ](../../unit-three/lessons/1_sui_framework.html "Previous chapter") [ ](../../unit-three/lessons/3_witness_design_pattern.html "Next chapter")

# 3_witness_design_pattern.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [The Witness Design Pattern](#the-witness-design-pattern)

接下来，我们需要了解 witness 设计模式，来明确同质化代币在 Sui Move 中是如何实现的。

witness是一种设计模式，用于证明有关的一个资源或类型 `A`，在短暂的 `witness` 资源被消耗后只能启动一次。

`witness` 资源在使用后必须立即被消耗或丢弃，确保它不能被重复使用以创建 `A` 的多个实例。

## [Witness Pattern Example](#witness-pattern-example)

在下面的例子中，`witness` 资源是 `PEACE`，而我们要控制实例化的 `A` 类型是 `Guardian`。

`witness`资源类型必须有`drop`关键字，这样这个资源在被传入一个函数后可以被丢弃。我们看到`PEACE`资源的实例被传递到`create_guardian`方法中并被丢弃（注意 `witness` 前的下划线），确保只能创建一个 `Guardian` 的实例。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// Module that defines a generic type `Guardian<T>` which can only be
      /// instantiated with a witness.
      module witness::peace {
        use sui::object::{Self, UID};
        use sui::transfer;
        use sui::tx_context::{Self, TxContext};
        /// Phantom parameter T can only be initialized in the `create_guardian`
        /// function. But the types passed here must have `drop`.
      public struct Guardian<phantom T: drop> has key, store {
          id: UID
        }
        /// This type is the witness resource and is intended to be used only once.
        struct PEACE has drop {}
        /// The first argument of this function is an actual instance of the
        /// type T with `drop` ability. It is dropped as soon as received.
        public fun create_guardian<T: drop>(
          _witness: T, ctx: &mut TxContext
        ): Guardian<T> {
          Guardian { id: object::new(ctx) }
        }
        /// Module initializer is the best way to ensure that the
        /// code is called only once. With `Witness` pattern it is
        /// often the best practice.
        fun init(witness: PEACE, ctx: &mut TxContext) {
          transfer::transfer(
            create_guardian(witness, ctx),
            tx_context::sender(ctx)
          )
        }
      }
    }
```


```

_上面的例子是从 _[Damir Shamanaev](https://github.com/damirka)_ 的优秀书籍 _[Sui Move by Example](https://examples.sui.io/patterns/witness.html)_ 中修改的。_

## [phantom 关键字](#phantom-关键字)

在上面的例子中，我们希望 `Guardian` 类型具有 `key` 和 `store` 的能力，这样它就是一个资产，可以转移并在全局存储中持续存在。

我们还想把 `witness` 资源 `PEACE` 传入 `Guardian`，但 `PEACE` 只有 `drop` 的能力。回顾我们之前关于[能力约束](https://github.com/sui-foundation/sui-move-intro-course/blob/main/unit-three/lessons/2_intro_to_generics.md#ability-constraints)和内部类型的讨论，该规则暗示 `PEACE` 也应该有 `key` 和 `storage`，因为外部类型 `Guardian` 有。但是在这种情况下，我们不想给我们的 `witness` 类型添加不必要的能力，因为这样做可能会导致不符合预期的行为和漏洞。

我们可以使用关键字 `phantom` 来解决这种情况。当一个类型参数没有在结构定义中使用，或者它只是作为另一个 `phantom` 类型参数的参数使用时，我们可以使用 `phantom` 关键字来要求Move类型系统放松对内部类型的能力约束规则。我们看到 `Guardian` 在它的任何字段中都没有使用 `T` 类型，所以我们可以安全地声明 `T` 是一个 `phantom` 类型。

关于 `phantom` 关键字的更深入解释，请查看Move语言文档的[相关章节](https://github.com/move-language/move/blob/main/language/documentation/book/src/generics.md#phantom-type-parameters)。

## [One Time Witness](#one-time-witness)

一次性见证 One Time Witness（OTW）是Witness模式的一个子模式，我们利用模块 `init` 函数来确保只创建一个 `witness` 资源的实例（所以 `A` 类型被保证是唯一的）。

在Sui Move中，如果一个类型的定义具有以下属性，那么它就被认为是一个OTW。

  * 该类型是以模块的名字命名的，但大写字母。
  * 该类型只具有`drop`的能力



为了得到这个类型的实例，你需要把它作为第一个参数添加到模块的 `init` 函数中，如上例。然后Sui运行时将在模块发布时自动生成OTW结构。

上面的例子使用一次性见证设计模式来保证 `Guardian` 是一个单例。

[ ](../../unit-three/lessons/2_intro_to_generics.html "Previous chapter") [ ](../../unit-three/lessons/4_the_coin_resource_and_create_currency.html "Next chapter")

[ ](../../unit-three/lessons/2_intro_to_generics.html "Previous chapter") [ ](../../unit-three/lessons/4_the_coin_resource_and_create_currency.html "Next chapter")

# 4_the_coin_resource_and_create_currency.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [`Coin` 资源和 `create_currency` 方法](#coin-资源和-create_currency-方法)

现在，我们知道了泛型和见证模式是如何工作的，让我们再来看看 `Coin` 资源和 `create_currency` 方法。

## [`Coin` 资源](#coin-资源)

现在我们了解了泛型是如何工作的，我们可以重新审视一下 "sui::coin "中的 "Coin "资源。它被[定义](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/sui-framework/sources/coin.move#L26)为以下内容。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public struct Coin<phantom T> has key, store {
        id: UID,
        balance: Balance<T>
      }
    }
```


```

`Coin`资源类型是一个结构，有一个通用类型`T`和两个字段，`id`和`balance` 。`id`是`sui::object::UID`类型，我们之前已经看到过了。

`balance`是`sui::balance::Balance`类型，并且[定义](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/sui-framework/sources/balance.move#L32)为：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public struct Balance<phantom T> has store {
      value: u64
    }
    }
```


```

回顾我们关于[phantom](https://github.com/sui-foundation/sui-move-intro-course/blob/main/unit-three/lessons/3_witness_design_pattern.md#the-phantom-keyword)讨论，`T`类型在`Coin`中只作为`Balance`的另一个phantom类型的参数，而在`Balance`中，它没有用于任何字段，因此`T`是一个`phantom`类型参数。

`Coin<T>`服务于可转移的资产表示，即一定数量的同质化代币类型`T`，可以在地址之间转移或被智能合约函数调用消耗。

## [`create_currency` 方法](#create_currency-方法)

让我们看看 `coin::create_currency` 在其[源代码](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/sui-framework/sources/coin.move#L235)中实际做了什么：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
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
        // Emit Currency metadata as an event.
        event::emit(CurrencyCreated<T> {
          decimals
        });
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
    }
```


```

该语句使用Sui框架中的[sui::types::is_one_time_witness](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/sui-framework/sources/types.move) 方法检查传入的`witness`资源是否是一次性见证。

该方法创建并返回两个对象，一个是`TreasuryCap`资源，另一个是`CoinMetadata`资源。

### [`TreasuryCap`](#treasurycap)

`TreasuryCap`是一种资产，通过一次性见证模式保证是一个单体对象：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// Capability allowing the bearer to mint and burn
      /// coins of type `T`. Transferable
    public struct TreasuryCap<phantom T> has key, store {
          id: UID,
          total_supply: Supply<T>
        }
    }
```


```

它包装了一个类型为`balance::Supply`的singtleton字段`total_supply`：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    /// A Supply of T. Used for minting and burning.
      /// Wrapped into a `TreasuryCap` in the `Coin` module.
      struct Supply<phantom T> has store {
        value: u64
      }
    }
```


```

`Supple<T>`跟踪当前正在流通的`T`类型的同质化代币的发行总量。你可以看到为什么这个字段必须是一个单体，因为为一个代币类型拥有多个`supply`实例是没有意义的。

### [`CoinMetadata`](#coinmetadata)

这是一个存储已创建的同质化代币的元数据的资源。它包括以下字段。

  * `decimals`: 自定义同质化代币的精度
  * `name`：自定义同质化代币的名称
  * `symbol`：自定义同质化代币的代币符号
  * `description`: 自定义同质化代币的描述
  * `icon_url`: 自定义同质化代币的图标文件的网址



`CoinMetadata`中包含的信息可以被认为是Sui的基本和轻量级的同质化代币标准，可以被钱包和浏览器用来显示使用`sui::coin`模块创建同质化代币。

[ ](../../unit-three/lessons/3_witness_design_pattern.html "Previous chapter") [ ](../../unit-three/lessons/5_managed_coin.html "Next chapter")

[ ](../../unit-three/lessons/3_witness_design_pattern.html "Previous chapter") [ ](../../unit-three/lessons/5_managed_coin.html "Next chapter")

# 5_managed_coin.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Managed Coin 案例](#managed-coin-案例)

我们已经深入了解了 `sui::coin` 模块，现在我们可以来看一个简单但是完整的案例，即创造一种由可信任管理员 mint 和 burn 的自定义的同质化代币，与许多 ERC-20 代币实现相似。

## [智能合约](#智能合约)

你可以在项目案例文件夹中找到完整的 [Managed Coin 案例合约](../example_projects/fungible_tokens/sources/managed.move)。

根据我们目前课程所涵盖的内容，这份合约应该很容易理解。它完全遵守 [One Time Witness](3_witness_design_pattern.html#one-time-witness) 模式，`witness` 资源就叫 `MANAGED`，由 `init` 功能模块自动创建。

然后，`init` 功能通过调用 `coin::create_currency` 获取 `TreasuryCap` 和 `CoinMetadata` 资源，得到的参数就是 `CoinMetadata` 对象的字段，包括代币名称、符号、图标地址等。

通过 `transfer::freeze_object` 方法创建后，`CoinMetadata` 立刻就冻结了，之后就变成可以被任何地址读取的[不可变共享对象](../../unit-two/lessons/2_%E6%89%80%E6%9C%89%E6%9D%83.html)。

`TreasuryCap` [Capability object](https://github.com/RandyPen/sui-move-intro-course-zh/blob/main/unit-two/lessons/6_capability_%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F.md) 被用作控制调用 `mint` 和 `burn` 方法的一种方式，`mint` 和 `burn` 分别可以铸造和燃烧 `Coin<MANAGED>` object。

## [发布和 CLI 测试](#发布和-cli-测试)

### [发布 Module](#发布-module)

在 [fungible_tokens](https://github.com/sui-foundation/sui-move-intro-course/blob/main/unit-three/example_projects/fungible_tokens) 项目文件夹下，运行：

```

    
    
    sui client publish --gas-budget 30000
    
```

你可以看到控制台输出类似下面的内容：

![Publish Output](../images/publish.png)

创建的两个不可变对象分别是 package 本身和 Managed Coin 的 CoinMetadata object，交易发送人收到并拥有的对象就是 Managed Coin 的 TreasuryCap对象。

![Treasury Object](../images/treasury.png)

Export the object ID's of the package object and the `TreasuryCap` object to environmental variables:

分别输出 package 对象和 TreasuryCap对象的ID地址到环境变量：

```

    
    
    export PACKAGE_ID=<package object ID from previous output>
    export TREASURYCAP_ID=<treasury cap object ID from previous output>
    
```

### [铸造代币](#铸造代币)

要铸造一些 `MNG` 代币，我们可以使用如下 CLI 命令：

```

    
    
    sui client call --function mint --module managed --package $PACKAGE_ID --args $TREASURYCAP_ID \"<amount to mint>\" <recipient address> --gas-budget 3000
    
```

*💡注意：Sui的二进制版本 0.21.0 中，*_u64_ _输入必须被转义成字符串，也就是上面 CLI 命令格式。这在以后的版本中可能会有所改变。_

![Minting](../images/minting.png)

输出新铸造的 `COIN<MANAGED>` 对象的 ID 地址给 bash 变量：

```

    
    
    export COIN_ID=<coin object ID from previous output>
    
```

确认 `TreasuryCap<MANAGED>` 对象下 `Supply` 字段应该增加了所铸造的数量。

### [Burning Tokens](#burning-tokens)

要销毁一个现存的 `COIN<MANAGED>` 对象，我们使用如下 CLI 命令：

```

    
    
    sui client call --function burn --module managed --package $PACKAGE_ID --args $TREASURYCAP_ID $COIN_ID --gas-budget 3000
    
```

![Burning](../images/burning.png)

确认 `TreasuryCap<MANAGED>` 对象下 `Supply` 字段应该变回 `0`。

_练习：fungible token需要的其他一些普遍使用的功能是什么？你现在已经非常了解 MOVE 编程，尝试去实现这些功能吧。_

[ ](../../unit-three/lessons/4_the_coin_resource_and_create_currency.html "Previous chapter") [ ](../../unit-three/lessons/6_unit_testing.html "Next chapter")

[ ](../../unit-three/lessons/4_the_coin_resource_and_create_currency.html "Previous chapter") [ ](../../unit-three/lessons/6_unit_testing.html "Next chapter")

# 6_unit_testing.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [单元测试](#单元测试)

[ ](../../unit-three/lessons/5_managed_coin.html "Previous chapter") [ ](../../unit-four/index.html "Next chapter")

[ ](../../unit-three/lessons/5_managed_coin.html "Previous chapter") [ ](../../unit-four/index.html "Next chapter")

# index.html
# Sui Move导论

[ ](../print.html "Print this book")

[ ](../unit-three/lessons/6_unit_testing.html "Previous chapter") [ ](../unit-four/lessons/1_homogeneous_collections.html "Next chapter")

[ ](../unit-three/lessons/6_unit_testing.html "Previous chapter") [ ](../unit-four/lessons/1_homogeneous_collections.html "Next chapter")

# 1_homogeneous_collections.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Homogeneous Collections](#homogeneous-collections)

在深入探讨在 Sui 上构建市场这一主题之前，让我们先了解一下 Move 中的集合。

## [Vectors](#vectors)

Move 中的 `Vector` 类似于其他语言（如 C++）中的 Vector。它是一种在运行时动态分配内存并管理一组单一类型的方法，可以是特定类型或[通用类型](../../unit-three/lessons/2_intro_to_generics.html)。

需要注意的是，用泛型类型定义的向量可以接受任意类型的对象，集合中的所有对象仍然必须是相同类型，也就是说，集合是同质的。

### [创建vector](#创建vector)

任何类型的向量都可以通过 `vector` 字面量和vector的API创建。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    vector<T>[]: vector<T>
    vector<T>[e1, ..., en]: vector<T>
    }
```


```

一个简单的示例：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    const A: vector<u8> = vector[0u8, 1u8, 2u8];
    const B: vector<bool> = vector<bool>[false];
    (vector[]: vector<bool>);
    (vector[0u8, 1u8, 2u8]: vector<u8>);
    (vector<u128>[]: vector<u128>);
    (vector<address>[@0x42, @0x100]: vector<address>);
    }
```


```

下面是一个自定义类型的vector，并封装了相关操作函数，请参阅包含的示例代码以 `vector` 的定义以及其基本操作。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module collection::vector {
      use std::vector;
      public struct Widget {
      }
      // Vector for a specified type
      public struct WidgetVector {
        widgets: vector<Widget>
      }
      // Vector for a generic type 
      public struct GenericVector<T> {
        values: vector<T>
      }
      // Creates a GenericVector that hold a generic type T
      public fun create<T>(): GenericVector<T> {
        GenericVector<T> {
          values: vector::empty<T>()
        }
      }
      // Push a value of type T into a GenericVector
      public fun put<T>(vec: &mut GenericVector<T>, value: T) {
        vector::push_back<T>(&mut vec.values, value);
      }
      // Pops a value of type T from a GenericVector
      public fun remove<T>(vec: &mut GenericVector<T>): T {
        vector::pop_back<T>(&mut vec.values)
      }
      // Returns the size of a given GenericVector
      public fun size<T>(vec: &mut GenericVector<T>): u64 {
        vector::length<T>(&vec.values)
      }
    }
    }
```


```

更多可以通过movebook查看

## [Table](#table)

`Table` 是一个映射类的集合，可以动态存储键值对。但与传统的映射集合不同，它的键和值不存储在 `Table` 值中，而是使用 Sui 的对象系统存储。该 `Table` 结构仅充当对象系统的句柄以检索这些键和值。

`Table` 中一个 `key` 的类型必须具有 `copy + drop + store` 的能力约束，并且 `value` 类型必须具有 `store` 的能力约束。

`Table` 也是一种 _同构集合_ 类型，其中键和值字段可以指定或泛型类型，但集合中的所有值和所有键 `Table` 必须是相同的 _类型_ 。

_测验：用运算符检查包含完全相同的键值对的两个表对象是否彼此相等`===`？试试看。_

有关使用集合的信息，请参见以下示例`Table`：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module collection::table {
      use sui::table::{Table, Self};
      use sui::tx_context::{TxContext};
      // Defining a table with specified types for the key and value
      public struct IntegerTable {
        table_values: Table<u8, u8>
      }
      // Defining a table with generic types for the key and value 
      public struct GenericTable<phantom K: copy + drop + store, phantom V: store> {
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

[ ](../../unit-four/index.html "Previous chapter") [ ](../../unit-four/lessons/2_dynamic_fields.html "Next chapter")

[ ](../../unit-four/index.html "Previous chapter") [ ](../../unit-four/lessons/2_dynamic_fields.html "Next chapter")

# 2_dynamic_fields.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Dynamic Fields](#dynamic-fields)

为了了解像 Table 这样的集合在 Sui Move 中是如何实现的，我们需要引入 Sui Move 中动态字段的概念。动态字段是可以在运行时添加或删除的异构字段，并且可以具有任意用户分配的名称。

动态字段有两种子类型：

  * **动态字段** ：可以存储任何具有`store`能力的值，但是存储在这种字段中的对象将被视为被包装过（例如一个带有key能力的全局对象被嵌套进另一个结构体中），无法通过其直接访问通过外部工具（浏览器、钱包等）访问存储的 ID。
  * **动态对象字段** ：值必须是 Sui 对象（具有 `key` 和 `store` 能力，以及 `id: UID` 作为第一个字段），但仍然可以通过它们的对象 ID 直接访问被附上。



## [动态字段操作](#动态字段操作)

### [添加动态字段](#添加动态字段)

为了说明如何使用动态字段，我们定义了以下结构：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      // Parent struct
      public struct Parent has key {
        id: UID,
      }
      // Dynamic field child struct type containing a counter
      public struct DFChild has store {
        count: u64
      }
      // Dynamic object field child struct type containing a counter
      public struct DOFChild has key, store {
        id: UID,
        count: u64,
      }
    }
```


```

下面是用于向对象添加**动态字段** 或**动态对象字段** 的 API：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
     module collection::dynamic_fields {
       use sui::dynamic_object_field as ofield;
       use sui::dynamic_field as field;
      // Adds a DFChild to the parent object under the provided name
      public fun add_dfchild(parent: &mut Parent, child: DFChild, name: vector<u8>) {
        field::add(&mut parent.id, name, child);
      }
      // Adds a DOFChild to the parent object under the provided name
      public entry fun add_dofchild(parent: &mut Parent, child: DOFChild, name: vector<u8>) {
        ofield::add(&mut parent.id, name, child);
      } 
     }
    }
```


```

### [访问和改变动态字段](#访问和改变动态字段)

可以按如下方式读取或访问动态字段和动态对象字段：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      // Borrows a reference to a DOFChild
      public fun borrow_dofchild(child: &DOFChild): &DOFChild {
        child
      }
      // Borrows a reference to a DFChild via its parent object
      public fun borrow_dfchild_via_parent(parent: &Parent, child_name: vector<u8>): &DFChild {
        field::borrow<vector<u8>, DFChild>(&parent.id, child_name)
      }
      // Borrows a reference to a DOFChild via its parent object
      public fun borrow_dofchild_via_parent(parent: &Parent, child_name: vector<u8>): &DOFChild {
        ofield::borrow<vector<u8>, DOFChild>(&parent.id, child_name)
      }
    }
```


```

动态字段和动态对象字段也可以像下面这样改变：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      // Mutate a DOFChild directly
      public entry fun mutate_dofchild(child: &mut DOFChild) {
        child.count = child.count + 1;
      }
      // Mutate a DFChild directly
      public fun mutate_dfchild(child: &mut DFChild) {
        child.count = child.count + 1;
      }
      // Mutate a DFChild's counter via its parent object
      public entry fun mutate_dfchild_via_parent(parent: &mut Parent, child_name: vector<u8>) {
        let child = field::borrow_mut<vector<u8>, DFChild>(&mut parent.id, child_name);
        child.count = child.count + 1;
      }
      // Mutate a DOFChild's counter via its parent object
      public entry fun mutate_dofchild_via_parent(parent: &mut Parent, child_name: vector<u8>) {
        mutate_dofchild(ofield::borrow_mut<vector<u8>, DOFChild>(
          &mut parent.id,
          child_name,
        ));
      }
    }
```


```

_小测验：为什么`mutate_dofchild` 可以作为入口函数而不是 `mutate_dfchild` ？_

### [删除动态字段](#删除动态字段)

我们可以从其父对象中删除一个动态字段，如下所示：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      // Removes a DFChild given its name and parent object's mutable reference, and returns it by value
      public fun remove_dfchild(parent: &mut Parent, child_name: vector<u8>): DFChild {
        field::remove<vector<u8>, DFChild>(&mut parent.id, child_name)
      }
      // Deletes a DOFChild given its name and parent object's mutable reference
      public entry fun delete_dofchild(parent: &mut Parent, child_name: vector<u8>) {
        let DOFChild { id, count: _ } = ofield::remove<vector<u8>, DOFChild>(
          &mut parent.id,
          child_name,
        );
        object::delete(id);
      }
      // Removes a DOFChild from the parent object and transfer it to the caller
      public entry fun reclaim_dofchild(parent: &mut Parent, child_name: vector<u8>, ctx: &mut TxContext) {
        let child = ofield::remove<vector<u8>, DOFChild>(
          &mut parent.id,
          child_name,
        );
        transfer::transfer(child, tx_context::sender(ctx));
      }
    }
```


```

请注意，对于动态对象字段，我们可以在删除它与另一个对象的附件后删除或转移它，因为动态对象字段是一个 Sui 对象。但是我们不能对动态字段做同样的事情，因为它没有`key`能力，也不是 Sui 对象。

## [动态字段与动态对象字段](#动态字段与动态对象字段)

什么时候应该使用动态字段与动态对象字段？一般来说，我们希望在相关子类型具有`key`能力时使用动态对象字段，否则使用动态字段。有关根本原因的完整解释，请查看@sblackshear 的[此论坛帖子](https://forums.sui.io/t/dynamicfield-vs-dynamicobjectfield-why-do-we-have-both/2095) .

## [重温 `Table`](#重温-table)

现在我们了解了动态字段的工作原理，我们可以将 Table 集合视为动态字段操作的简单的封装。

您可以查看 Sui 中 `Table` 类型的[源代码](https://github.com/MystenLabs/sui/blob/eb866def280bb050838d803f8f72e67e05bf1616/crates/sui-framework/sources/table.move) 作为练习，并查看之前介绍的每个操作如何映射到动态字段操作，以及如何使用一些额外的逻辑来跟踪`Table`的大小。

[ ](../../unit-four/lessons/1_homogeneous_collections.html "Previous chapter") [ ](../../unit-four/lessons/3_heterogeneous_collections.html "Next chapter")

[ ](../../unit-four/lessons/1_homogeneous_collections.html "Previous chapter") [ ](../../unit-four/lessons/3_heterogeneous_collections.html "Next chapter")

# 3_heterogeneous_collections.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [Heterogeneous Collections](#heterogeneous-collections)

像 `Vector` 和 `Table` 这样的同类集合可以用于我们需要保存相同类型对象集合的市场，但是如果我们需要保存不同类型的对象，或者甚至如果我们在编译时不知道我们需要保存的对象是什么类型，那么又该怎么办呢？

对于这种类型的市场，我们需要使用一个异构的集合来保存要出售的商品。 已经完成了理解动态字段的工作原理，Sui 中的异构集合应该很容易理解。我们将在这里更仔细地研究 `Bag` 集合类型。

## [`Bag` 类型](#bag-类型)

`Bag` 是一个异构的类似映射的集合。 该集合类似于`Table`，因为它的键和值同样不存储在`Bag`值中，而是使用 Sui 的对象系统存储。 `Bag` 结构仅充当对象系统的句柄以检索这些键和值。

### [常见的`Bag`操作](#常见的bag操作)

常见的“Bag”操作示例代码如下：

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

如您所见，与 `Bag` 集合交互的函数签名与与 `Table` 集合交互的函数签名非常相似，主要区别在于在创建新的 `Bag` 时不需要声明任何类型，并 添加到其中的键值对类型不需要是相同的类型。

[ ](../../unit-four/lessons/2_dynamic_fields.html "Previous chapter") [ ](../../unit-four/lessons/4_marketplace_contract.html "Next chapter")

[ ](../../unit-four/lessons/2_dynamic_fields.html "Previous chapter") [ ](../../unit-four/lessons/4_marketplace_contract.html "Next chapter")

# 4_marketplace_contract.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [市场合约](#市场合约)

现在我们对各种类型的集合和动态字段的工作原理有了深入的了解，我们可以开始为链上市场编写合约，它可以支持以下功能：

  * 列出任意项目类型和数量
  * 接受自定义或本地可替代令牌类型的付款
  * 可以同时允许多个卖家列出他们的物品并安全地接收付款



## [类型定义](#类型定义)

首先，我们定义整体的“Marketplace”结构：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// A shared `Marketplace`. Can be created by anyone using the
      /// `create` function. One instance of `Marketplace` accepts
      /// only one type of Coin - `COIN` for all its listings.
      public struct Marketplace<phantom COIN> has key {
        id: UID,
        items: Bag,
        payments: Table<address, Coin<COIN>>
      }
    }
```


```

`Marketplace` 将是一个共享对象，任何人都可以访问和更改。 它接受一个 `COIN` 通用类型参数，该参数定义了接受付款的 [同质化代币](../../unit-three/lessons/4_the_coin_resource_and_create_currency.html) 类型。

`items` 字段将保存项目列表，它可以是不同的类型，因此我们在这里使用异构的 `Bag` 集合。

`payments` 字段将保存每个卖家收到的付款。 这可以用一个键值对来表示，其中卖家的地址作为键，接受的硬币类型作为值。 因为这里的key和value的类型是同构的，固定的，所以我们可以对这个字段使用`Table`集合类型。

_测验：您将如何修改此结构以接受多种可替代令牌类型？_

接下来，我们定义一个 `Listing` 类型：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// A single listing which contains the listed item and its
      /// price in [`Coin<COIN>`].
    public  struct Listing has key, store {
        id: UID,
        ask: u64,
        owner: address,
      }
    }
```


```

该结构仅包含我们需要的与项目列表相关的信息。 我们将直接将要交易的实际项目作为动态对象字段附加到 Listing 对象，因此我们不需要在此处显式定义任何项目字段或集合。

注意 `Listing` 具有 `key` 能力，这是因为当我们将它放入集合中时，我们希望能够使用它的对象 ID 作为键。

## [Listing and Delisting](#listing-and-delisting)

接下来，我们编写列出和删除项目的逻辑。 首先，列出一个项目：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// List an item at the Marketplace.
      public entry fun list<T: key + store, COIN>(
        marketplace: &mut Marketplace<COIN>,
        item: T,
        ask: u64,
        ctx: &mut TxContext
      ) {
        let item_id = object::id(&item);
        let listing = Listing {
          ask,
          id: object::new(ctx),
          owner: tx_context::sender(ctx),
        };
        ofield::add(&mut listing.id, true, item);
        bag::add(&mut marketplace.items, item_id, listing)
      }
    }
```


```

如前所述，我们将简单地使用动态对象字段接口附加任意类型的待售商品，然后我们将 `Listing` 对象添加到 listings 的 `Bag` 中，使用该商品的对象 id 作为 key 和实际的 Listing 对象作为值（这就是为什么 Listing 也有 store 的能力）。

对于下架，我们定义了以下方法：

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// Internal function to remove listing and get an item back. Only owner can do that.
      fun delist<T: key + store, COIN>(
        marketplace: &mut Marketplace<COIN>,
        item_id: ID,
        ctx: &mut TxContext
      ): T {
        let Listing {
          id,
          owner,
          ask: _,
        } = bag::remove(&mut marketplace.items, item_id);
        assert!(tx_context::sender(ctx) == owner, ENotOwner);
        let item = ofield::remove(&mut id, true);
        object::delete(id);
        item
      }
      /// Call [`delist`] and transfer item to the sender.
      public entry fun delist_and_take<T: key + store, COIN>(
        marketplace: &mut Marketplace<COIN>,
        item_id: ID,
        ctx: &mut TxContext
      ) {
        let item = delist<T, COIN>(marketplace, item_id, ctx);
        transfer::transfer(item, tx_context::sender(ctx));
      }
    }
```


```

注意下架的 `Listing` 对象是如何解包和删除的，以及通过 [ofield::remove](https://github.com/MystenLabs/sui/blob/e4c459ff522dc2077d3520f99b514e266935047a/crates/sui-framework/sources/dynamic_object_field.move#L67)请记住，Sui 资产不能在其定义模块之外销毁，因此我们必须将项目转移到 delister。

## [采购和付款](#采购和付款)

购买商品类似于下架，但具有处理付款的额外逻辑。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// Internal function to purchase an item using a known Listing. Payment is done in Coin<C>.
      /// Amount paid must match the requested amount. If conditions are met,
      /// owner of the item gets the payment and buyer receives their item.
      fun buy<T: key + store, COIN>(
        marketplace: &mut Marketplace<COIN>,
        item_id: ID,
        paid: Coin<COIN>,
      ): T {
        let Listing {
          id,
          ask,
          owner
        } = bag::remove(&mut marketplace.items, item_id);
        assert!(ask == coin::value(&paid), EAmountIncorrect);
        // Check if there's already a Coin hanging and merge `paid` with it.
        // Otherwise attach `paid` to the `Marketplace` under owner's `address`.
        if (table::contains<address, Coin<COIN>>(&marketplace.payments, owner)) {
          coin::join(
            table::borrow_mut<address, Coin<COIN>>(&mut marketplace.payments, owner),
            paid
          )
        } else {
          table::add(&mut marketplace.payments, owner, paid)
        };
        let item = ofield::remove(&mut id, true);
        object::delete(id);
        item
      }
      /// Call [`buy`] and transfer item to the sender.
      public entry fun buy_and_take<T: key + store, COIN>(
        marketplace: &mut Marketplace<COIN>,
        item_id: ID,
        paid: Coin<COIN>,
        ctx: &mut TxContext
      ) {
        transfer::transfer(
          buy<T, COIN>(marketplace, item_id, paid),
          tx_context::sender(ctx)
        )
      }
    }
```


```

第一部分与从列表中删除项目相同，但我们还会检查发送的付款金额是否正确。

第二部分将支付硬币对象插入到我们的`payments` `Table` 中，并且根据卖家是否已经有一些余额，它将执行一个简单的`table::add` 或`table::borrow_mut` 和 `coin::join` 将付款合并到现有余额中。

入口函数 `buy_and_take` 简单地调用 `buy` 并将购买的物品转移给买家。

### [收取利润](#收取利润)

最后，我们为卖家定义了从市场中收取余额的方法。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
      /// Internal function to take profits from selling items on the `Marketplace`.
      fun take_profits<COIN>(
        marketplace: &mut Marketplace<COIN>,
        ctx: &mut TxContext
      ): Coin<COIN> {
        table::remove<address, Coin<COIN>>(&mut marketplace.payments, tx_context::sender(ctx))
      }
      /// Call [`take_profits`] and transfer Coin object to the sender.
      public entry fun take_profits_and_keep<COIN>(
        marketplace: &mut Marketplace<COIN>,
        ctx: &mut TxContext
      ) {
        transfer::transfer(
          take_profits(marketplace, ctx),
          tx_context::sender(ctx)
        )
      }
    }
```


```

_Quiz：为什么我们不需要在这种市场设计下使用基于[Capability](../../unit-two/lessons/6_capability_design_pattern.html) 的访问控制？ 我们可以在这里实现能力设计模式吗？ 这会给市场带来什么特性？_

## [完整合同](#完整合同)

您可以在 [`example_projects/marketplace`](../example_projects/marketplace/sources/marketplace.move) 文件夹下找到我们实现的通用市场完整的智能合约。

[ ](../../unit-four/lessons/3_heterogeneous_collections.html "Previous chapter") [ ](../../unit-four/lessons/5_deployment_and_testing.html "Next chapter")

[ ](../../unit-four/lessons/3_heterogeneous_collections.html "Previous chapter") [ ](../../unit-four/lessons/5_deployment_and_testing.html "Next chapter")

# 5_deployment_and_testing.html
# Sui Move导论

[ ](../../print.html "Print this book")

# [部署和测试](#部署和测试)

接下来我们可以通过 SUI CLI 部署和测试我们的市场合约。

我们创建了一个简单的 `marketplace::widget` 模块，这样我们就可以创建一些项目供我们列出以帮助测试。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    module marketplace::widget {
      use sui::object::{Self, UID};
      use sui::transfer;
      use sui::tx_context::{Self, TxContext};
      public struct Widget has key, store {
        id: UID,
      }
      public entry fun mint(ctx: &mut TxContext) {
        let object = Widget {
          id: object::new(ctx)
        };
        transfer::transfer(object, tx_context::sender(ctx));
      }
    }
    }
```


```

这基本上是第一单元的 Hello World 项目，但变得更加简单。

## [部署](#部署)

首先我们发布两个包：

```

    
    
      sui client publish --gas-budget 3000
    
```

您应该会看到在资源管理器上发布的`marketplace`和`widget`模块：

![Publish](../images/publish.png)

将包对象 ID 导出到环境变量中：

```

    
    
    export PACKAGE_ID=<package object ID from previous output>
    
```

## [初始化市场](#初始化市场)

接下来，我们需要通过调用`create`入口函数来初始化市场合约。 我们想向它传递一个类型参数，以指定该市场将接受哪种类型的可替代代币。 在这里使用 `Sui` 原生标记是最简单的。 我们可以使用以下 CLI 命令：

```

    
    
    sui client call --function create --module marketplace --package $PACKAGE_ID --type-args 0x2::sui::SUI --gas-budget 1000
    
```

请注意为`SUI`令牌传递类型参数的语法。

将 Marketplace 共享对象的 ID 导出到环境变量中：

```

    
    
    export MARKET_ID=<marketplace shared object ID from previous output>
    
```

## [Listing](#listing)

首先，我们制作一个要列出的`widget`项目：

```

    
    
    sui client call --function mint --module widget --package $PACKAGE_ID --gas-budget 1000
    
```

将生成的`widget`的对象项保存到环境变量中：

```

    
    
    export ITEM_ID=<object ID of the widget item from console>
    
```

然后我们将这个项目列出到我们的市场：

```

    
    
    sui client call --function list --module marketplace --package $PACKAGE_ID --args $MARKET_ID $ITEM_ID 1 --type-args $PACKAGE_ID::widget::Widget 0x2::sui::SUI --gas-budget 1000
    
```

我们需要在这里提交两个类型参数，第一个是要列出的项目的类型，第二个是用于支付的可替代硬币类型。 上面的例子使用了 `1` 的标价。

提交本次交易后，您可以在[Sui explorer](https://explorer.sui.io/)查看新建的listing：

![Listing](../images/listing.png)

## [Purchase](#purchase)

拆分出一个金额为“1”的“SUI”币对象作为支付对象。 您可以使用 `sui client gas` CLI 命令查看您帐户下可用的 `SUI` 代币列表，然后选择一个进行拆分。

```

    
    
      sui client split-coin --coin-id <object ID of the coin to be split> --amounts 1 --gas-budget 1000
    
```

导出余额为“1”的新拆分的`SUI`币的对象ID：

```

    
    
      export PAYMENT_ID=<object ID of the split 1 balance SUI coin>
    
```

_测验：作为练习，修改市场合约以接受任何余额足以支付要价的付款，而不是要求确切的金额。_

现在，让我们买回刚刚列出的商品：

```

    
    
      sui client call --function buy_and_take --module marketplace --package $PACKAGE_ID --args $MARKET_ID $ITEM_ID $PAYMENT_ID --type-args $PACKAGE_ID::widget::Widget 0x2::sui::SUI --gas-budget 1000
    
```

提交此交易后，您应该会在控制台中看到一长串交易效果。 我们可以验证 `widget` 由我们的地址拥有，并且 `payments` `Table` 现在有一个带有我们地址键的条目，大小应该为 `1`。

### [Take Profits](#take-profits)

最后，我们可以通过调用`take_profits_and_keep`方法来领取我们的收益：

```

    
    
    sui client call --function take_profits_and_keep --module marketplace --package $PACKAGE_ID --args $MARKET_ID --type-args 0x2::sui::SUI --gas-budget 1000
    
```

这将从 `payments` `Table` 对象中获取余额并将其大小返回到 `0`。 在资源管理器上验证这一点。

[ ](../../unit-four/lessons/4_marketplace_contract.html "Previous chapter") [ ](../../advanced-topics/index.html "Next chapter")

[ ](../../unit-four/lessons/4_marketplace_contract.html "Previous chapter") [ ](../../advanced-topics/index.html "Next chapter")

# index.html
# Sui Move导论

[ ](../print.html "Print this book")

# [高级主题](#高级主题)

[ ](../unit-four/lessons/5_deployment_and_testing.html "Previous chapter") [ ](../advanced-topics/BCS_encoding/lessons/BCS_编码.html "Next chapter")

[ ](../unit-four/lessons/5_deployment_and_testing.html "Previous chapter") [ ](../advanced-topics/BCS_encoding/lessons/BCS_编码.html "Next chapter")

# BCS_编码.html
# Sui Move导论

[ ](../../../print.html "Print this book")

# [BCS 编码](#bcs-编码)

Binary Canonical Serialization, [BCS](https://github.com/diem/bcs), 是在 Diem 区块链项目中开发出来的序列化格式，现在也被广泛应用于大部分基于 Move 的区块链，比如Sui, Starcoin, Aptos, 0L. 除了在 Move VM 虚拟机中使用，BCS也被用在交易 transaction 和事件 event 编码中，比如在签署交易之前做序列化处理，解析事件数据。

如果你想深入了解Move的工作原理并成为Move专家，了解BCS的工作原理是至关重要的。让我们开启深入探讨。

## [BCS 特性说明](#bcs-特性说明)

在我们继续学习的过程中，有一些关于BCS编码的高级属性是值得记住的:

  * BCS是一种数据序列化格式，其生成的输出字节不包含任何类型信息。因此，接收编码字节的一方需要知道如何反序列化数据

  * BCS 中没有数据类型，当然也没有结构体 structs; struct 只是定义了内部字段 fields 被序列化的顺序

  * Wrapper 类型会被忽略掉，因此 `OuterType` 和 `UnnestedType` 会有同样的BCS表示:

```

    
    ```
    
    
        #![allow(unused)]
    fn main() {
    public struct OuterType {
      owner: InnerType
    }
    public struct InnerType {
      address: address
    }
    public struct UnnestedType {
      address: address
    }
    }
```


```

  * 包含泛型类型字段的类型可以被解析到第一个泛型类型字段。因此，如果泛型类型字段是自定义类型，并且需要进行序列化和反序列化操作，将泛型类型字段放在最后是一个好的实践方式。

```

    
    ```
    
    
        #![allow(unused)]
    fn main() {
    public struct BCSObject<T> has drop, copy {
      id: ID,
      owner: address,
      meta: Metadata,
      generic: T
    }
    }
```


```

在这个例子中，我们可以将所有数据反序列化直到`meta`字段。

  * 原始类型 primitive types（如无符号整数）以小端格式进行编码

  * 不定长向量 Vector 被序列化成一个表明包含向量 vector 长度的数字(最大取值是 `u32`), 后面跟着向量内的元素。[参考样例](https://github.com/diem/bcs#fixed-and-variable-length-sequences)都是采用[小端编码](https://en.wikipedia.org/wiki/LEB128)。




完整的BCS特性说明可以在 [BCS repository](https://github.com/diem/bcs) 里找到。

## [使用 `@mysten/bcs` NPM 库](#使用-mystenbcs-npm-库)

### [运行](#运行)

查看库文档[@mysten/bcs library](https://www.npmjs.com/package/@mysten/bcs). 运行环境是[deno](https://deno.land/), 无手动安装操作，直接导入即可。

```

    
    
    import { BCS, getSuiMoveConfig } from "npm:@mysten/bcs";
    
```

### [基础用例](#基础用例)

使用bcs库对一些简单数据做序列化和反序列化操作:

```

    
    
    import { BCS, getSuiMoveConfig } from "npm:@mysten/bcs";
    // initialize the serializer with default Sui Move configurations
    const bcs = new BCS(getSuiMoveConfig());
    // Define some test data types
    const integer = 10;
    const array = [1, 2, 3, 4];
    const string = "test string"
    // use bcs.ser() to serialize data
    const ser_integer = bcs.ser(BCS.U16, integer);
    const ser_array = bcs.ser("vector<u8>", array);
    const ser_string = bcs.ser(BCS.STRING, string);
    // use bcs.de() to deserialize data
    const de_integer = bcs.de(BCS.U16, ser_integer.toBytes());
    const de_array = bcs.de("vector<u8>", ser_array.toBytes());
    const de_string = bcs.de(BCS.STRING, ser_string.toBytes());
    
```

我们可以像上面的语法那样，用内置的默认设置`new BCS(getSuiMoveConfig())`来初始化Sui Move的序列化器实例。

BCS中有内置的枚举类型，如`BCS.U16`, `BCS.STRING`等，可以直接被当作 Sui Move 类型使用。对于[泛型类型](../../../unit-three/lessons/2_intro_to_generics.html)，可以使用与Sui Move相同的语法进行定义，例如上面的示例中的`vector<u8>`。

现在来仔细观察序列化和反序列化字段:

```

    
    
    # ints are little endian hexadecimals
    0a00
    10
    # the first element of a vector indicates the total length,
    # then it's just whatever elements are in the vector
    0401020304
    1,2,3,4
    # strings are just vectors of u8's, with the first element equal to the length of the string
    0b7465737420737472696e67
    test string
    
```

### [类型注册](#类型注册)

可以使用以下语法，来注册我们将要使用的自定义类型:

```

    
    
    import { BCS, getSuiMoveConfig } from "npm:@mysten/bcs";
    const bcs = new BCS(getSuiMoveConfig());
    // Register the Metadata Type
    bcs.registerStructType("Metadata", {
     name: BCS.STRING,
    });
    // Same for the main object that we intend to read
    bcs.registerStructType("BCSObject", {
     // BCS.ADDRESS is used for ID types as well as address types
     id: BCS.ADDRESS,
     owner: BCS.ADDRESS,
     meta: "Metadata",
    });
    
```

## [在 Sui 智能合约中使用 `bcs`](#在-sui-智能合约中使用-bcs)

继续使用上面 structs 的例子来进行演示。

### [定义 Struct](#定义-struct)

我们首先在 Sui Move 合约中定义与之前对应的 struct.

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    {
      //..
      struct Metadata has drop, copy {
        name: std::ascii::String
      }
      struct BCSObject has drop, copy {
        id: ID,
        owner: address,
        meta: Metadata
      }
      //..
    }
    }
```


```

### [反序列化](#反序列化)

现在，在 Sui 合约中写一个函数将一个 object 反序列化操作。

```

    
    
```
    
    
    
    #![allow(unused)]
    fn main() {
    public fun object_from_bytes(bcs_bytes: vector<u8>): BCSObject {
      // Initializes the bcs bytes instance
      let bcs = bcs::new(bcs_bytes);
      // Use `peel_*` functions to peel values from the serialized bytes. 
      // Order has to be the same as we used in serialization!
      let (id, owner, meta) = (
      bcs::peel_address(&mut bcs), bcs::peel_address(&mut bcs), bcs::peel_vec_u8(&mut bcs)
      );
      // Pack a BCSObject struct with the results of serialization
      BCSObject { id: object::id_from_address(id), owner, meta: Metadata {name: std::ascii::string(meta)} } }
    }
```


```

在 Sui [`bcs` 模块](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/docs/bcs.md)中，各种`peel_*`方法用于从BCS序列化的字节中"peel"出每个单独的字段。请注意，我们"peel"字段的顺序必须与结构定义中字段的顺序完全相同。

_测验: 为什么在对同一个`bcs`对象调用的前两个`peel_address`的结果不相同?_

还要注意我们如何使用辅助函数将类型从`address`转换为`id`，以及从`vector<8>`转换为`std::ascii::string`.

_测验: 如果`BSCObject`拥有的类型是`UID`而不是`ID`，会发生什么?_

## [补全 序列化/反序列化 示例](#补全-序列化反序列化-示例)

完整的 TypeScript 和 Sui Move 示例代码可以在[`example_projects`](../example_projects/)文件夹中找到。

首先，我们使用TypeScript程序序列化一个测试object:

```

    
    
    // We construct a test object to serialize, note that we can specify the format of the output to hex
    let _bytes = bcs
     .ser("BCSObject", {
      id: "0x0000000000000000000000000000000000000000000000000000000000000005",
      owner: "0x000000000000000000000000000000000000000000000000000000000000000a",
      meta: {name: "aaa"}
     })
     .toString("hex");
    
```

这次我们希望BCS writer的输出是十六进制格式，可以像上面那样指定。

将序列化结果的十六进制字符串添加前缀 `0x`, 并导出到一个环境变量中:

```

    
    
    export OBJECT_HEXSTRING=0x0000000000000000000000000000000000000000000000000000000000000005000000000000000000000000000000000000000000000000000000000000000a03616161
    
```

现在我们可以运行相关的Move单元测试来检查正确性:

```

    
    
    sui move test
    
```

你应该会在控制台中看到这个:

```

    
    
    BUILDING bcs_move
    Running Move unit tests
    [ PASS  ] 0x0::bcs_object::test_deserialization
    Test result: OK. Total tests: 1; passed: 1; failed: 0
    
```

或者我们可以发布该模块(并导出PACKAGE_ID), 然后使用上述BCS序列化的十六进制字符串调用 `emit_object` 方法:

```

    
    
    sui client call --function emit_object --module bcs_object --package $PACKAGE_ID --args $OBJECT_HEXSTRING --gas-budget 100000000
    
```

我们可以检查Sui Explorer上交易事务的 `Events` 选项卡，以查看emit输出的反序列化`BCSObject`是否正确:

![Event](../images/event.png)

[ ](../../../advanced-topics/index.html "Previous chapter") [ ](../../../advanced-topics/upgrade_packages/index.html "Next chapter")

[ ](../../../advanced-topics/index.html "Previous chapter") [ ](../../../advanced-topics/upgrade_packages/index.html "Next chapter")

# index.html
# Sui Move导论

[ ](../../print.html "Print this book")

[ ](../../advanced-topics/BCS_encoding/lessons/BCS_编码.html "Previous chapter") [ ](../../advanced-topics/upgrade_packages/lessons/1_合约升级.html "Next chapter")

[ ](../../advanced-topics/BCS_encoding/lessons/BCS_编码.html "Previous chapter") [ ](../../advanced-topics/upgrade_packages/lessons/1_合约升级.html "Next chapter")

# 1_合约升级.html
# Sui Move导论

[ ](../../../print.html "Print this book")

# [合约升级](#合约升级)

## [合约升级的本质](#合约升级的本质)

在之前课程的实践中，你会发现发布的合约 package 是不可变的 object, 不可撤回也无法修改。智能合约升级的本质是在新的地址上重新发布更新的合约，并且把旧版合约的数据迁移过去。

## [可以升级的内容](#可以升级的内容)

升级合约时做的代码修改需要满足以下条件：

  * 现有的 `public` 函数的输入输出参数格式保持不变
  * 可以添加新的 `struct` 和 函数
  * 可以给现有的 `struct` 添加新的能力
  * 可以把现有函数中对范型参数的约束去掉
  * 可以改变函数的实现
  * 可以修改非 `public` 函数的输入输出参数格式，包括 `friend` 和 `entry` 函数
  * 可以让非 `public` 函数变为 `public` 函数



## [注意](#注意)

### [init 函数](#init-函数)

init 函数只会在第一次发布合约时执行，后面升级合约时不会再次执行。

### [升级合约不会自动更新依赖](#升级合约不会自动更新依赖)

假定你的 package 有依赖一个外部的 package, 当所依赖 package 升级合约时，你的 package 不会自动把依赖关系调整为所依赖 package 升级后的合约地址，您必须明确地升级自己的 package, 以指向新的依赖项。

[ ](../../../advanced-topics/upgrade_packages/index.html "Previous chapter") [ ](../../../advanced-topics/upgrade_packages/lessons/2_数据迁移.html "Next chapter")

[ ](../../../advanced-topics/upgrade_packages/index.html "Previous chapter") [ ](../../../advanced-topics/upgrade_packages/lessons/2_数据迁移.html "Next chapter")

# 2_数据迁移.html
# Sui Move导论

[ ](../../../print.html "Print this book")

# [数据迁移](#数据迁移)

智能合约升级还涉及到数据迁移，将旧版合约中的数据移动到新版合约中来。如果没有数据迁移，另外重新发布一个合约也行。

## [共享的 Object](#共享的-object)

记录的数据通常会以共享的 object 存放，合约升级之后，新版和旧版的合约其实都还可以对该共享的 object 进行操作。 有的时候会因为函数不兼容或给共享 object 添加了动态属性导致不兼容造成出错。可以考虑给共享 object 添加版本属性，限制只允许新版的合约进行操作，打破共享 object 的往后兼容，强迫用户选择升级。

## [计数器合约升级](#计数器合约升级)

以一个计数器的案例来演示如何进行合约升级。

### [实现目标](#实现目标)

原始合约是使用了共享 object 来累加计数

```

    
    
    public entry fun increment(c: &mut Counter) {
      c.value = c.value + 1;
    }
    
```

升级后的合约添加了每累加 100 就发出 event 信息的功能

```

    
    
    struct Progress has copy, drop {
      reached: u64
    }
    public entry fun increment(c: &mut Counter) {
      c.value = c.value + 1;
      if (c.value % 100 == 0) {
        event::emit(Progress { reached: c.value });
      }
    }
    
```

只实现了原本计数功能的合约可以在这里找到[完整代码](../example_projects/counter/sources/counter_1.move_wip)。

### [修改合约以支持升级](#修改合约以支持升级)

对原本计数功能的合约进行修改，以支持后续合约升级中的数据迁移。

  1. 在合约当前 module 中使用常量 `VERSION` 记录当前的版本信息.

```

    
    
      const VERSION: u64 = 1;
    
```

  1. 在共享 object `Counter` 中添加新的 `version` 属性来记录当前共享 object 的版本信息。

```

    
    
      struct Counter has key {
        id: UID,
        // 2. Track the current version of the shared object
        version: u64,
        // 3. Associate the `Counter` with its `AdminCap`
        admin: ID,
        value: u64,
      }
    
```

  1. 让执行共享 object 版本升级数据迁移的操作成为专有操作，并只允许使用 `AdminCap` 来调用。

```

    
    
      /// Not the right admin for this counter
      const ENotAdmin: u64 = 0;
      fun init(ctx: &mut TxContext) {
        let admin = AdminCap {
          id: object::new(ctx),
        };
        transfer::share_object(Counter {
          id: object::new(ctx),
          version: VERSION,
          admin: object::id(&admin),
          value: 0,
        });
        transfer::transfer(admin, tx_context::sender(ctx));
      }
    
```

  1. 确保所有调用了共享 object 的 entry 函数都会检查，确保共享 object 的版本属性 `version` 与合约版本 `VERSION` 一致。

```

    
    
      /// Calling functions from the wrong package version
      const EWrongVersion: u64 = 1;
      public entry fun increment(c: &mut Counter) {
        // 4. Guard the entry of all functions that access the shared object with a version check.
        assert!(c.version == VERSION, EWrongVersion);
        c.value = c.value + 1;
      }
    
```

修改到可以支持升级的合约可以在这里找到[完整代码](../example_projects/counter/sources/counter_2.move_wip)。

这时候 package 配置文件 `Move.toml` 的格式是这样的

```

    
    
    [package]
    name = "sui_package"
    version = "0.0.0"
    [addresses]
    sui_package = "0x0"
    
```

使用 Sui CLI 发布合约

```

    
    
    sui client publish --gas-budget <GAS-BUDGET-AMOUNT>
    
```

并且记录下发布后的地址信息

![发布合约地址](../images/published_address_1.png)

`Immutable` 是合约发布地址，`Shared` 是该合约中共享的 object. 使用 [suiexplorer](https://suiexplorer.com/) 或者 

```

    
    
    sui client object <OBJECT-ID>
    
```

判别区分出`AdminCap`和`UpgradeCap`. 其中`AdminCap`在后续共享object数据迁移中用来管理权限，`UpgradeCap`是合约升级的关键权限，在[下一节定制升级权限](./3_%E5%AE%9A%E5%88%B6%E5%8D%87%E7%BA%A7%E6%9D%83%E9%99%90.html)做更详细讲解。

### [升级后的合约](#升级后的合约)

升级后的合约除了功能更新之外，还需要做以下额外修改。

  1. 增大 package 的`VERSION`.

```

    
    
      // 1. Bump the `VERSION` of the package.
      const VERSION: u64 = 2;
    
```

  1. 引入 `migrate` 函数来升级共享 object.

```

    
    
      // 2. Introduce a migrate function
      entry fun migrate(c: &mut Counter, a: &AdminCap) {
        assert!(c.admin == object::id(a), ENotAdmin);
        assert!(c.version < VERSION, ENotUpgrade);
        c.version = VERSION;
      }
    
```

需要注意的是，`migrate` 函数是非public的entry函数，这让 `migrate` 函数可以被 Sui CLI 或 SDK 调用，但无法被其他 module 调用。这种权限允许了将来升级的合约可以自由修改输入的参数格式。还做了 `AdminCap` 权限管理的检查以及确保合约先升级再进行共享object升级的检查。

在合约升级之后，共享的 object 并不会自动升级，还需要调用 `migrate` 函数升级。

其他功能更新

```

    
    
      struct Progress has copy, drop {
        reached: u64,
      }
      /// Not the right admin for this counter
      const ENotAdmin: u64 = 0;
      /// Migration is not an upgrade
      const ENotUpgrade: u64 = 1;
      /// Calling functions from the wrong package version
      const EWrongVersion: u64 = 2;
      public entry fun increment(c: &mut Counter) {
        assert!(c.version == VERSION, EWrongVersion);
        c.value = c.value + 1;
        if (c.value % 100 == 0) {
          event::emit(Progress { reached: c.value })
        }
      }
    
```

升级后的合约可以在这里找到[完整代码](../example_projects/counter/sources/counter.move)。

对配置文件 `Move.toml` 进行编辑，`<ORIGINAL-PACKAGE-ID>` 填写之前发布合约的地址。

```

    
    
    [package]
    name = "sui_package"
    version = "0.0.1"
    published-at = "<ORIGINAL-PACKAGE-ID>"
    [addresses]
    sui_package = "0x0"
    
```

运行合约升级命令行，其中`<UPGRADE-CAP-ID>`处填写`UpgradeCap`的Object ID.

```

    
    
    sui client upgrade --gas-budget <GAS-BUDGET-AMOUNT> --upgrade-capability <UPGRADE-CAP-ID>
    
```

运行结果如下，表明合约升级成功，其中出现的 `Immutable` 是升级后合约的发布地址。 ![合约升级结果](../images/upgrade_result_1.png)

[ ](../../../advanced-topics/upgrade_packages/lessons/1_合约升级.html "Previous chapter") [ ](../../../advanced-topics/upgrade_packages/lessons/3_定制升级权限.html "Next chapter")

[ ](../../../advanced-topics/upgrade_packages/lessons/1_合约升级.html "Previous chapter") [ ](../../../advanced-topics/upgrade_packages/lessons/3_定制升级权限.html "Next chapter")

# 3_定制升级权限.html
# Sui Move导论

[ ](../../../print.html "Print this book")

# [定制升级权限](#定制升级权限)

## [升级权限类型](#升级权限类型)

在先前的示范中，智能合约升级几乎可以把所有功能重写，这会让用户产生担心。 当然，Sui Move也提供了不同等级的合约升级权限。从宽松到收紧的程度罗列如下：

  * Compatible: 最宽松的权限。可以修改所有函数的实现。可以剔除函数对范型输入参数的能力约束。可以让非`public`的函数变为`public`. 可以修改、删除任意 `private`, `public(friend)` 和 `entry` 函数的输入输出参数。但除了能力约束之外不能修改`public`函数的输入输出参数。不能修改已有的类型。
  * Additive: 可以给 package 添加新的函数，比如新的 `public`函数和`struct`. 但不能对现有函数的代码做任何修改。
  * Dependency-only: 只能修改该 package 的依赖项。
  * Immutable: 无法再升级该 package .



最初发布合约的时候，得到的合约升级权限是最宽松的 Compatible. 可以调用 [package.move](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/sui-framework/sources/package.move) 模块中的 `only_additive_upgrades`, `only_dep_upgrades` 和 `make_immutable` 这三个 public entry 函数来单向收紧升级权限。

## [合约升级过程](#合约升级过程)

![合约升级过程](../images/Upgrade_process.png)

合约升级的过程可以理解为三个环节。

  1. Authorization: 使用`UpgradeCap`授权升级合约，生成一个`UpgradeTicket`.
  2. Execution: 虚拟机消费了这个`UpgradeTicket`并验证合约的字节码、与旧版合约的兼容性，在链上创建升级后合约object. 如果升级成功就会返回`UpgradeReceipt`.
  3. Commit: 根据返回的`UpgradeReceipt`更新`UpgradeCap`上关于新建的 package 信息。



其中第2步 Execution 是内部指令，第1步和第3步在 [package.move](https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/sui-framework/sources/package.move) 中使用函数实现。

```

    
    
    module sui::package {
      public fun authorize_upgrade(
        cap: &mut UpgradeCap,
        policy: u8,
        digest: vector<u8>
      ): UpgradeTicket;
      public fun commit_upgrade(
        cap: &mut UpgradeCap,
        receipt: UpgradeReceipt,
      );
    }
    
```

## [定制权限作用](#定制权限作用)

除了通过内置的命令行 `sui client upgrade` 调用默认的 authorization 和 commit 环节，还可以定制升级权限添加更多条件约束，比如投票、治理、允许清单、时间锁等功能。

[ ](../../../advanced-topics/upgrade_packages/lessons/2_数据迁移.html "Previous chapter")

[ ](../../../advanced-topics/upgrade_packages/lessons/2_数据迁移.html "Previous chapter")
