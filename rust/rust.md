# Rust入门

`rustup`是一个管理 Rust 版本和相关工具的命令行工具）。

```bash
$ rustup update  // 更新rust
$ rustup self uninstall // 卸载 Rust 和 rustup
$ rustup doc // 在浏览器中打开本地文档

$ rustc main.rs // 编译程序
$ rustfmt main.rs // 代码格式化

```
`Cargo` 是 Rust 的构建系统和包管理器。大多数 Rustacean 们使用 Cargo 来管理他们的 Rust 项目，因为它可以为你处理很多任务，比如构建代码、下载依赖库，以及编译这些库。（我们把代码所需要的库叫做依赖（dependency））。

```bash
$ cargo new hello_cargo // 创建一个新项目
$ cargo build // 编译debug版本/release版本 "cargo build --release"

$ cargo run // 编译并运行
$ cargo check //检查代码不编译

$ cargo update // 更新依赖库
$ cargo doc --open // 浏览器中打开本地依赖库文档
```