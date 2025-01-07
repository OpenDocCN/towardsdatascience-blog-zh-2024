# 九个 Rust Cargo.toml 的 Wat 和 Wat Not

> 原文：[`towardsdatascience.com/nine-rust-cargo-toml-wats-and-wat-nots-1e5e02e41648?source=collection_archive---------1-----------------------#2024-07-24`](https://towardsdatascience.com/nine-rust-cargo-toml-wats-and-wat-nots-1e5e02e41648?source=collection_archive---------1-----------------------#2024-07-24)

## 掌握 Cargo.toml 格式规则，避免挫败感

[](https://medium.com/@carlmkadie?source=post_page---byline--1e5e02e41648--------------------------------)![Carl M. Kadie](https://medium.com/@carlmkadie?source=post_page---byline--1e5e02e41648--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1e5e02e41648--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e5e02e41648--------------------------------) [Carl M. Kadie](https://medium.com/@carlmkadie?source=post_page---byline--1e5e02e41648--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e5e02e41648--------------------------------) ·阅读时长 8 分钟 ·2024 年 7 月 24 日

--

![](img/811e179337d8b0229e8200ca753b3c86.png)

Rust Cargo 惊讶 — 来源: [`openai.com/dall-e-2/`](https://openai.com/dall-e-2/)。所有其他图表来自作者。

在 JavaScript 和其他语言中，我们称 [这种令人惊讶或不一致的行为为“Wat!”](https://www.destroyallsoftware.com/talks/wat) [即“什么!?”]。例如，在 JavaScript 中，一个空数组加上一个空数组会产生一个空字符串，`[] + [] === ""`。Wat!

在另一个极端，某些语言表现出令人惊讶的一致性。我称之为“Wat Not”。

Rust 通常（远远）比 JavaScript 更一致。然而，一些与 Rust 相关的格式却带来惊喜。具体来说，这篇文章将讨论 `Cargo.toml` 中的九个“wat”和“wat not”。

回想一下，`Cargo.toml` 是定义 Rust 项目配置和依赖项的清单文件。它的格式 TOML（Tom's Obvious, Minimal Language）表示嵌套的键/值对和/或数组。JSON 和 YAML 是类似的格式。Tom 设计 TOML 时，像 YAML 一样，但不同于 JSON，他的目标是让人类更容易阅读和写作。

这段包含九个“wat”和“wat not”的旅程可能不会像 JavaScript 的怪癖那样有趣（谢天谢地）。然而，如果你曾经觉得 `Cargo.toml` 的格式令人困惑，希望这篇文章能让你对自己感觉更好。而且，更重要的是，当你学习了这九个“wat”和“wat not”之后，希望你能更轻松、更高效地编写 `Cargo.toml`。

本文并不是要“修复” `Cargo.toml`。该文件格式在其主要用途上表现得非常出色：指定 Rust 项目的配置和依赖项。相反，本文的目的是帮助理解这种格式及其怪癖。

# 什么？1：依赖项与配置文件部分名称

你可能知道如何向`Cargo.toml`添加`[dependencies]`部分。这样的部分指定发布依赖项，例如：

```py
[dependencies]
serde = "1.0"
```

类似地，你可以通过`[dev-dependencies]`部分指定开发依赖，通过`[build-dependencies]`部分指定构建依赖。

你可能还需要设置编译器选项，例如优化级别和是否包含调试信息。你可以通过**配置文件**部分来设置这些选项，分别用于发布、开发和构建。你能猜出这三个部分的名称吗？是`[profile]`、`[dev-profile]`和`[build-profile]`吗？

不！是`[profile.release]`、`[profile.dev]`和`[profile.build]`。什么？

`[dev-profile]`比`[profile.dev]`更好吗？`[dependencies.dev]`比`[dev-dependencies]`更好吗？

我个人更喜欢带点的名称。（在“什么？不是 9”中，我们将看到点的强大功能。）不过，我也愿意记住依赖项和配置文件的工作方式不同。

# 什么？2：依赖继承

你可能会争辩说，点在配置文件中是可以的，但连字符在依赖项中更好，因为`[dev-dependencies]`继承自`[dependencies]`。换句话说，`[dependencies]`中的依赖项在`[dev-dependencies]`中也可以使用。那么，这是否意味着`[build-dependencies]`继承自`[dependencies]`？

不！`[build-dependencies]`并不继承自`[dependencies]`。什么？

我觉得这种`Cargo.toml`的行为既方便又令人困惑。

# 什么？3：默认键

你可能知道，这样写不好：

```py
[dependencies]
serde = { version = "1.0" }
```

你可以这样写：

```py
[dependencies]
serde = "1.0"
```

这里的原则是什么？在一般的 TOML 中，如何指定一个键为默认键？

你不能！一般的 TOML 没有默认键。什么？

Cargo TOML 对`[dependencies]`部分中的`version`键进行特殊处理。这是 Cargo 特有的功能，而不是一般的 TOML 功能。据我所知，Cargo TOML 没有其他默认键。

# 什么？4：子特性

通过`Cargo.toml`的`[features]`，你可以创建不同依赖项版本的项目。这些依赖项本身也可能在特性上有所不同，我们称之为子特性。

在这里，我们创建了项目的两个版本。默认版本依赖于`getrandom`，并使用默认特性。`wasm`版本依赖于`getrandom`，并使用`js`子特性：

```py
[features]
default = []
wasm = ["getrandom-js"]

[dependencies]
rand = { version = "0.8" }
getrandom = { version = "0.2", optional = true }

[dependencies.getrandom-js]
package = "getrandom"
version = "0.2"
optional = true
features = ["js"]
```

在这个示例中，`wasm`是我们项目的一个特性，依赖于别名`getrandom-rs`，它表示带有`js`子特性的`getrandom` crate 的版本。

那么，我们如何在避免冗长的`[dependencies.getrandom-js]`部分的同时，给出相同的规范呢？

在`[features]`中，将`getrandom-js`替换为`"getrandom/js"`。我们可以简单地写：

```py
[features]
default = []
wasm = ["getrandom/js"]

[dependencies]
rand = { version = "0.8" }
getrandom = { version = "0.2", optional = true }
```

什么？

通常，在`Cargo.toml`中，特性规范如`wasm = ["getrandom/js"]`可以列出

+   其他特性

+   依赖别名

+   依赖项

+   一个或多个依赖项“斜杠”一个子特性

这不是标准 TOML，而是`Cargo.toml`特有的简写。

奖励：猜猜你如何使用简写来表示 `wasm` 特性应该包括 `getrandom`，并且有两个子特性：`js` 和 `test-in-browser`？

答案：列出依赖项两次。

```py
wasm = ["getrandom/js","getrandom/test-in-browser"]
```

# Wat 5：目标的依赖项

我们已经看到如何为发布、调试和构建指定依赖项。

```py
[dependencies]
#...
[dev-dependencies]
#...
[build-dependencies]
#...
```

我们已经看到如何为各种特性指定依赖项：

```py
[features]
default = []
wasm = ["getrandom/js"]
```

你如何猜测我们为各种目标（例如某个版本的 Linux、Windows 等）指定依赖项呢？

我们使用 `target.*TARGET_EXPRESSION*` 来前缀 `[dependences]`，例如：

```py
[target.x86_64-pc-windows-msvc.dependencies]
winapi = { version = "0.3.9", features = ["winuser"] }
```

按照一般 TOML 的规则，这也意味着我们可以这样说：

```py
[target]
x86_64-pc-windows-msvc.dependencies={winapi = { version = "0.3.9", features = ["winuser"] }}
```

Wat!

我觉得这个前缀语法很奇怪，但我无法提出更好的替代方案。不过，我确实好奇为什么特性不能以相同的方式处理：

```py
# not allowed
[feature.wasm.dependencies]
getrandom = { version = "0.2", features=["js"]}
```

# Wat Not 6：目标 cfg 表达式

这是我们的第一个“Wat Not”，即它是以其一致性让我感到惊讶的部分。

你可以使用 `cfg` 表达式（用单引号包裹）来代替具体的目标，如 `x86_64-pc-windows-msvc`。例如，

```py
[target.'cfg(all(windows, target_arch = "x86_64"))'.dependencies]
```

我不认为这算是“wat!”，我认为这很棒。

回顾 `cfg`，它是“配置”（configuration）的缩写，是 Rust 用来有条件编译代码的机制。例如，在我们的 `main.rs` 中，我们可以写：

```py
if cfg!(target_os = "linux") {
    println!("This is Linux!");
}
```

在 `Cargo.toml` 中，目标表达式几乎支持整个 `[cfg](https://doc.rust-lang.org/reference/conditional-compilation.html)` [小语言](https://doc.rust-lang.org/reference/conditional-compilation.html)。

```py
all(), any(), not()
target_arch
target_feature
target_os
target_family
target_env
target_abi
target_endian
target_pointer_width
target_vendor
target_has_atomic
unix
windows
```

`cfg` 小语言中唯一不支持的部分是（我认为）你不能通过 `--cfg` 命令行参数设置一个值。另外，像 `test` 这样的 cfg 值没有意义。

# Wat 7：目标的配置文件

回顾 Wat 1，你可以通过 `[profile.release]`、`[profile.dev]` 和 `[profile.build]` 设置编译器选项。例如：

```py
[profile.dev]
opt-level = 0
```

你能猜到如何为特定目标（如 Windows）设置编译器选项吗？是这样吗？

```py
[target.'cfg(windows)'.profile.dev]
opt-level = 0
```

否。相反，你需要创建一个名为 `.cargo/config.toml` 的新文件，并添加以下内容：

```py
[target.'cfg(windows)']
rustflags = ["-C", "opt-level=0"]
```

Wat!

一般来说，`Cargo.toml` 只支持 `target.*TARGET_EXPRESSION*` 作为依赖项部分的前缀。你不能为配置文件部分添加前缀。但是，在 `[.cargo/config.toml](https://doc.rust-lang.org/cargo/reference/config.html)` 中，你可以有 `[target.*TARGET_EXPRESSION*]` 部分。在这些部分中，你可以设置环境变量来设置编译器选项。

# Wat Not 8：TOML 列表

`Cargo.toml` 支持两种列表语法：

+   内联数组

+   表格数组

这个例子同时使用了以下两种：

```py
[package]
name = "cargo-wat"
version = "0.1.0"
edition = "2021"

[dependencies]
rand = { version = "0.8" }
# Inline array 'features'
getrandom = { version = "0.2", features = ["std", "test-in-browser"] }

# Table array 'bin'
[[bin]]
name = "example"
path = "src/bin/example.rs"

[[bin]]
name = "another"
path = "src/bin/another.rs"
```

我们能把表格数组改成内联数组吗？可以！

```py
# Inline array 'bin'
bins = [
    { name = "example", path = "src/bin/example.rs" },
    { name = "another", path = "src/bin/another.rs" },
]

[package]
name = "cargo-wat"
version = "0.1.0"
edition = "2021"

[dependencies]
rand = { version = "0.8" }
# Inline array 'features'
getrandom = { version = "0.2", features = ["std", "test-in-browser"] }
```

我们能把特性的内联数组改成表格数组吗？

否。内联数组（在这里是字符串类型）不能表示为表格数组。然而，我认为这算是一个“wat not”，而不是“wat!” 因为这是通用 TOML 的限制，而不仅仅是 `Cargo.toml` 的限制。

> 顺便提一下，YAML 格式和 TOML 格式一样，提供了两种列表语法。不过，YAML 的两种语法[都能与简单值一起使用](https://www.yaml.info/learn/flowstyle.html)。

# Wat Not 9：TOML 内联、节和点

这是一个典型的`Cargo.toml`。它混合了部分语法，如`[dependences]`，以及内联语法，如`getrandom = {version = "0.2", features = ["std", "test-in-browser"]}`。

```py
[package]
name = "cargo-wat"
version = "0.1.0"
edition = "2021"

[dependencies]
rand = "0.8"
getrandom = { version = "0.2", features = ["std", "test-in-browser"] }

[target.x86_64-pc-windows-msvc.dependencies]
winapi = { version = "0.3.9", features = ["winuser"] }

[[bin]]
name = "example"
path = "src/bin/example.rs"

[[bin]]
name = "another"
path = "src/bin/another.rs"
```

我们能把它重写成 100%内联的形式吗？可以。

```py
package = { name = "cargo-wat", version = "0.1.0", edition = "2021" }

dependencies = { rand = "0.8", getrandom = { version = "0.2", features = [
    "std",
    "test-in-browser",
] } }

target = { 'cfg(target_os = "windows")'.dependencies = { winapi = { version = "0.3.9", features = [
    "winuser",
] } } }

bins = [
    { name = "example", path = "src/bin/example.rs" },
    { name = "another", path = "src/bin/another.rs" },
]
```

我们也可以将其重写为最大化使用部分：

```py
[package]
name = "cargo-wat"
version = "0.1.0"
edition = "2021"

[dependencies.rand]
version = "0.8"

[dependencies.getrandom]
version = "0.2"
features = ["std", "test-in-browser"]

[target.x86_64-pc-windows-msvc.dependencies.winapi]
version = "0.3.9"
features = ["winuser"]

[[bin]]
name = "example"
path = "src/bin/example.rs"

[[bin]]
name = "another"
path = "src/bin/another.rs"
```

最后，让我们谈谈点号。在 TOML 中，点号用于分隔嵌套表中的键。例如，`a.b.c`表示表`a`中的表`b`中的键`c`。我们能用“很多点号”重写我们的例子吗？可以：

```py
package.name = "cargo-wat"
package.version = "0.1.0"
package.edition = "2021"
dependencies.rand = "0.8"
dependencies.getrandom.version = "0.2"
dependencies.getrandom.features = ["std", "test-in-browser"]
target.x86_64-pc-windows-msvc.dependencies.winapi.version = "0.3.9"
target.x86_64-pc-windows-msvc.dependencies.winapi.features = ["winuser"]
bins = [
    { name = "example", path = "src/bin/example.rs" },
    { name = "another", path = "src/bin/another.rs" },
]
```

我很欣赏 TOML 在处理部分、内联和点号方面的灵活性。我将这种灵活性视为一种“wat not”。你可能会觉得它提供的所有选择令人困惑。不过，我喜欢`Cargo.toml`让我们能够充分发挥 TOML 的强大功能。

> 你可以在[GitHub 上查看这个例子](https://github.com/CarlKCarlK/cargo-wat)。

# 结论

`Cargo.toml`是 Rust 生态系统中的一个重要工具，它提供了简单性和灵活性的平衡，既适合初学者，也适合经验丰富的开发者。通过我们探索的九个 wats 和 wat nots，我们看到这个配置文件有时因其怪癖而令人惊讶，但也因其一致性和强大功能而让人印象深刻。

理解这些怪癖可以帮助你避免潜在的挫折，并使你能够最大化地利用`Cargo.toml`。从管理依赖关系和配置文件到处理特定目标的配置和特性，在这里获得的洞察将帮助你编写更高效、更有效的`Cargo.toml`文件。

本质上，虽然`Cargo.toml`可能有些特殊性，但这些特性通常根源于实际的设计选择，优先考虑功能性和可读性。接受这些怪癖，你会发现`Cargo.toml`不仅能满足你项目的需求，还能提升你的 Rust 开发体验。

*请* [*关注 Carl 在 Medium 上的文章*](https://medium.com/@carlmkadie)*。我在 Rust 和 Python 的科学编程、机器学习以及统计学方面写作。我通常每个月写一篇文章。*
