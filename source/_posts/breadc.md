---
mathjax: true
title: 为 cac / commander.js API 风格的 CLI 框架添加 TypeScript 类型推导
date: 2023-02-1 13:57:28
tags:
  - other
categories:
  - 乱七八糟
---

[cac](https://github.com/cacjs/cac) / [commander.js](https://github.com/tj/commander.js) 提供了一个简单直接的 API 来创建命令行应用，你只需要指定用户应该如何使用你的程序参数，就能生成出一个相应的 CLI 应用，包含参数传递，选项解析，版本和帮助命令的自动生成等等。

我从零实现了一个使用类似 cac / commander.js 的 API 风格的 CLI 应用框架 —— [Breadc](https://github.com/yjl9903/Breadc)，但是相比于 cac ：

* 添加了 TypeScript 类型推导
* 移除了部分类型不友好的参数解析
* 添加了多层 sub-commands 的实现

本文主要讲解 Bread 如何添加 TypeScript 类型推导，如何使用 Template Literal Type 进行类型体操。

<!--more-->

## 使用 Breadc 创建一个命令行 CLI 应用

首先，看以下示例代码如何使用 Breadc 创建 CLI 应用。

```TypeScript
import breadc from 'breadc'

const cli = breadc('echo', { version: '1.0.0' })
  .option('--host <host>', { default: 'localhost' })
  .option('-p, --port <port>', { default: '3000', cast: p => +p })

cli
  .command('[message]', 'Say something!')
  .option('--dev')
  .action((message, option) => {
    const host = option.host
    const port = option.port
    console.log(`Host: ${host}`)
    console.log(`Port: ${port}`)
  })

cli.run(process.argv.slice(2)).catch(err => console.error(err))
```

第 3-5 行，`breadc('echo')` 创建了一个 Breadc 对象，并注册了 `--host` 和 `-p, --port` 两个全局的接受字符串值的选项。这两个选项字符串的含义，其实就是我们运行 CLI 时如何进行使用，例如可以通过 `echo --host 1.1.1.1` ，`echo --port 3001` 这样的使用方式来向命令传递参数。

第 8 行，`cli.command('[message]', 'Say something!')` 创建了一个默认命令，接受一个可选参数 `message` 。例如你可以直接使用 `echo` ，那么 `message` 的值默认为空串；或者 `echo hello` ，那么 `message` 的值传递进 `hello`l

第 9 行，`cli.option('--dev')` 创建了一个名为 `dev` 的布尔值选项，默认为 `false`。

第 10 行，`cli.action((message, option) => { ... })` 指定运行这个命令后触发的回调函数，参数列表依次为 `cli.command()` 时指定的参数列表，然后接上一个对象表示收到的所有 `option` 。在上面的例子里，回调函数的第一个参数是可选参数 `[message]`，类型是 `string`，而第二个参数是选项对象，类型应该是 `{ host: string, port: string, dev: boolean, '--': string[] }`。

但实际上，在 cac 和未添加 TypeScript 支持扩展的 commander.js 中，都没有办法自动推导出回调函数的参数类型。而 Breadc，可以看到类型无需任何标注，已经被自动推导出来了：

这就是本文的目标，让 TypeScript 能够自动从上下文的 fluent builder API 提供的模板字符串中，推导出命令回调函数的参数类型列表！

> commander.js 的 TypeScript 类型推导扩展： [commander-js/extra-typings](https://github.com/commander-js/extra-typings) Breadc 最初版本实现的时候还没有这个东西，我撰写本文搜索资料时发现 commander.js 后来也实现和本文内容类似的推导功能。)

## Breadc 的 API

```TypeScript
interface Breadc {
  option(format: string): Breadc;
  command(format: string): Command;
  run(args: string[]): void;
}

interface Command {
  option(format: string): Command;
  action(fn: (...args: any[]) => any): void;
}
```

Breadc 简要的 fluent API 如上图所示，interface Breadc 表示我们整个命令行程序，interface Command 表示命令行程序的 sub-command 子命令。

通过 `Breadc.option(format: string): Breadc` 来添加一个全局的选项，通过 `Breadc.command(format: string): Command` 来添加一个 sub-command。所有创建工作完成后，使用 `Breadc.run(args: string[]): void` 运行命令行程序。

Command 也可以类似的通过 `Command.option(format: string): Command` 添加子命令自己的选项，通过 `Command.action(fn: (...args: any[]) => any): void` 添加子命令的回调函数。

之后，我们主要操作的就是这两个 interface，最终为 `action` 方法推导相应的类型参数。

## Option 模板字符串的类型推导

Option 支持两种输入形式，`--option` 表示一个 boolean 开关选项，`--option` 表示这个选项后面必须接一个字符串参数，同时还支持 option 的单字母缩写，`-o, --option` 使用 `-o` 或者 `--option` 都表示这个选项。

我们首先提取 Option 的类型，先看这个不考虑缩写的实现：

```TypeScript
export type ExtractOptionType<T extends string> =
  T extends `--${infer P} <infer Q>` ? string :
  T extends `--${infer P}` ? boolean :
  never;
```

如果参数模板字符串参数 T，形如 `--xxx` 这样子那么这个选项就是一个字符串选项，如果形如 `--xxx` ，则是一个布尔选项，否则用 `never` 表示啥也不是提取失败。

**注意：**这里两个东西判断顺序是不可以调换，因为 `--${infer P}` 这个 pattern 其实能够匹配 `--xxx` 形式的东西，即 `P: 'xxx '` ，这是不符合我们期望的。这个问题在后续的实现中，也会经常出现，因为很显然，TypeScript 在类型检查的时候，是按照顺序一条一条判断能否匹配的，在进行多条匹配的时候必须注意 patterns 之间的重叠关系。

于是，类似的可以写出一个考虑上单字母缩写的版本：

```TypeScript
export type ExtractOptionType<T extends string> =
  T extends `-${Letter}, --${infer R} <${infer U}>`
    ? string
    : T extends `-${Letter}, --${infer R}`
    ? boolean
    : T extends `--${infer R} <${infer U}>`
    ? string
    : T extends `--${infer R}`
    ? boolean
    : string | boolean; // fallback
```

其中 `Letter` 是一个辅助类型，表示所有小写英文字母，即 `type Letter = 'a' | 'b' | ... | 'z'`。

依葫芦画瓢，我们可以继续写出提取 Option 名称的类型：

```TypeScript
export type ExtractOptionName<T extends string> =
  T extends `-${Letter}, --${infer R} <${infer U}>`
    ? R
    : T extends `-${Letter}, --${infer R}`
    ? R
    : T extends `--${infer R} <${infer U}>`
    ? R
    : T extends `--${infer R}`
    ? R
    : string; // fallback
```

> 课后练习： `type FS: ExtractOptionName<'--allow-fs'> = 'allowFs'` 实现将 kebab case 转换为 camel case 的类型。

## Command 模板字符串的类型推导

一点小插曲，Breadc 最初版本实现时，可能由于使用的 VS Code 插件版本等未知问题，并没有支持递归类型，所以有了一个非递归的实现，已经足以应对大部分使用情况。

具体思路是，首先考虑最简单的情形，Command 的模板字符串只有一个东西（没有空格分割），有以下 5 种情况，按照重叠顺序依次匹配。

```JavaScript
export type ExtractCommand<T extends string> =
    T extends `[...${infer P1}]`
    ? [string[]]
    : T extends `[${infer P1}]`
    ? [string | undefined]
    : T extends `<${infer P1}>`
    ? [string]
    : T extends `${infer P1}`
    ? []
    : T extends ``
    ? []
    : never;
```

然后，Command 的模板字符串在大部分使用情况下，不会分出太多块（如果需要传递很长的参数列表，那么为什么不用 Option 呢，无需有记忆参数顺序的负担）。

因此可以比如暴力搜索一下长度不超过 2 的所有可能性，有如下的字符串匹配 pattern：

```TypeScript
export type ExtractArg<T extends string> =
    T extends `<${infer P1}> [...${infer P2}]`
    ? [string, string[]]
    : T extends `<${infer P1}> [${infer P2}]`
    ? [string, string | undefined]
    : T extends `<${infer P1}> <${infer P2}>`
    ? [string, string]
    : T extends `${infer P1} [...${infer P2}]`
    ? [string[]]
    : T extends `${infer P1} [${infer P2}]`
    ? [string | undefined]
    : T extends `${infer P1} <${infer P2}>`
    ? [string]
    : T extends `${infer P1} ${infer P2}`
    ? []
    : T extends `[...${infer P1}]`
    ? [string[]]
    : T extends `[${infer P1}]`
    ? [string | undefined]
    : T extends `<${infer P1}>`
    ? [string]
    : T extends `${infer P1}`
    ? []
    : T extends ``
    ? []
    : never;
```

展开了 5 层的版本：[https://github.com/yjl9903/Breadc/blob/573587e5960b55386ee476fbb372cf15950633e9/packages/breadc/src/types/extract.ts#L61-L159](https://github.com/yjl9903/Breadc/blob/573587e5960b55386ee476fbb372cf15950633e9/packages/breadc/src/types/extract.ts#L61-L159)

这个代码当然不可能是手写的，写了个代码生成这个长度的类型定义：[https://github.com/yjl9903/Breadc/blob/573587e5960b55386ee476fbb372cf15950633e9/examples/genType.ts](https://github.com/yjl9903/Breadc/blob/573587e5960b55386ee476fbb372cf15950633e9/examples/genType.ts)

生成匹配 pattern 时，考虑到方便 CLI parser 的实现，过滤掉了诸如 `cli [p1]` 这样可选参数放在前面的情况，强制指定可选的参数一定是放在最后的，这样方便最后 parser 的传递参数；并且减少了生成类型定义的状态总数，使得即使我们展开了 5 层，它的总状态数没有太过指数爆炸。

当然，非递归的版本虽然能够解决我们的问题，但总归是不够 robust，比如真的有很长的参数列表就会坏掉。所以在最新版本 4.9.5 的 TypeScript 上实验，已经可以使用如下的递归类型：

[https://www.typescriptlang.org/play?#code/KYDwDg9gTgLgBDAnmYcCiIZQIYGMYCCUA5gDwAqcoMwAdgCYDOcjWAlrcQHxwC8cAKDjC4lanSZwABqQAkAbw4AzYFDgAlAL48Fy1XACqmqXAD8cANqsoHYgBo4AOmcYseQiVIGuAXTgAuQRFRKkwJZildWhU1LTgomMNjM3RMHHwiMm8AoJExMIYIi2dHBP0tHxNzK3ZOCx8-QKE80JpC6Qsy2M1KlJqbTjgAHzgAVwZgJQ5gekbc4Xy2yRkujW0qy2tbOfqAbgEBJBQ4Aj5UtwzPAHJsOAAjOFwrrkPkVAAhM9d0jzIb+8ecFI9C4z1exwAwl80u5MqR-g9cECQUDgDwLEofGCjqgACLQi6-eG3RHInikNGWEqYsFAA](https://www.typescriptlang.org/play?#code/KYDwDg9gTgLgBDAnmYcCiIZQIYGMYCCUA5gDwAqcoMwAdgCYDOcjWAlrcQHxwC8cAKDjC4lanSZwABqQAkAbw4AzYFDgAlAL48Fy1XACqmqXAD8cANqsoHYgBo4AOmcYseQiVIGuAXTgAuQRFRKkwJZildWhU1LTgomMNjM3RMHHwiMm8AoJExMIYIi2dHBP0tHxNzK3ZOCx8-QKE80JpC6Qsy2M1KlJqbTjgAHzgAVwZgJQ5gekbc4Xy2yRkujW0qy2tbOfqAbgEBJBQ4Aj5UtwzPAHJsOAAjOFwrrkPkVAAhM9d0jzIb+8ecFI9C4z1exwAwl80u5MqR-g9cECQUDgDwLEofGCjqgACLQi6-eG3RHInikNGWEqYsFAA)

```TypeScript
export type ExtractArg<T extends string> = 
    T extends `<${infer R}> ${infer U}` ? [string, ...ExtractArg<U>] : 
    T extends `${infer R} ${infer U}` ? ExtractArg<U> : 
    T extends `[...${infer R}]` ? [string[]] : 
    T extends `[${infer R}]` ? [string | undefined] : 
    T extends `<${infer R}>` ? [string] : [];

type A = ExtractArg<'a b c'> // []
type B = ExtractArg<'a b c <d>'> // [string]
type C = ExtractArg<'a b c <d> <e> [f]'> // [string, string, string | undefined]
type D = ExtractArg<'a b c <d> <e> [...f]'>  // [string, string, string[]]
```

## 回调函数的类型推导
