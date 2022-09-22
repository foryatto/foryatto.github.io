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

# 通用编程概念

## 变量和可变性

**变量**

```rust
let a = 12;  // 默认不可变
let mut a = 5; // 使用mut声明变量可变
```

**常量**
```rust
// Rust常量的命名约定是全部字母都使用大写，并使用下划线分隔单词
const MATH_PI : f32 = 3.14; // 常量必须注明数据类型
```

**遮蔽（shadow）**
```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);  // 12
    }

    println!("The value of x is: {}", x); //6

    let spaces = "   ";
    let spaces = spaces.len(); // 和mut不同, 遮蔽可以改变变量类型, 如从string变成int
}
```

## 数据类型
```rust
// int / unsigned
// isize (usize) 类型取决于程序运行的计算机体系结构,若使用 64 位架构系统则为 64 位，若使用 32 位架构系统则为 32 位。
let a = 5u8 // u8

// float f32 f64
// 默认浮点类型是 f64
let x = 2.0; // f64
let y: f32 = 3.0; // f32

// bool
let f: bool = false;

// char, Rust的字符类型大小为 4 个字节，表示的是一个 Unicode 标量值
let c = 'z';
let heart_eyed_cat = '😻';
```

```rust
// 元组，长度固定
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup; // 使用模式匹配来解构（destructure）元组的一个值

let x = tup.0;
let y = tup.1;
let z = tup.2;

// 数组
let a: [i32; 5] = [0, 0, 0, 0, 0];
let a = [0; 5]; // 同上
println!(a[0])
```

## 函数
