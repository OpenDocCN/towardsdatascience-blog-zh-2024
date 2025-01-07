# 在嵌入式系统上运行Rust的九条规则

> 原文：[https://towardsdatascience.com/nine-rules-for-running-rust-on-embedded-systems-b0c247ee877e?source=collection_archive---------0-----------------------#2024-10-13](https://towardsdatascience.com/nine-rules-for-running-rust-on-embedded-systems-b0c247ee877e?source=collection_archive---------0-----------------------#2024-10-13)

## 将`range-set-blaze`移植到`no_std`的实践经验

[](https://medium.com/@carlmkadie?source=post_page---byline--b0c247ee877e--------------------------------)[![Carl M. Kadie](../Images/9dbe27c76e9567136e5a7dc587f1fb15.png)](https://medium.com/@carlmkadie?source=post_page---byline--b0c247ee877e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b0c247ee877e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b0c247ee877e--------------------------------) [Carl M. Kadie](https://medium.com/@carlmkadie?source=post_page---byline--b0c247ee877e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b0c247ee877e--------------------------------) ·阅读时长：16分钟·2024年10月13日

--

![](../Images/c3cbaa1585fa74c08e577624b2a2e707.png)

Rust在嵌入式设备上的运行 — 来源：[https://openai.com/dall-e-2/](https://openai.com/dall-e-2/)。所有其他图示来自作者。

你想让你的Rust代码可以在各种设备上运行——从大型服务器到网页、机器人，甚至是手表吗？在这篇三部分系列文章的最后一篇中[[1](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a), [2](https://medium.com/towards-data-science/nine-rules-for-running-rust-in-the-browser-8228353649d1), [3](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-embedded-systems-b0c247ee877e)]，我们将看到如何使用Rust在嵌入式设备上运行，方法是使用`no_std`。

将你的Rust项目移植到`no_std`环境，可以让你面向微控制器和深度嵌入式系统，从而为资源受限的环境创建高效的软件。例如，我使用即将发布的`range-set-blaze`版本，创建了一个LED动画序列和合成器，该软件在Raspberry Pi Pico上运行：

1分钟的视频展示了在Pico上的LED动画

在没有标准库的情况下运行Rust会带来独特的挑战。由于没有操作系统的支持，一些功能，如文件I/O、网络连接，甚至有时动态内存分配都不可用。在本文中，我们将探讨一些实用的策略，以克服这些限制。

将Rust移植到`no_std`环境需要仔细的步骤和选择，任何一步遗漏都可能导致失败。我们将通过遵循这九条规则来简化这一过程，接下来我们将详细探讨这些规则：

1.  确保你的项目在WASM WASI和浏览器中的WASM环境下能够正常工作。

1.  使用目标 `thumbv7m-none-eabi` 和 `cargo tree` 来识别和修复与 `no_std` 不兼容的依赖项。

1.  标记主（非测试）代码为 `no_std` 和 `alloc`。将 `std::` 替换为 `core::` 和 `alloc::`。

1.  使用Cargo功能使你的主代码能够选择性地使用 `std` 来处理文件相关功能（等）。

1.  了解为什么测试代码总是使用标准库。

1.  创建一个简单的嵌入式测试项目。通过QEMU运行它。

1.  在 `Cargo.toml` 中添加适用于WASM和 `no_std` 的关键字和类别。

1.  [可选] 使用预分配数据类型以避免 `alloc`。

1.  将 `thumbv7m-none-eabi` 和 QEMU 添加到你的CI（持续集成）测试中。

> 附注：这些文章基于我在[RustConf24](https://rustconf.com/programs/#755)上于蒙特利尔主持的一个三小时工作坊。感谢所有参与该工作坊的人员。特别感谢来自西雅图Rust Meetup的志愿者们，他们帮助测试了这份材料。这些文章更新了我去年撰写的[一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-the-web-and-on-embedded-94462ef249a2)中的信息。

和本系列中的[第一篇](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a)和[第二篇](https://medium.com/towards-data-science/nine-rules-for-running-rust-in-the-browser-8228353649d1)文章一样，在逐条讲解规则之前，我们先来定义一些术语。

+   **本地环境：** 你的主操作系统（Linux，Windows，macOS）

+   **标准库 (std)**：提供Rust的核心功能——`Vec`，`String`，文件输入/输出，网络，时间处理。

+   **WASM**：WebAssembly（WASM）是一种二进制指令格式，能够在大多数浏览器中运行（以及更广泛的环境中）。

+   **WASI**：WebAssembly系统接口（WASI）允许浏览器外部的WASM访问文件输入/输出、网络（尚未实现）以及时间处理。

+   **no_std**：指示Rust程序不使用完整的标准库，使其适用于小型嵌入式设备或资源极其有限的环境。

+   **alloc**：在 `no_std` 环境中提供堆内存分配功能（`Vec`，`String` 等），对于动态管理内存至关重要。

基于我在`[range-set-blaze](https://github.com/CarlKCarlK/range-set-blaze)`数据结构项目中的经验，以下是我推荐的决策，每个决策逐一描述。为了避免模糊不清，我将它们作为规则表达出来。

# 规则1：确保你的项目能够与WASM WASI和浏览器中的WASM兼容。

在将Rust代码移植到嵌入式环境之前，请确保它能够在[WASM WASI](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a)和[WASM浏览器](https://medium.com/towards-data-science/nine-rules-for-running-rust-in-the-browser-8228353649d1)中成功运行。这些环境暴露了与标准库的脱离相关的问题，并施加了类似嵌入式系统的约束。通过提前解决这些挑战，你将更接近在嵌入式设备上运行你的项目。

> 旁白：如果你不需要让你的项目同时在本地和/或WASM上运行，你可以跳过这一步。不过，你可能会发现之前文章中的一些步骤仍然有用——例如，运行在32位环境下和理解条件编译。

![](../Images/c13b6ccb5fcff21890cb7aa324a1c097.png)

我们希望在其中运行代码的环境，可以看作是一个逐步收紧约束的维恩图。

运行以下命令以确认你的代码在WASM WASI和WASM浏览器中都能正常工作：

```py
cargo test --target wasm32-wasip1
cargo test --target wasm32-unknown-unknown 
```

如果测试失败或无法运行，请重新查看本系列早期文章中的步骤：[WASM WASI](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a)和[WASM浏览器](https://medium.com/towards-data-science/nine-rules-for-running-rust-in-the-browser-8228353649d1)。

WASM WASI文章还提供了关于理解Rust目标（规则2）、条件编译（规则4）和Cargo特性（规则6）的关键背景知识。

一旦满足这些前提条件，下一步是看看我们是否能让依赖项在嵌入式系统上工作。

# 规则2：使用目标`thumbv7m-none-eabi`和`cargo tree`来识别和修复与`no_std`不兼容的依赖项。

要检查你的依赖项是否与嵌入式环境兼容，可以为嵌入式目标编译项目。我建议使用`thumbv7m-none-eabi`目标：

+   `thumbv7m` — 代表ARM Cortex-M3微控制器，是一种流行的嵌入式处理器系列。

+   `none` — 表示没有可用的操作系统（OS）。在Rust中，这通常意味着我们无法依赖标准库（`std`），因此我们使用`no_std`。请记住，标准库提供了诸如`Vec`、`String`、文件输入/输出、网络和时间等核心功能。

+   `eabi` — 嵌入式应用程序二进制接口，一种定义嵌入式可执行文件调用约定、数据类型和二进制布局的标准。

由于大多数嵌入式处理器都共享`no_std`约束，因此确保与此目标的兼容性有助于确保与其他嵌入式目标的兼容性。

安装目标并检查你的项目：

```py
rustup target add thumbv7m-none-eabi
cargo check --target thumbv7m-none-eabi
```

当我在`range-set-blaze`上进行此操作时，遇到了一些关于依赖项的错误，例如：

![](../Images/78e11b0199b7ae21db407aaaf719595f.png)

这表明我的项目依赖于`num-traits`，而`num-traits`又依赖于`either`，最终依赖于`std`。

错误信息可能会让人困惑。为了更好地理解情况，运行以下 `cargo tree` 命令：

```py
cargo tree --edges no-dev --format "{p} {f}"
```

它显示了你的项目依赖关系及其激活的 Cargo 特性的递归列表。例如：

```py
range-set-blaze v0.1.6 (C:\deldir\branches\rustconf24.nostd) 
├── gen_ops v0.3.0
├── itertools v0.13.0 default,use_alloc,use_std
│   └── either v1.12.0 use_std
├── num-integer v0.1.46 default,std
│   └── num-traits v0.2.19 default,i128,std
│       [build-dependencies]
│       └── autocfg v1.3.0
└── num-traits v0.2.19 default,i128,std (*)
```

我们看到多个出现了名为 `use_std` 和 `std` 的 Cargo 特性，这强烈表明：

+   这些 Cargo 特性需要标准库。

+   我们可以关闭这些 Cargo 特性。

使用在[第一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a)中解释的技巧，规则 6，我们禁用了 `use_std` 和 `std` Cargo 特性。请记住，Cargo 特性是累加的，并且具有默认值。为了关闭默认特性，我们使用 `default-features = false`。然后，我们通过指定例如 `features = ["use_alloc"]` 来启用我们想要保留的 Cargo 特性。现在，`Cargo.toml` 文件内容如下：

```py
[dependencies]
gen_ops = "0.3.0"
itertools = { version = "0.13.0", features=["use_alloc"], default-features = false }
num-integer = { version = "0.1.46", default-features = false }
num-traits = { version = "0.2.19", features=["i128"], default-features = false } 
```

关闭 Cargo 特性并不总是足以使你的依赖项兼容 `no_std`。

例如，流行的 `thiserror` 包将 `std` 引入到你的代码中，并且没有提供禁用它的 Cargo 特性。然而，社区已经创建了 `no_std` 替代版本。你可以通过搜索，例如 [https://crates.io/search?q=thiserror+no_std](https://crates.io/search?q=thiserror+no_std)，来找到这些替代版本。

对于 `range-set-blaze`，仍然存在与包 `[gen_ops](https://crates.io/crates/gen_ops)` 相关的问题——这是一个非常方便的包，用于定义操作符如 `+` 和 `&`。该包使用了 `std`，但其实并不需要。我找到了需要的单行更改（使用我们将在规则 3 中讲解的方法）并提交了拉取请求。维护者接受了它，他们发布了更新版本：`0.4.0`。

有时，我们的项目无法禁用 `std`，因为我们需要像文件访问这样的能力来运行在完整的操作系统上。然而，在嵌入式系统上，我们愿意—事实上必须—放弃这些能力。在规则 4 中，我们将看到如何通过引入我们自己的 Cargo 特性使 `std` 使用变为可选。

使用这些方法解决了 `range-set-blaze` 中所有的依赖错误。然而，解决**这些**错误暴露出了主代码中的 281 个错误。进展！

# 规则 3：将主代码（非测试代码）标记为 `no_std` 和 `alloc`。将 `std::` 替换为 `core::` 和 `alloc::`。

在项目的 `lib.rs`（或 `main.rs`）顶部添加：

```py
#![no_std]
extern crate alloc;
```

这意味着我们不会使用标准库，但仍然会分配内存。对于 `range-set-blaze`，这一变化将错误数量从 281 降低到 52。

许多剩余的错误是由于使用了 `std` 中的项，这些项在 `core` 或 `alloc` 中是可用的。由于 `std` 的大部分内容实际上是 `core` 和 `alloc` 的重新导出，我们可以通过将 `std` 引用切换到 `core` 或 `alloc` 来解决许多错误。这使我们能够在不依赖标准库的情况下保持必要的功能。

例如，对于以下每一行，我们都会遇到错误：

```py
use std::cmp::max;
use std::cmp::Ordering;
use std::collections::BTreeMap;
```

将 `std::` 改为 `core::` 或（如果与内存相关）`alloc::` 可以修复这些错误：

```py
use core::cmp::max;
use core::cmp::Ordering;
use alloc::collections::BTreeMap;
```

一些功能，比如文件访问，仅限于 `std` —— 即它们在 `core` 和 `alloc` 之外定义。幸运的是，对于 `range-set-blaze`，切换到 `core` 和 `alloc` 解决了主代码中的 52 个错误。然而，这一修复暴露了测试代码中的 89 个错误。再次进展！

> 附注：你还可以通过 [Clippy 规则](https://users.rust-lang.org/t/how-to-libraries-and-no-std/119455/3) 找到 `std` 可以替换为 `alloc` 或 `core` 的地方。

我们将在规则 5 中处理测试代码中的错误，但首先，让我们弄清楚如果在完整操作系统上运行时需要文件访问等功能该怎么做。

# 规则 4：使用 Cargo 特性让你的主代码在文件相关（等）功能上可选使用 `std`。

如果我们需要两种版本的代码——一种用于在完整操作系统上运行，另一种用于嵌入式系统——我们可以使用 Cargo 特性（参见 [第一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a) 中的规则 6）。例如，定义一个名为 `foo` 的特性，它将是默认的。我们只会在启用 `foo` 时包含 `demo_read_ranges_from_file` 函数。

在 `Cargo.toml`（初步版）中：

```py
[features]
default = ["foo"]
foo = []
```

在 `lib.rs`（初步版本）中：

```py
#![no_std]
extern crate alloc;

// ...

#[cfg(feature = "foo")]
pub fn demo_read_ranges_from_file<P, T>(path: P) -> std::io::Result<RangeSetBlaze<T>>
where
    P: AsRef<std::path::Path>,
    T: FromStr + Integer,
{
    todo!("This function is not yet implemented.");
}
```

这意味着只有在启用 Cargo 特性 `foo` 时才定义函数 `demo_read_ranges_from_file`。现在我们可以检查代码的不同版本：

```py
cargo check # enables "foo", the default Cargo features
cargo check --features foo # also enables "foo"
cargo check --no-default-features # enables nothing
```

现在让我们通过将 `foo` 重命名为 `std`，为我们的 Cargo 特性起一个更有意义的名字。我们的 `Cargo.toml`（中间版）现在看起来是这样的：

```py
[features]
default = ["std"]
std = []
```

在我们的 `lib.rs` 中，我们在顶部添加这些行，以便在启用 `std` Cargo 特性时引入 `std` 库：

```py
#[cfg(feature = "std")]
extern crate std;
```

因此，`lib.rs`（最终版）看起来是这样的：

```py
#![no_std]
extern crate alloc;

#[cfg(feature = "std")]
extern crate std;

// ...

#[cfg(feature = "std")]
pub fn demo_read_ranges_from_file<P, T>(path: P) -> std::io::Result<RangeSetBlaze<T>>
where
    P: AsRef<std::path::Path>,
    T: FromStr + Integer,
{
    todo!("This function is not yet implemented.");
}
```

我们希望对 `Cargo.toml` 做最后一次更改。我们希望新的 Cargo 特性控制依赖和它们的特性。下面是最终版的 `Cargo.toml`：

```py
[features]
default = ["std"]
std = ["itertools/use_std", "num-traits/std", "num-integer/std"]

[dependencies]
itertools = { version = "0.13.0", features = ["use_alloc"], default-features = false }
num-integer = { version = "0.1.46", default-features = false }
num-traits = { version = "0.2.19", features = ["i128"], default-features = false }
gen_ops = "0.4.0"
```

> 附注：如果你对 `Cargo.toml` 中指定依赖和特性的格式感到困惑，看看我最近的文章：[*九个 Rust Cargo.toml 的陷阱与误区：掌握 Cargo.toml 格式规则，避免沮丧*](https://medium.com/towards-data-science/nine-rust-cargo-toml-wats-and-wat-nots-1e5e02e41648) *在* Towards Data Science。

要检查你的项目是否同时能够在标准库（`std`）和无标准库环境下编译，使用以下命令：

```py
cargo check # std
cargo check --no-default-features # no_std
```

使用 `cargo check` 已经能正常工作，你可能会认为 `cargo test` 会很直接。遗憾的是，并非如此。我们接下来看看这个问题。

# 规则 5：理解为什么测试代码总是使用标准库。

当我们使用 `--no-default-features` 编译项目时，它将在 `no_std` 环境中运行。然而，Rust 的测试框架总是包括标准库，即使在 `no_std` 项目中也是如此。这是因为 `cargo test` 需要 `std`；例如，`#[test]` 属性和测试框架本身都在标准库中定义。

结果是，运行：

```py
# DOES NOT TEST `no_std`
cargo test --no-default-features
```

实际上并不会测试你代码的`no_std`版本。即使在真正的`no_std`环境中，`std`中那些不可用的函数，在测试时仍然可以访问。例如，下面的测试将在使用`--no-default-features`时成功编译和运行，尽管它使用了`std::fs`：

```py
#[test]
fn test_read_file_metadata() {
    let metadata = std::fs::metadata("./").unwrap();
    assert!(metadata.is_dir());
}
```

此外，在`std`模式下进行测试时，你可能需要显式导入标准库中的某些功能。这是因为，即使在测试期间`std`可用，你的项目仍然是以`#![no_std]`编译的，这意味着标准前导并不会自动包含在作用域中。例如，你通常需要在测试代码中包含以下导入：

```py
#![cfg(test)]
use std::prelude::v1::*;
use std::{format, print, println, vec};
```

这些导入将从标准库中引入必要的工具，以便在测试过程中可以使用它们。

要真正测试没有标准库的代码，你需要使用不依赖于`cargo test`的替代方法。我们将在下一条规则中探讨如何运行`no_std`测试。

# 规则6：创建一个简单的嵌入式测试项目。使用QEMU运行它。

你无法在嵌入式环境中运行常规测试。然而，你**可以** — 并且应该 — 至少运行一个嵌入式测试。我的哲学是，即使只有一个测试，也比没有测试要好得多。由于“如果它能编译，它就能工作”通常对`no_std`项目有效，一个（或几个）精心选择的测试可能会非常有效。

> 旁注：有希望以更正常的方式运行嵌入式测试[[1](https://users.rust-lang.org/t/how-to-implement-unit-tests-for-a-project-with-embedded-rust/99768)][[2](https://www.reddit.com/r/rust/comments/1g3i5uh/comment/lrxith4/)]。据我所知，正常的本地测试没有简单的方法。如果有变化，请告诉我，我会更新这一部分内容。

要运行这个测试，我们使用QEMU（快速仿真器，发音为“cue-em-you”），它允许我们在主操作系统（Linux、Windows或macOS）上模拟`thumbv7m-none-eabi`代码。

## 安装QEMU。

查看QEMU的[下载页面](https://www.qemu.org/download/)以获取完整信息：

**Linux/WSL**

+   Ubuntu：`sudo apt-get install qemu-system`

+   Arch：`sudo pacman -S qemu-system-arm`

+   Fedora：`sudo dnf install qemu-system-arm`

**Windows**

+   方法 1：[https://qemu.weilnetz.de/w64](https://qemu.weilnetz.de/w64)。运行安装程序（告诉Windows它是可以的）。将`"C:\Program Files\qemu\"`添加到你的路径中。

+   方法 2：从[https://www.msys2.org/](https://www.msys2.org/)安装MSYS2。打开MSYS2 UCRT64终端。`pacman -S mingw-w64-x86_64-qemu`。将`C:\msys64\mingw64\bin\`添加到你的路径中。

**Mac**

+   `brew install qemu` 或 `sudo port install qemu`

测试安装：

```py
qemu-system-arm --version
```

## 创建一个嵌入式子项目。

为嵌入式测试创建一个子项目：

```py
cargo new tests/embedded
```

这个命令生成一个新的子项目，包括位于`tests/embedded/Cargo.toml`的配置文件。

> 附注**：此命令还会修改您的顶级`Cargo.toml`，将子项目添加到您的工作区。在Rust中，工作区是由顶级`Cargo.toml`中的`[workspace]`部分定义的相关包的集合。工作区中的所有包共享一个`Cargo.lock`文件，确保整个工作区的依赖版本一致。

编辑`tests/embedded/Cargo.toml`使其如下所示，但将`"range-set-blaze"`替换为您顶级项目的名称：

```py
[package]
name = "embedded"
version = "0.1.0"
edition = "2021"

[dependencies]
alloc-cortex-m = "0.4.4"
cortex-m = "0.7.7"
cortex-m-rt = "0.7.3"
cortex-m-semihosting = "0.5.0"
panic-halt = "0.2.0"
# Change to refer to your top-level project
range-set-blaze = { path = "../..", default-features = false }
```

## 更新测试代码。

将`tests/embedded/src/main.rs`的内容替换为：

```py
// Based on https://github.com/rust-embedded/cortex-m-quickstart/blob/master/examples/allocator.rs
// and https://github.com/rust-lang/rust/issues/51540
#![feature(alloc_error_handler)]
#![no_main]
#![no_std]
extern crate alloc;
use alloc::string::ToString;
use alloc_cortex_m::CortexMHeap;
use core::{alloc::Layout, iter::FromIterator};
use cortex_m::asm;
use cortex_m_rt::entry;
use cortex_m_semihosting::{debug, hprintln};
use panic_halt as _;
#[global_allocator]
static ALLOCATOR: CortexMHeap = CortexMHeap::empty();
const HEAP_SIZE: usize = 1024; // in bytes
#[alloc_error_handler]
fn alloc_error(_layout: Layout) -> ! {
    asm::bkpt();
    loop {}
}

#[entry]
fn main() -> ! {
    unsafe { ALLOCATOR.init(cortex_m_rt::heap_start() as usize, HEAP_SIZE) }

    // Test(s) goes here. Run only under emulation
    use range_set_blaze::RangeSetBlaze;
    let range_set_blaze = RangeSetBlaze::from_iter([100, 103, 101, 102, -3, -4]);
    hprintln!("{:?}", range_set_blaze.to_string());
    if range_set_blaze.to_string() != "-4..=-3, 100..=103" {
        debug::exit(debug::EXIT_FAILURE);
    }

    debug::exit(debug::EXIT_SUCCESS);
    loop {}
}
```

这部分`main.rs`代码大部分是嵌入式系统的模板代码。实际的测试代码是：

```py
use range_set_blaze::RangeSetBlaze;
let range_set_blaze = RangeSetBlaze::from_iter([100, 103, 101, 102, -3, -4]);
hprintln!("{:?}", range_set_blaze.to_string());
if range_set_blaze.to_string() != "-4..=-3, 100..=103" {
    debug::exit(debug::EXIT_FAILURE);
}
```

如果测试失败，它返回`EXIT_FAILURE`；否则，它返回`EXIT_SUCCESS`。我们使用`hprintln!`宏在仿真过程中将消息打印到控制台。由于这是一个嵌入式系统，代码会以无限循环的方式结束，以便持续运行。

## 添加支持文件。

在运行测试之前，您必须将两个文件添加到子项目中：来自Cortex-M [快速入门仓库](https://github.com/rust-embedded/cortex-m-quickstart/tree/master)的`build.rs`和`memory.x`文件。

**Linux/WSL/macOS**

```py
cd tests/embedded
wget https://raw.githubusercontent.com/rust-embedded/cortex-m-quickstart/master/build.rs
wget https://raw.githubusercontent.com/rust-embedded/cortex-m-quickstart/master/memory.
```

**Windows (Powershell)**

```py
cd tests/embedded
Invoke-WebRequest -Uri 'https://raw.githubusercontent.com/rust-embedded/cortex-m-quickstart/master/build.rs' -OutFile 'build.rs'
Invoke-WebRequest -Uri 'https://raw.githubusercontent.com/rust-embedded/cortex-m-quickstart/master/memory.x' -OutFile 'memory.x'
```

另外，创建一个`tests/embedded/.cargo/config.toml`，并将以下内容添加到其中：

```py
[target.thumbv7m-none-eabi]
runner = "qemu-system-arm -cpu cortex-m3 -machine lm3s6965evb -nographic -semihosting-config enable=on,target=native -kernel"

[build]
target = "thumbv7m-none-eabi"
```

此配置指示Cargo使用QEMU运行嵌入式代码，并将`thumbv7m-none-eabi`设置为子项目的默认目标。

## 运行测试。

使用`cargo run`（而不是`cargo test`）运行测试：

```py
# Setup
# Make this subproject 'nightly' to support #![feature(alloc_error_handler)]
rustup override set nightly
rustup target add thumbv7m-none-eabi

# If needed, cd tests/embedded
cargo run
```

您应该看到日志消息，且进程应该无错误退出。在我的例子中，我看到：`"-4..=-3, 100..=103"`。

这些步骤可能看起来需要做很多工作，仅仅是为了运行一个（或几个）测试。然而，这主要是一次性的工作，主要是复制和粘贴。此外，它还使得在CI环境中运行测试成为可能（请参见规则9）。替代方法——声称代码在`no_std`环境中运行良好，而实际上从未在`no_std`中运行过——可能会忽视关键问题。

下一个规则要简单得多。

# 规则7：在`Cargo.toml`中，为WASM和`no_std`添加关键字和分类。

一旦您的包编译并通过了额外的嵌入式测试，您可能希望将其发布到[crates.io](https://crates.io/)，Rust的包注册表。为了让其他人知道它兼容WASM和`no_std`，请将以下关键字和分类添加到您的`Cargo.toml`文件中：

```py
[package]
# ... 
categories = ["no-std", "wasm", "embedded"] # + others specific to your package
keywords = ["no_std", "wasm"] # + others specific to your package
```

请注意，对于分类，我们在`no-std`中使用了连字符。对于关键字，`no_std`（带下划线）比`no-std`更常用。您的包最多可以有五个关键字和五个分类。

这里有一个[分类](https://crates.io/categories/)和[关键字](https://crates.io/keywords)的列表，可能会对您有兴趣，并附有每个术语使用的crate数量：

+   [分类 no-std](https://crates.io/categories/no-std?sort=downloads)（6884）

+   [分类嵌入式](https://crates.io/categories/embedded?sort=downloads)（3455）

+   [分类 wasm](https://crates.io/categories/wasm?sort=downloads)（2026）

+   [Category no-std::no-alloc](https://crates.io/categories/no-std::no-alloc?sort=downloads) (581)

+   [Keyword wasm](https://crates.io/keywords/wasm?sort=downloads) (1686)

+   [Keyword no_std](https://crates.io/keywords/no_std?sort=downloads) (1351)

+   [Keyword no-std](https://crates.io/keywords/no-std?sort=downloads) (1157)

+   [Keyword embedded](https://crates.io/keywords/embedded?sort=downloads) (925)

+   [Keyword webassembly](https://crates.io/keywords/webassembly?sort=downloads) (804)

良好的类别和关键字将帮助人们找到你的包，但该系统是不正式的。没有机制检查你的类别和关键字是否准确，也不要求你提供它们。

接下来，我们将探索你可能遇到的最受限的环境之一。

# 规则 8：[可选] 使用预分配的数据类型以避免使用`alloc`。

我的项目`range-set-blaze`实现了一个动态数据结构，需要从堆中进行内存分配（通过`alloc`）。但是，如果你的项目不需要动态内存分配怎么办？那样的话，它可以运行在更加受限的嵌入式环境中——特别是那些程序加载时就已预分配所有内存的环境。

如果可能的话，避免使用`alloc`的原因：

+   完全确定的内存使用

+   降低运行时故障的风险（通常由内存碎片引起）

+   降低功耗

有些crate可以帮助你替换像`Vec`、`String`和`HashMap`这样的动态数据结构。这些替代方案通常要求你指定一个最大大小。下表展示了一些常用的crate：

![](../Images/4f7714b6900349e2d92b8951535736c8.png)

我推荐使用`heapless` crate，因为它提供了一系列协同工作的数据结构。

这是一个与LED显示相关的代码示例——使用`heapless`。这段代码创建了一个从字节到整数列表的映射。我们将映射中条目的数量和整数列表的长度限制为`DIGIT_COUNT`（在此例中为4）。

```py
use heapless::{LinearMap, Vec};
// …
let mut map: LinearMap<u8, Vec<usize, DIGIT_COUNT>, DIGIT_COUNT> = LinearMap::new();
// …
let mut vec = Vec::default();
vec.push(index).unwrap();
map.insert(*byte, vec).unwrap(); // actually copies
```

创建一个`no_alloc`项目的完整细节超出了我的经验范围。然而，第一步是从你的`lib.rs`或`main.rs`中删除这一行（在规则3中添加的）：

```py
extern crate alloc; // remove this
```

# 规则 9：将`thumbv7m-none-eabi`和QEMU添加到你的CI（持续集成）测试中。

你的项目现在已经编译为`no_std`并通过了至少一个嵌入式特定的测试。你完成了吗？还没有。正如我在前两篇文章中所说：

> 如果它不在CI中，那就不存在。

记住，持续集成（CI）是一个每次更新代码时都能自动运行测试的系统。我使用GitHub Actions作为我的CI平台。以下是我添加到`.github/workflows/ci.yml`中的配置，用于在嵌入式平台上测试我的项目：

```py
test_thumbv7m_none_eabi:
    name: Setup and Check Embedded
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Set up Rust
        uses: dtolnay/rust-toolchain@master
        with:
          toolchain: stable
          target: thumbv7m-none-eabi
      - name: Install check stable and nightly
        run: |
          cargo check --target thumbv7m-none-eabi --no-default-features
          rustup override set nightly
          rustup target add thumbv7m-none-eabi
          cargo check --target thumbv7m-none-eabi --no-default-features
          sudo apt-get update && sudo apt-get install qemu qemu-system-arm
      - name: Test Embedded (in nightly)
        timeout-minutes: 1
        run: |
          cd tests/embedded
          cargo run
```

通过在CI中测试嵌入式和`no_std`，我可以确保我的代码将继续支持未来的嵌入式平台。

所以，事情就是这样——移植Rust代码到嵌入式的九条规则。要查看应用了这九条规则后整个`range-set-blaze`项目的快照，请参见[这个Github分支](https://github.com/CarlKCarlK/range-set-blaze/tree/rustconf24.nostd)。

这就是让我在移植到嵌入式时感到惊讶的地方：

**坏的一面：**

+   我们无法在嵌入式系统上运行现有的测试。相反，我们必须创建一个新的子项目并编写（一些）新的测试。

+   许多流行的库依赖于`std`，因此找到或调整适用于`no_std`的依赖库可能会面临挑战。

**好的一面：**

+   Rust中“如果能编译，就能运行”的说法在嵌入式开发中依然成立。这使我们能够在无需大量新测试的情况下，对代码的正确性充满信心。

+   尽管`no_std`移除了我们对标准库的直接访问，但许多项目仍然可以通过`core`和`alloc`使用。

+   借助仿真，你可以在没有硬件的情况下为嵌入式系统进行开发。

感谢你加入我从WASI到WebAssembly再到嵌入式开发的旅程。Rust凭借其在不同环境中高效且安全运行的能力，继续让我印象深刻。随着你探索这些不同的领域，我希望你能像我一样，感受到Rust的灵活性和强大。无论你是在处理云服务器、浏览器还是微控制器，我们讨论的工具都将帮助你自信地应对未来的挑战。

> 对未来的文章感兴趣吗？请[在Medium上关注我](https://medium.com/@carlmkadie)。我写关于Rust和Python、科学编程、机器学习以及统计学的文章。我倾向于每月写一篇文章。
