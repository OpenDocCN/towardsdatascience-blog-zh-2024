# 在浏览器中运行 Rust 的九条规则

> 原文：[https://towardsdatascience.com/nine-rules-for-running-rust-in-the-browser-8228353649d1?source=collection_archive---------1-----------------------#2024-10-08](https://towardsdatascience.com/nine-rules-for-running-rust-in-the-browser-8228353649d1?source=collection_archive---------1-----------------------#2024-10-08)

## 从将 range-set-blaze 移植到 WASM 中获得的实践经验

[](https://medium.com/@carlmkadie?source=post_page---byline--8228353649d1--------------------------------)[![Carl M. Kadie](../Images/9dbe27c76e9567136e5a7dc587f1fb15.png)](https://medium.com/@carlmkadie?source=post_page---byline--8228353649d1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8228353649d1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8228353649d1--------------------------------) [Carl M. Kadie](https://medium.com/@carlmkadie?source=post_page---byline--8228353649d1--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8228353649d1--------------------------------) ·阅读时长：21分钟·2024年10月8日

--

![](../Images/f6718aabbf0325b092e3b4437e3f96c6.png)

在浏览器中运行 Rust — 来源：[https://openai.com/dall-e-2/](https://openai.com/dall-e-2/)。其他所有图像均来自作者。

你想让你的 Rust 代码无处不在——从大型服务器到网页、机器人，甚至手表吗？在这篇三篇文章中的第二篇[[1](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a), [2](https://medium.com/towards-data-science/nine-rules-for-running-rust-in-the-browser-8228353649d1), [3](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-embedded-systems-b0c247ee877e)]中，我将向你展示如何使用 WebAssembly (WASM) 在用户的浏览器中直接运行 Rust 代码。

使用这种技术，你可以通过一个—或许是免费的—静态 web 服务器提供 CPU 密集型、动态网页。作为额外的好处，用户的数据永远不会离开他们的机器，避免了隐私问题。例如，我提供了一个工具，帮助朋友、跑步俱乐部成员和队友查找比赛结果。要查看该工具，请访问[其网页](https://carlkcarlk.github.io/race-results/matcher/)，并点击“match”。

![](../Images/138b2fdb5c996a8d2a16b26e4e50c943.png)

> 旁注：要了解更多关于匹配名称的信息，请参阅[使用贝叶斯定理在列表中查找独特名称](https://medium.com/towards-data-science/use-bayes-theorem-to-find-distinctive-names-in-a-list-5acd8fe03c2b)，《*Towards Data Science*》中的文章。

在浏览器中运行 Rust 面临一些挑战。你的代码无法像 Linux、Windows 或 macOS 那样访问完整的操作系统。你无法直接访问文件或网络。你仅能有限地访问时间和随机数。我们将探讨一些解决方法和变通方案。

将代码移植到浏览器中的WASM需要多个步骤和选择，且这些过程可能耗时。漏掉一步可能会导致失败。我们将通过提供九条规则来简化这一复杂性，接下来我们将详细探讨这些规则：

1.  确认你现有的应用程序可以与WASM WASI一起工作，并创建一个简单的JavaScript网页。

1.  安装`wasm32-unknown-unknown`目标、`wasm-pack`、`wasm-bindgen-cli`以及用于测试的Chrome和Chromedriver。

1.  使你的项目成为`cdylib`（以及`rlib`），添加`wasm-bindgen`依赖，并进行测试。

1.  了解`wasm-bindgen`支持哪些类型。

1.  更改函数以使用支持的类型。将文件更改为通用的`BufRead`。

1.  适配测试，跳过那些不适用的测试。

1.  如有必要，切换到适合JavaScript的依赖。运行测试。

1.  将你的网页与函数连接起来。

1.  将`wasm-pack`添加到你的CI（持续集成）测试中。

> 旁注：这些文章基于我在蒙特利尔的[RustConf24](https://rustconf.com/programs/#755)上进行的三小时工作坊。感谢工作坊的参与者。同时也特别感谢来自西雅图Rust Meetup的志愿者，他们帮助测试了这些材料。这些文章替代了我去年写的[一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-the-web-and-on-embedded-94462ef249a2)，并且包含了更新的信息。

正如在[本系列的第一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a)中所述，在逐条查看规则之前，我们先来定义一下术语。

+   **Native**：你的本地操作系统（Linux、Windows、macOS）

+   **标准库（std）**：提供Rust的核心功能——`Vec`、`String`、文件输入/输出、网络、时间等。

+   **WASM**：WebAssembly（WASM）是一种二进制指令格式，可以在大多数浏览器中运行（以及其他平台）。

+   **WASI**：WebAssembly系统接口（WASI）允许非浏览器环境中的WASM访问文件I/O、网络（尚未实现）和时间处理。

+   **no_std**：指示Rust程序不使用完整的标准库，使其适用于小型嵌入式设备或高度资源受限的环境。

+   **alloc**：在`no_std`环境中提供堆内存分配功能（`Vec`、`String`等），这是动态管理内存所必需的。

基于我在`[range-set-blaze](https://github.com/CarlKCarlK/range-set-blaze)`数据结构项目中的经验，以下是我推荐的决策，逐一描述。为了避免含糊其辞，我将这些决策表达为规则。

# 规则1：确认你现有的应用程序可以与WASM WASI一起工作，并创建一个简单的JavaScript网页。

使你的Rust代码能够在浏览器中运行，如果满足两个前提条件，将会更加容易：

+   让你的Rust代码在WASM WASI中运行。

+   让一些JavaScript在浏览器中运行。

对于第一个先决条件，请参阅[《在 WASM WASI 上运行 Rust 的九条规则》](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a)（来自 *Towards Data Science*）。那篇文章——本系列的第一篇——详细说明了如何将你的代码从本地操作系统移植到 WASM WASI。通过这个迁移，你将完成一半的工作，接下来就可以在浏览器中运行 WASM 了。

![](../Images/c13b6ccb5fcff21890cb7aa324a1c097.png)

我们希望运行代码的环境就像是一个逐渐收紧约束条件的维恩图。

通过你的测试确认代码在 WASM WASI 上运行：

```py
rustup target add wasm32-wasip1
cargo install wasmtime-cli
cargo test --target wasm32-wasip1
```

对于第二个先决条件，展示你能创建一些 JavaScript 代码并在浏览器中运行。我建议将这个 `index.html` 文件添加到你项目的顶层：

```py
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Line Counter</title>
</head>
<body>
    <h1>Line Counter</h1>
    <input type="file" id="fileInput" />
    <p id="lineCount">Lines in file: </p>
    <script>
        const output = document.getElementById('lineCount');
        document.getElementById('fileInput').addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (!file) { output.innerHTML = ''; return } // No file selected
            const reader = new FileReader();
            // When the file is fully read
            reader.onload = async (e) => {                
                const content = e.target.result;
                const lines = content.split(/\r\n|\n/).length;
                output.textContent = `Lines in file: ${lines}`;
            };
            // Now start to read the file as text
            reader.readAsText(file);
        });
    </script>
</body>
</html>
```

现在，将此页面提供给你的浏览器。你可以通过编辑器扩展来提供网页。我使用 [Live Preview](https://marketplace.visualstudio.com/items?itemName=ms-vscode.live-server) 作为 VS Code 的扩展。或者，你也可以安装并使用独立的 Web 服务器，比如 [Simple Html Server](https://github.com/TheWaWaR/simple-http-server)：

```py
cargo install simple-http-server
simple-http-server --ip 127.0.0.1 --port 3000 --index
# then open browser to http://127.0.0.1:3000
```

现在你应该能看到一个网页，在该网页上你可以选择一个文件。页面上的 JavaScript 会统计文件中的行数。

![](../Images/2e27c53e09c60f8cc65a412a17741755.png)

让我们看一下 JavaScript 的关键部分，因为稍后我们会修改它以调用 Rust。

> 旁注：你必须学习 JavaScript 才能在浏览器中使用 Rust 吗？是，也不是。是的，你需要编写一些简单的 JavaScript 代码。不是，你可能不需要“学习” JavaScript。我发现 ChatGPT 足够强大，可以生成我所需的简单 JavaScript 代码。

+   查看用户选择了哪个文件。如果没有选择文件，则返回：

```py
const file = event.target.files[0];
if (!file) { output.innerHTML = ''; return } // No file selected
```

+   创建一个新的 `FileReader` 对象，进行一些设置，然后以文本形式读取文件：

```py
const reader = new FileReader();
// ... some setup ...
// Now start to read the file as text
reader.readAsText(file);
```

+   这是设置步骤。它说：等待文件完全读取，将其内容作为字符串读取，按行拆分字符串，然后显示行数。

```py
// When the file is fully read
reader.onload = async (e) => {                
    const content = e.target.result;
    const lines = content.split(/\r\n|\n/).length;
    output.textContent = `Lines in file: ${lines}`;
    };
```

满足先决条件后，我们接下来安装所需的 WASM-in-the-Browser 工具。

# 规则 2：安装 `wasm32-unknown-unknown` 目标、wasm-pack、`wasm-bindgen-cli`，以及用于测试的 Chrome 和 Chromedriver。

我们从简单的开始，安装这三个工具：

```py
rustup target add wasm32-unknown-unknown
cargo install wasm-pack --force
cargo install wasm-bindgen-cli --force
```

第一行安装了一个新的目标，`wasm32-unknown-unknown`。这个目标将 Rust 编译为 WebAssembly，而不对代码将运行的环境做任何假设。没有假设使得它适合在浏览器中运行。（关于目标的更多信息，请参见[上一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a)中的规则 #2。）

接下来的两行安装 `wasm-pack` 和 `wasm-bindgen-cli`，这两个命令行工具。第一个用于构建、打包和发布，生成适用于网页使用的格式。第二个则简化了测试过程。我们使用 `--force` 来确保工具是最新的并且互相兼容。

现在，我们进入了麻烦的部分，安装测试用的 Chrome 和 Chromedriver。测试用的 Chrome 是一个可自动化的 Chrome 浏览器版本，而 Chromedriver 是一个独立的程序，可以将你的 Rust 测试用例运行在测试用的 Chrome 中。

为什么安装它们很麻烦？首先，过程有些复杂。其次，测试用的 Chrome 版本必须与 Chromedriver 版本匹配。第三，安装测试用的 Chrome 会与当前安装的常规 Chrome 冲突。

在了解这些背景信息后，以下是我的建议。从将这两个程序安装到你主目录的专用子文件夹开始。

+   Linux 和 WSL（Windows 子系统 Linux）：

```py
cd ~
mkdir -p ~/.chrome-for-testing
cd .chrome-for-testing/
wget https://storage.googleapis.com/chrome-for-testing-public/129.0.6668.70/linux64/chrome-linux64.zip
wget https://storage.googleapis.com/chrome-for-testing-public/129.0.6668.70/linux64/chromedriver-linux64.zip
unzip chrome-linux64.zip
unzip chromedriver-linux64.zip
```

+   Windows（PowerShell）：

```py
New-Item -Path $HOME -Name ".chrome-for-testing" -ItemType "Directory"
Set-Location -Path $HOME\.chrome-for-testing
bitsadmin /transfer "ChromeDownload" https://storage.googleapis.com/chrome-for-testing-public/129.0.6668.70/win64/chrome-win64.zip $HOME\.chrome-for-testing\chrome-win64.zip
bitsadmin /transfer "ChromeDriverDownload" https://storage.googleapis.com/chrome-for-testing-public/129.0.6668.70/win64/chromedriver-win64.zip $HOME\.chrome-for-testing\chromedriver-win64.zip
Expand-Archive -Path "$HOME\.chrome-for-testing\chrome-win64.zip" -DestinationPath "$HOME\.chrome-for-testing"
Expand-Archive -Path "$HOME\.chrome-for-testing\chromedriver-win64.zip" -DestinationPath "$HOME\.chrome-for-testing"
```

> 旁白：抱歉，我还没有测试任何 Mac 系统的安装说明。请查看 [Chrome for Testing 网页](https://googlechromelabs.github.io/chrome-for-testing/)，然后尝试适配 Linux 的方法。如果你告诉我什么方法有效，我将更新这一部分内容。

这将安装 129.0.6668.70 版本，这是截至 2024 年 9 月 30 日的稳定版本。如果你愿意，可以查看 [Chrome for Testing 可用性](https://googlechromelabs.github.io/chrome-for-testing/) 页面，查看最新的稳定版本。

接下来，我们需要将这些程序添加到 `PATH` 中。我们可以临时添加它们，仅对当前的终端会话有效：

+   Linux 和 WSL（仅限本次会话）：

```py
export PATH=~/.chrome-for-testing/chrome-linux64:~/.chrome-for-testing/chromedriver-linux64:$PATH
```

+   Windows（仅限本次会话）：

```py
# PowerShell
$env:PATH = "$HOME\.chrome-for-testing\chrome-win64;$HOME\.chrome-for-testing\chromedriver-win64;$PATH"
# or, CMD
set PATH=%USERPROFILE%\.chrome-for-testing\chrome-win64;%USERPROFILE%\.chrome-for-testing\chromedriver-win64;%PATH%
```

另外，我们可以将它们永久添加到我们的 `PATH` 中，适用于所有未来的终端会话。请理解，这可能会干扰你访问常规版本的 Chrome。

Linux 和 WSL（然后重新启动终端）：

```py
echo 'export PATH=~/.chrome-for-testing/chrome-linux64:~/.chrome-for-testing/chromedriver-linux64:$PATH' >> ~/.bashrc
```

Windows（PowerShell，然后重新启动终端）：

```py
[System.Environment]::SetEnvironmentVariable("Path", "$HOME\.chrome-for-testing\chrome-win64;$HOME\.chrome-for-testing\chromedriver-win64;" + $env:PATH, [System.EnvironmentVariableTarget]::User)
```

安装完成后，你可以使用以下命令验证安装是否成功：

```py
chromedriver --version
```

> 旁白：你可以跳过安装并使用测试用的 Chrome 和 Chromedriver 吗？可以，也不可以。如果跳过它们，你仍然能够从 Rust 创建 WASM。此外，你还可以在网页中通过 JavaScript 调用这个 WASM。
> 
> 然而，你的项目——就像所有优秀的代码一样——应该已经包含了测试。如果跳过测试用的 Chrome，你将无法运行浏览器中的 WASM 测试用例。此外，浏览器中的 WASM 违反了 Rust 的 “如果它能编译，它就能工作” 原则。具体来说，如果你使用了不支持的功能，如文件访问，编译成 WASM 时不会捕获错误。只有测试用例才能捕捉到这种错误。因此，运行测试用例是至关重要的。

现在我们有了在浏览器中运行测试的工具，让我们尝试（几乎可以肯定会失败）运行这些测试。

# 规则 3：将项目设置为 `cdylib`（和 `rlib`），添加 `wasm-bindgen` 依赖，并进行测试。

`wasm-bindgen` 包是一组自动生成的 Rust 和 JavaScript 之间的绑定，允许 JavaScript 调用 Rust。

为了在浏览器中为 WASM 准备代码，你需要将项目设置为库项目。此外，你还需要添加并使用 `wasm-bindgen` 依赖。按照以下步骤操作：

+   如果你的项目是可执行文件，将其改为库项目，通过将`src/main.rs`重命名为`src/lib.rs`。同时，注释掉你的`main`函数。

+   让你的项目同时创建静态库（默认）和动态库（WASM所需）。具体来说，编辑`Cargo.toml`以包含：

```py
[lib]
crate-type = ["cdylib", "rlib"]
```

+   添加`wasm-bindgen`依赖：

```py
cargo add wasm-bindgen
cargo add wasm-bindgen-test --dev
```

+   创建或更新`.cargo/config.toml`（不要与`Cargo.toml`混淆），并包括：

```py
[target.wasm32-unknown-unknown]
runner = "wasm-bindgen-test-runner"
```

接下来，哪些函数你希望能在JavaScript中可见？用`#[wasm_bindgen]`标记这些函数，并将它们设为`pub`（公共）。在函数文件的顶部，添加`use wasm_bindgen::prelude::*;`。

> 旁白：目前，你的函数可能无法编译。我们将在后续规则中解决这个问题。

测试怎么办？在每个`#[test]`上添加`#[wasm_bindgen_test]`。在测试需要时，添加以下`use`语句和配置语句：

```py
use wasm_bindgen_test::wasm_bindgen_test;
wasm_bindgen_test::wasm_bindgen_test_configure!(run_in_browser);
```

如果你愿意，可以在一个小型示例项目上尝试上述步骤。从GitHub安装该示例项目：

```py
# cd to the top of a work directory
git clone --branch native_version --single-branch https://github.com/CarlKCarlK/rustconf24-good-turing.git good-turing
cd good-turing
cargo test
cargo run pg100.txt
```

在这里，我们看到这些修改应用到了小型示例项目的`lib.rs`：

```py
// --- May fail to compile for now. ---
use wasm_bindgen::prelude::*;
// ...
#[wasm_bindgen]
pub fn good_turing(file_name: &str) -> Result<(u32, u32), io::Error> {
    let reader = BufReader::new(File::open(file_name)?);
    // ...
}
// fn main() {
// ...
// }
#[cfg(test)]
mod tests {
    use wasm_bindgen_test::wasm_bindgen_test;
    wasm_bindgen_test::wasm_bindgen_test_configure!(run_in_browser);
    // ...
    #[test]
    #[wasm_bindgen_test]
    fn test_process_file() {
      let (prediction, actual) = good_turing("./pg100.txt").unwrap();
      // ...
    }
}
```

做了这些修改后，我们准备开始测试（并可能失败）：

```py
cargo test --target wasm32-unknown-unknown
```

在这个示例中，编译器抱怨浏览器中的WASM不喜欢返回元组类型，这里是`(u32, u32)`。它还抱怨不喜欢返回包含`io::Error`的`Result`。要解决这些问题，我们需要了解浏览器中的WASM支持哪些类型。这正是规则4的主题。

修复类型问题并能够运行测试后会发生什么？测试仍然会失败，但这时是运行时错误。浏览器中的WASM不支持从文件中读取。然而，示例测试尝试从文件中读取。在规则5中，我们将讨论针对类型限制和文件访问限制的解决方法。

# 规则 4：了解`wasm-bindgen`支持哪些类型。

JavaScript可以看到的Rust函数必须具有`wasm-bindgen`支持的输入和输出类型。使用不支持的类型会导致编译器错误。例如，传递一个`u32`是可以的。但传递一个元组`(u32, 32)`则不行。

更一般来说，我们可以将Rust类型分为三类：“是的！”，“不是！”和“避免”。

## 是的！

这是Rust类型的类别，JavaScript（通过`wasm-bindgen`）能够很好理解。

我们从**Rust的简单复制类型**开始：

![](../Images/40a0ca49852a34ff8b965969aac0a284.png)

有两个项目让我感到意外。首先，64位整数在JavaScript端需要额外的工作。具体来说，它们需要使用JavaScript的`[BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)`类。其次，JavaScript不支持128位整数。128位整数属于“不是！”类别。

现在转向**与字符串和向量相关的类型**：

![](../Images/fe4f7bd53d64b057ccfc9b2d471ca821.png)

这些超有用的类型使用堆分配的内存。由于Rust和JavaScript的内存管理方式不同，每种语言都会创建一份数据的副本。我曾以为通过从JavaScript向Rust传递`&mut [u8]`（可变字节切片）可以避免这种分配。但这并没有奏效。[它并没有零次拷贝或一次拷贝，而是拷贝了两次](https://stackoverflow.com/questions/78634475/does-wasm-bindgen-copy-mut-u8-zero-times-or-twice/78634853#78634853')。

> 顺便提一下，对于`String`和`&str`，`wasm-bindgen`还在JavaScript的UTF-16 Unicode编码和Rust的UTF-8 Unicode编码之间进行转换。

接下来，在Rust中我们喜欢我们的**Option和Result类型**。我很高兴地报告它们属于“Yeps”。

![](../Images/e2d7c5632da35f7e6ed59d259c8d2f13.png)

Rust的`Some(3)`变成JavaScript的`3`，而Rust的`None`变成JavaScript的`null`。换句话说，`wasm-bindgen`将Rust的类型安全的空值处理转换为JavaScript的传统方式。在两种情况下，`null`/`None`都按照各自语言的习惯方式处理。

Rust的`Result`与`Option`的行为类似。Rust的`Ok(3)`变成JavaScript的`3`，而Rust的`Err("Some error message")`变成JavaScript的异常，可以通过`try`/`catch`捕获。请注意，Rust的`Err`中的值只能是实现了`Into<JsValue>`特征的类型。通常使用`String`会很有效。

最后，让我们看看**struct、enum和JSValue**，我们最后一组“Yeps”：

![](../Images/c1d7ee973c89183ee633c3f549b171f2.png)

激动人心的是，JavaScript可以构建并调用Rust结构体上的方法。为了实现这一点，你需要使用`#[wasm_bindgen]`标记结构体和任何可以由JavaScript访问的方法。

举个例子，假设你想避免将一个巨大的字符串从JavaScript传递到Rust。你可以定义一个Rust结构体，按序处理一系列字符串。JavaScript可以构建该结构体，将文件的块传入其中，然后请求结果。

JavaScript处理Rust枚举的方式不太令人兴奋。它只能处理没有关联数据（类似C枚举）的枚举，并将其值视为整数。

在兴奋感的中间位置，你可以将不透明的JavaScript值作为`JsValue`传递给Rust。Rust随后可以动态检查该值，确定其子类型，或者——如果适用——调用其方法。

这就结束了“Yeps”部分。现在来看一下“Nopes”部分。

## 不行！

这是Rust类型的类别，JavaScript（通过`wasm-bindgen`）无法处理。

![](../Images/2bdc9fc79862c1d8d09fffe0877940e7.png)

例如，不能通过引用传递`&u8`是可以接受的，因为你可以直接使用`u8`，而且它可能更高效。

不能返回字符串切片（`&str`）或常规切片（`&[u8]`）有些令人烦恼。为了避免生命周期问题，你必须返回一个拥有所有权的类型，如`String`或`Vec<u8>`。

你不能接受可变的`String`引用（`&mut String`）。不过，你可以接受一个`String`的值，通过修改它后再返回修改后的`String`。

我们如何解决“不可行”的问题？可以用向量（`Vec<T>`）或结构体来替代固定长度数组、元组和 128 位整数。

Rust 有集合和映射。JavaScript 也有集合和映射。然而，`wasm-bindgen` 库不会自动在它们之间转换。那么，如何将 Rust 中的 `HashSet` 传递给 JavaScript 呢？将其包装在你自己的 Rust 结构体中并定义所需的方法。然后，使用 `#[wasm-bindgen]` 标记该结构体和这些方法。

现在是我们的第三类。

## 避免

这是 Rust 类型的类别，JavaScript（通过 `wasm-bindgen`）允许使用，但你不应该使用它们。

![](../Images/f588e4f19fc187adcd88b822342e1283.png)

避免使用 `usize` 和 `isize`，因为大多数人会认为它们是 64 位整数，但在 WebAssembly（WASM）中，它们是 32 位整数。相反，使用 `u32`、`i32`、`u64` 或 `i64`。

在 Rust 中，`char` 是一个特殊的 `u32`，只能包含有效的 Unicode 标量值。相比之下，JavaScript 将 `char` 视为字符串。它检查 Unicode 有效性，但不强制字符串的长度为 1。如果你需要将 `char` 从 JavaScript 传递到 Rust，最好使用 `String` 类型，然后在 Rust 端检查长度。

# 规则 5：更改函数以使用支持的类型。将文件更改为通用的 `BufRead`。

通过了解 `wasm-bindgen` 支持的类型，我们可以修正我们希望暴露给 JavaScript 的函数。我们将规则 3 的示例函数保留为如下所示：

```py
#[wasm_bindgen]
pub fn good_turing(file_name: &str) -> Result<(u32, u32), io::Error> {
    let reader = BufReader::new(File::open(file_name)?);
    // ...
}
```

我们现在通过移除 `#[wasm_bindgen] pub` 来更改函数。我们还将函数修改为从通用读取器中读取，而不是文件名。使用 `BufRead` 可以提供更多的灵活性，使得该函数能够接受不同类型的输入流，例如内存数据或文件。

```py
fn good_turing<R: BufRead>(reader: R) -> Result<(u32, u32), io::Error> {
  // delete: let reader = BufReader::new(File::open(file_name)?);
  // ...
}
```

JavaScript 无法看到这个函数，因此我们创建一个包装函数来调用它。例如：

```py
#[wasm_bindgen]
pub fn good_turing_byte_slice(data: &[u8]) -> Result<Vec<u32>, String> {
    let reader = BufReader::new(data);
    match good_turing(reader) {
        Ok((prediction, actual)) => Ok(vec![prediction, actual]),
        Err(e) => Err(format!("Error processing data: {e}")),
    }
}
```

这个包装函数接受一个字节切片（`&[u8]`）作为输入，这是 JavaScript 可以传递的内容。该函数将字节切片转换为一个读取器，并调用内部的 `good_turing` 函数。内部函数返回一个 `Result<(u32, u32), io::Error>`。包装函数将此结果转换为 `Result<Vec<u32>, String>`，这是 JavaScript 可以接受的类型。

一般来说，我只愿意对既能原生运行又能在浏览器中以 WASM 运行的函数做一些小修改。例如，在这里，我愿意将函数改为处理通用读取器，而不是文件名。当 JavaScript 兼容性需要进行重大且不符合惯例的更改时，我会创建一个包装函数。

在这个示例中，在做出这些更改后，主代码现在可以编译。然而，原始测试仍然无法编译。修复测试是规则 6 的主题。

# 规则 6：调整测试，跳过不适用的部分。

规则 3 提倡将每个常规测试（`#[test]`）同时标记为WASM浏览器测试（`#[wasm_bindgen_test]`）。然而，由于WASM在访问系统资源（如文件）方面的限制，并非所有来自原生Rust的测试都可以在WebAssembly环境中运行。

在我们的示例中，规则 3 给出的测试代码无法编译：

```py
#[cfg(test)]
mod tests {
    use super::*;
    use wasm_bindgen_test::wasm_bindgen_test;
    wasm_bindgen_test::wasm_bindgen_test_configure!(run_in_browser);

    #[test]
    #[wasm_bindgen_test]
    fn test_process_file() {
        let (prediction, actual) = good_turing("./pg100.txt").unwrap();
        assert_eq!(prediction, 10223);
        assert_eq!(actual, 7967);
    }
}
```

这个测试代码失败的原因是我们更新后的 `good_turing` 函数需要一个通用的读取器，而不是文件名。我们可以通过从示例文件创建一个读取器来修复这个测试：

```py
 use std::fs::File;

    #[test]
    fn test_process_file() {
        let reader = BufReader::new(File::open("pg100.txt").unwrap());
        let (prediction, actual) = good_turing(reader).unwrap();
        assert_eq!(prediction, 10223);
        assert_eq!(actual, 7967);
    }
```

这是一个不错的原生测试。不幸的是，我们无法将其作为WASM浏览器测试运行，因为它使用了文件读取器——这是WASM不支持的功能。

解决方案是创建一个额外的测试：

```py
 #[test]
    #[wasm_bindgen_test]
    fn test_good_turing_byte_slice() {
        let data = include_bytes!("../pg100.txt");
        let result = good_turing_byte_slice(data).unwrap();
        assert_eq!(result, vec![10223, 7967]);
    }
```

在编译时，这个测试使用宏 `include_bytes!` 将一个文件转换为WASM兼容的字节切片。`good_turing_byte_slice` 函数将字节切片转换为读取器，并调用 `good_turing`。 （`include_bytes` 宏是[Rust标准库的一部分](https://doc.rust-lang.org/std/macro.include_bytes.html)，因此可以用于测试。）

请注意，额外的测试既是常规测试，又是WASM浏览器测试。我们尽量让我们的测试两者兼顾。

在我的 `range-set-blaze` 项目中，我几乎能够将所有测试都标记为常规测试和WASM浏览器测试。唯一的例外是：有一个测试使用了Criterion基准测试函数。Criterion无法在WASM浏览器中运行，因此我仅将该测试标记为常规测试（`#[test]`）。

在修复了我们的主代码（规则 5）和测试代码（规则 6）之后，我们能实际运行我们的测试吗？不一定，我们可能需要找到适合JavaScript的依赖。

> 附注：如果你使用的是Windows并运行WASM浏览器测试，你可能会看到“`ERROR tiny_http] Error accepting new client: A blocking operation was interrupted by a call to WSACancelBlockingCall. (os error 10004)`”。这与您的测试无关，可以忽略它。

# 规则 7：如有必要，改用适合JavaScript的依赖项。运行测试。

## 依赖项

示例项目现在可以编译。然而，在我的 `range-set-blaze` 项目中，仅修复我的代码和测试还不够。我还需要修复一些依赖项。具体来说，我需要在 `Cargo.toml` 中添加如下内容：

```py
[target.'cfg(all(target_arch = "wasm32", target_os = "unknown"))'.dev-dependencies]
getrandom = { version = "0.2", features = ["js"] }
web-time = "1.1.0"
```

这两个依赖使得随机数生成和提供了一个替代的时间库。默认情况下，浏览器中的WASM无法访问随机数或时间。这两个依赖封装了JavaScript函数，使得它们对Rust既可访问又符合惯用法。

*附注：有关在* `*Cargo.toml*` *中使用* `*cfg*` *表达式的更多信息，请参见我的文章：* [*九个Rust Cargo.toml的陷阱与误区*](https://medium.com/towards-data-science/nine-rust-cargo-toml-wats-and-wat-nots-1e5e02e41648)*：掌握Cargo.toml格式规则，避免挫折 |* Towards Data Science (medium.com)*.*

在 [WebAssembly — 分类 — crates.io](https://crates.io/categories/wasm) 中查找其他类似的 JavaScript 包装库。我还没有尝试过，但看起来很有趣的流行库包括：

+   [reqwest](https://crates.io/crates/reqwest) — `features=["wasm"]` — HTTP 网络访问

+   [plotters](https://crates.io/crates/plotters) — 绘图 — 包括一个 [demo](https://github.com/plotters-rs/plotters-wasm-demo)，它可以从 Rust 控制 HTML 画布对象

+   [gloo](https://crates.io/crates/gloo) — JavaScript 包装器工具包

另请参见 [上一篇文章](/nine-rules-for-running-rust-on-wasm-wasi-550cd14c252a) 中的规则 7 — 关于 WASM WASI — 了解更多关于修复依赖问题的内容。在本系列的 [下一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-embedded-systems-b0c247ee877e) 中 — 关于 `no_std` 和嵌入式 — 我们将深入探讨更多修复依赖的策略。

## 运行测试

在修复了依赖问题后，我们终于可以运行我们的测试，包括常规测试和浏览器中的 WASM 测试：

```py
cargo test
cargo test --target wasm32-unknown-unknown
```

记住，在幕后，我们对 `cargo test --target wasm32-unknown-unknown` 的调用：

+   查看 `.cargo/config.toml` 并找到 `wasm-bindgen-test-runner`（规则 3）。

+   调用 `wasm-bindgen-test-runner`。

+   使用 Chromedriver 在 Chrome 中运行我们的测试以进行测试。（规则 2，确保 Chrome for Testing 和 Chromedriver 已添加到你的路径中）。

在我们的测试成功后，我们现在准备从网页调用我们的 Rust 代码。

# 规则 8：将你的网页与你的函数连接起来。

要从网页调用你的 Rust 函数，你必须首先为网页打包你的 Rust 库。在规则 2 中，我们安装了 `wasm-pack`。现在，我们运行它：

```py
wasm-pack build --target web
```

这将编译你的项目，并创建一个 JavaScript 能够理解的 `pkg` 输出目录。

## 示例

在规则 1 中，我们创建了一个没有调用 Rust 的 `index.html` 文件。现在让我们将其修改为调用 Rust。以下是一个示例的 `index.html`，并附有相关变化的描述。

```py
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Good-Turing Estimation</title>
</head>
<body>
    <h1>Good-Turing Estimation</h1>
    <input type="file" id="fileInput" />
    <p id="lineCount"></p>

    <script type="module">
        import init, { good_turing_byte_slice } from './pkg/good_turing.js'; // These files are generated by `wasm-pack build --target web`
        const output = document.getElementById('lineCount');
        document.getElementById('fileInput').addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (!file) { output.innerHTML = ''; return } // No file selected
            const reader = new FileReader();
            // When the file is fully read
            reader.onload = async (e) => {
                await init(); // Ensure 'good_turing_byte_slice' is ready
                // View the memory buffer as a Uint8Array
                const u8array = new Uint8Array(e.target.result);
                try { // Actually run the WASM
                    const [prediction, actual] = good_turing_byte_slice(u8array);
                    output.innerHTML =
                        `Prediction (words that appear exactly once on even lines): ${prediction.toLocaleString()}<br>` +
                        `Actual distinct words that appear only on odd lines: ${actual.toLocaleString()}`;
                } catch (err) { // Or output an error
                    output.innerHTML = `Error: ${err}`;
                }
            };
            // Now start to read the file as memory buffer
            reader.readAsArrayBuffer(file);
        });
    </script>
</body>
</html>
```

让我们了解一下关注的变化。

+   下面这行代码将两个函数从 `pkg/good_turing.js` 模块文件导入到 JavaScript 中，该文件是我们使用 `wasm-pack` 创建的。默认函数 `init` 初始化我们用 Rust 生成的 WebAssembly (WASM) 模块。第二个函数 `good_turing_byte_slice` 通过将其名称放在大括号中显式导入。

```py
import init, { good_turing_byte_slice } from './pkg/good_turing.js';
```

+   创建一个新的 `FileReader` 对象，进行一些设置，然后将文件作为字节数组读取。

```py
const reader = new FileReader();
// ... some setup code ...
// Now start to read the file as bytes.
reader.readAsArrayBuffer(file);
```

+   这是我们在文件完全读取后执行的代码设置：

```py
reader.onload = async (e) => {
//...
};
```

+   这一行确保 WASM 模块已初始化。第一次调用时，模块会被初始化。之后的调用不会有任何动作，因为模块已经准备好了。

```py
await init(); // Ensure 'good_turing_byte_slice' is ready
```

+   从读取的文件中提取字节数组。

```py
// View the memory buffer as a Uint8Array
const u8array = new Uint8Array(e.target.result);
```

+   调用 Rust 生成的 WASM 函数。

```py
const [prediction, actual] = good_turing_byte_slice(u8array);
```

> 附带说明：这里的`good_turing_byte_slice`是一个常规（同步）函数。不过，如果你愿意，可以在 Rust 端标记为`async`，然后在 JavaScript 端用`await`调用它。如果你的 Rust 处理速度较慢，这可以让你的网页更加流畅。

+   显示结果。

```py
output.innerHTML =
    `Prediction (words that appear exactly once on even lines): ${prediction.toLocaleString()}<br>` +
    `Actual distinct words that appear only on odd lines: ${actual.toLocaleString()}`;
```

+   如果有错误，显示错误信息。

```py
try { // Actually run the WASM
    // ...
} catch (err) { // Or output an error
    output.innerHTML = `Error: ${err}`;
}
```

该 [最终代码](https://github.com/CarlKCarlK/rustconf24-good-turing) 在 GitHub 上，包含一个 [README.md](https://github.com/CarlKCarlK/rustconf24-good-turing/blob/main/README.md)，解释了它的功能。点击 [此链接](https://carlkcarlk.github.io/rustconf24-good-turing/) 查看实时演示。

## `range-set-blaze`

我根据用户的要求将 `range-set-blaze` 移植到 WASM，以便他们可以在自己的项目中使用它。`[range-set-blaze](https://github.com/CarlKCarlK/range-set-blaze)` 项目通常作为库在其他项目中使用。换句话说，你通常不会期待 `range-set-blaze` 成为网页的核心部分。然而，我确实做了一个小的演示页面。你可以 [浏览它](https://carlkcarlk.github.io/range-set-blaze/wasm-demo) 或 [查看它的 index.html](https://github.com/CarlKCarlK/range-set-blaze/blob/gh-pages/docs/wasm-demo/index.html)。该页面展示了 `range-set-blaze` 如何将一个整数列表转换为排序后的不相交范围列表。

> 附带说明：**免费在 GitHub 上托管你的 WASM 在浏览器中的项目**
> 
> 1\. 在你的项目中创建一个 `docs` 文件夹。
> 
> 2\. 执行 `wasm-pack build --target web`。
> 
> 3\. 将 `index.html` 和 `pkg` 复制（而不是移动）到 `docs` 文件夹中。
> 
> 4\. 删除 `docs/pkg` 中的 `.gitignore` 文件。
> 
> 5\. 将项目提交到 GitHub。
> 
> 6\. 前往 GitHub 上的项目。然后进入“设置”，“Pages”。
> 
> 7\. 设置分支（在我的情况下是 `main`）和文件夹为 `docs`。保存。
> 
> 8\. URL 将基于你的账户和项目名称，例如，[https://carlkcarlk.github.io/rustconf24-good-turing/](https://carlkcarlk.github.io/rustconf24-good-turing/)
> 
> 9\. 要更新，重复步骤 2 到 5（包括）步骤。

# 规则 9：将`wasm-pack`添加到你的 CI（持续集成）测试中。

你的项目现在已经可以在浏览器中编译为 WASM，运行测试通过，并展示在网页上。完成了吗？还没完全完成。因为，正如我在第一篇文章中所说：

> *如果不在 CI 中，它就不存在。*

回想一下，持续集成（CI）是一个系统，每次你更新代码时，它都可以自动运行测试，确保你的代码继续按预期工作。在我的例子中，GitHub 托管了我的项目。以下是我添加到 `.github/workflows/ci.yml` 文件中的配置，以便在浏览器中测试我的 WASM 项目：

```py
 test_wasm_unknown_unknown:
    name: Test WASM unknown unknown
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Set up Rust
        uses: dtolnay/rust-toolchain@master
        with:
          toolchain: stable
          target: wasm32-unknown-unknown
      - name: Install wasm-pack
        run: |
          curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
      - name: Run WASM tests with Chrome
        run: |
          rustup target add wasm32-unknown-unknown
          wasm-pack test --chrome --headless
```

通过将浏览器中的 WASM 集成到 CI 中，我可以放心地为我的项目添加新代码。CI 会自动测试我的所有代码，确保它将来继续支持浏览器中的 WASM。

所以，这就是—将你的 Rust 代码移植到浏览器中的 WASM 的九条规则。这里有一件事让我感到惊讶：

**不好的部分：**

+   在浏览器中为WASM设置测试是很难的。特别是，Chrome测试和Chromedriver的安装与管理非常困难。

+   在浏览器中使用WASM违反了Rust的格言：“如果它能编译，说明它能工作”。如果使用不支持的功能——例如，直接文件访问——编译器不会捕捉到错误。相反，错误会在运行时发生。

+   传递字符串和字节向量会创建数据的两个副本，一个在JavaScript端，一个在Rust端。

**优点：**

+   在浏览器中使用WASM既有用又有趣。

+   你可以标记你的常规测试，让它们也在浏览器中的WASM中运行。只需同时为测试添加这两个属性：

```py
#[test]
#[wasm_bindgen_test]
```

+   你可以在浏览器中运行WASM，而不需要移植到`no_std`。不过，在浏览器中使用WASM是朝着在嵌入式/`no_std`环境中运行迈出的一个重要步骤。

敬请期待！在[下一篇文章](https://medium.com/towards-data-science/nine-rules-for-running-rust-on-embedded-systems-b0c247ee877e)中，我们将看看如何将Rust代码移植到嵌入式环境并通过`no_std`运行。这将使你的代码能够在小型设备上运行，我觉得非常酷。

> 对未来的文章感兴趣吗？请[在Medium上关注我](https://medium.com/@carlmkadie)。我写关于Rust和Python、科学编程、机器学习和统计学的文章。我倾向于每个月写一篇文章。
