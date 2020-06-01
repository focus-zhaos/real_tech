## Rust 学习笔记

20190124


## 0.概述

Rust是编译型语言，rustc是编译器，cargo是项目管理工具。rustup doc可以查看本地文档。

## 1. cargo

cargo换源：https://lug.ustc.edu.cn/wiki/mirrors/help/rust-crates

#### 1.1 Cargo.toml

cargo期望源文件位于src目录。cargo需要一个配置文件`Cargo.toml`，toml即(Tom's Obvious, Minimal Language)，格式类似于ini文件。下面是一个例子：

```
[package]

name = "hello_world"
version = "0.0.1"
authors = ["jczhang <zhjcyx@gmail.com>"]
```

#### 1.2 命令

建好Cargo.toml文件之后，可以用`cargo build`进行程序构建。生成的二进制路径应该是`target/debug/hello_world`。也可以合并这两步直接用`cargo run`编译和执行二进制程序。注意，这样使debug版本，如果要生成release版本，应该用`cargo build --release`,这样二进制路径应该是`target/release/hello_world`。Release编译速度较慢，但程序执行会更快。

Cargo也提供建立“骨架项目”的命令，如：

```
cargo new hello_world
```

这个命令会创建一个`Cargo.toml`和一个包含`main.rs`的`src目录`。`main.rs`已经写好了一行 `println!("Hello, world!");` 代码，并且项目目录被创建好了一个**git仓库**。

`cargo check`可以检查是否可以正确编译而不编译，这样可以比build更快地检查语法错误。


## 2. 杂项

#### 2.1 打印 println!

* 用`println!`进行打印时，占位符是`{}`。其他和C语言`printf`很类似。例：

```rust
println!("x = {} and y = {}", x, y);
```

但是`{}`对应的是struct的`Display`方法，如果没有会panic，那么很多情况下我们可以用`{:?}`或者`{:#?}`，其对应对象的`Debug`方法。其他占位符还有：

| 占位符 | 对应方法 |
| ------ | -------- |
| 空     | Display  |
| ?      | Debug    |
| o      | Octal    |
| x      | LowerHex |
| X      | UpperHex |
| p      | Pointer  |
| b      | Binary   |
| e      | LowerExp |
| E      | UpperExp |

* crate是rust的库或者二进制，分别称为库crate，或者二进制crate。crate中又包含有trait。 运行 `cargo doc --open` 命令来构建所有本地依赖提供的文档，并在浏览器中打开，这样可以确定包含哪个trait和调用声明方法。

```rust
use rand::Rng // rand是一个库crate，Rng是rand的一个trait
```

#### 2.2 其他

- `//` 表示注释。
- `use` 引入包含的包。
- 缩进用**空格而非tab**。
- 声明一个变量用`let`，如：

```rust
let foo = 0;        // 可变
let mut bar = 1;    // 不可变
let mut guess = String::new() // 创建可变变量，并绑定到新的String空实例上。
```

## 3. 变量

#### 3.1 可变，不可变和常量

Rust的**变量**分为可变(mutable)和不可变(immutable)变量，默认是不可变的。与不可变量类似的是**常量**(const)，但是常量只能以常量表达式初始化，并且需要指明类型。例：


```rust
let x = 5;                          // 不可变量
let mut y = 6;                      // 可变量   
const MAX_POINTS: u32 = 100_000;    // 常量，注意数字中的下划线是为了可读性
```

#### 3.2 隐藏 (Shadowing)

Rust中变量声明可以重名，先声明的量会被后声明的同名量**隐藏** (Shadowing)。

利用“隐藏”，可以“改变”不可变量的值。这种改变方法，其实比改变可变量更灵活，因为实质上我们可以创建一个同名不同类型的新的不可变量，例如：

```rust
let spaces = "   ";
let spaces = spaces.len();
```

#### 3.3 数据类型

Rust是静态类型语言，即编译时需要确定所有变量的类型。

* 整形：

| 长度(bits) | 有符号 | 无符号 |
| ---------- | ------ | ------ |
| 8          | i8     | u8     |
| 16         | i16    | u16    |
| 32         | i32    | u32    |
| 64         | i64    | u64    |
| arch       | isize  | usize  |

(对于isize/usize，若在64位机器上即使64位，否则为32位。)

* 整形字面值：


| 数字字面值     | 例子        |
| -------------- | ----------- |
| Decimal        | 98_222      |
| Hex            | 0xff        |
| Octal          | 0o77        |
| Binary         | 0b1111_0000 |
| Byte (u8 only) | b'A'        |


* 浮点型：

| 长度(bit) | 类型 |
| --------- | ---- |
| 32        | f32  |
| 64        | f64  |

* 布尔型： `bool`，只有两个可能值：`true`和`false`

* 字符型： `char`， Rust中的char并非1个字节，它支持unicode。char字符用单引号包围。如：

```rust
let c = 'z';
let z = '哈';
let heart_eyed_cat = '😻';
```

#### 3.4. 复合类型

Rust 复合类型包括tuple和array：

* tuple：

```rust
// 声明tuple：
let foo = (500, 6.4, 1);
let x: (i32, f64, u8) = (500, 6.4, 1);
// 解构tuple:
let (x, y, z) = foo;
// 索引tuple，注意是用“.” 而非广泛使用的中括号
let five_hundred = x.0;
let one = x.2;
```

* Array

```rust
// 声明一个数组
// 注意类型后加分号，再加数字，这里说明a数组包含了5个i32类型的元素
let a: [i32; 5] = [1, 2, 3, 4, 5];
// 数组的索引是用中括号 "[]"
let first = a[0];
```

注意：如果索引超出了数组长度，Rust 会 panic，这是 Rust 术语，它用于程序因为错误而退出的情况。

## 4. 函数

* 函数参数的例子：

```rust
fn main() {
    another_function(5, 6);
}

fn another_function(x: i32, y: i32) {
    println!("The value of x is: {}", x);
    println!("The value of y is: {}", y);
}
```

和C/C++ 一样，定义函数时必须要注明参数类型。

* 函数返回的例子：

```rust
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```

## 5. 分支和循环

分支主要有if，循环结构有loop、for和while。

**if** 例1：

```rust
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

**if** 例2：

```rust
fn main() {
    let condition = true;
    // 注意： 这种情况下，变量必须只有一个类型
    // 比如5和6这里都是i32类型的
    let number = if condition {
        5
    } else {
        6
    };
    println!("The value of number is: {}", number);
}
```

**loop** 例1：

```rust
loop {
    println!("again!");
}
```

**loop** 例2：

```rust
let result = loop {
    counter += 1;

    if counter == 10 {
        break counter * 2;
    }
};
```

**while** 例：

```rust
while number != 0 {
    println!("{}!", number);

    number = number - 1;
}
```

**for** 例1：

```rust
let a = [10, 20, 30, 40, 50];
for element in a.iter() {
    println!("the value is: {}", element);
}
```

**for** 例2：

```rust
// rev 即反向
for number in (1..4).rev() {
    println!("{}!", number);
}
```

## 6. 所有权系统

所有权系统是Rust与GC语言或手动内存管理语言的主要区别。

跟踪哪部分代码正在使用**堆**上的哪些数据，最大限度的减少堆上的重复数据的数量，以及清理堆上不再使用的数据确保不会耗尽空间，这些问题正是所有权系统要处理的。

几个所有权基本规则：

```
Rust 中的每一个值都有一个被称为其 所有者（owner）的变量。
值有且只有一个所有者。
当所有者（变量）离开作用域，这个值将被丢弃。
```

对于一个字符串，字面值的字符串是直接硬编码到代码中并存到栈中的，而可变长的String类型必须是运行时从系统分配的堆中的内存，并且要在合适的时候将内存返还给操作系统。

Rust 在变量离开作用域时自动回收内存，它会在`}`处自动调用一个称谓drop的函数。

* 参数传递和所有权

只考虑堆上分配变量的情况，比如当把一个堆上变量x从foo函数传给一个bar函数，bar会接管x所有权，这个变量x也会在被调用函数结束时被drop调，除非它以返回值的形式再传回给foo。例：

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership 将返回值
                                        // 移给 s1

    let s2 = String::from("hello");     // s2 进入作用域

    let s3 = takes_and_gives_back(s2);  // s2 被移动到
                                        // takes_and_gives_back 中, 
                                        // 它也将返回值移给 s3
} // 这里, s3 移出作用域并被丢弃。s2 也移出作用域，但已被移走，
  // 所以什么也不会发生。s1 移出作用域并被丢弃

fn gives_ownership() -> String {             // gives_ownership 将返回值移动给
                                             // 调用它的函数

    let some_string = String::from("hello"); // some_string 进入作用域.

    some_string                              // 返回 some_string 并移出给调用的函数
}

// takes_and_gives_back 将传入字符串并返回该值
fn takes_and_gives_back(a_string: String) -> String { // a_string 进入作用域

    a_string  // 返回 a_string 并移出给调用的函数
}
```

* 引用和所有权借出

不过，还可以用引用的形式在传参的时候不转移所有权，只是将变量借出去，这样所有权在函数返回后仍然在，x也没有被bar释放。但是有一个局限是，bar中不能更改x(只读？)。引用(`&`)和解引用(`*`)的符号和C++很像，但注意传递传递发起端也要加引用符号`&`。例：

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1); // 这里返回后，s1的所有权仍是main的

    println!("The length of '{}' is {}.", s1, len);
}
fn calculate_length(s: &String) -> usize {
    s.len()
} // 这里s并不会被drop
```

要想让所引用的变量在被“借出”过程中可变，还是需要在参数列表中加入`mut`关键字(即用`&mut`代替`&`)。例：

```rust
fn main() {
    let mut s = String::from("hello");
    change(&mut s); // 注意，传参是也要加 &mut
}
fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

还有一个限制，对于同一份堆上变量(数据)，在同一作用域中只能由它的一份可变引用，或者多份不可变引用。这感觉上就类似读写锁，可以有多个读锁被同时持有，读和写不能同时持有，写和写也不能。Rust正是利用了这个思想，在编译时就防止了可能的数据竞争。

还有一点需要注意的是，Rust不允许以引用作为返回值来外借所有权，因为返回是生命周期会结束，这回造成dangling pointer悬停指针，Rust不允许这样。例：

```rust
// 错误的：
fn dangle() -> &String { // dangle 返回一个字符串的引用
    let s = String::from("hello"); // s 是一个新字符串
    &s // 返回字符串 s 的引用
} // 这里 s 离开作用域并被丢弃。其内存被释放。
  // 危险！

// 正确的：
fn no_dangle() -> String {
    let s = String::from("hello");
    s
}
```

* 对于堆中的变量如String，赋值后ownership会被转移，这样防止了**double free**问题。比如下边的代码就是错的，因为s1被转移到s2了之后就不能再用了。

```rust
let s1 = String::from("hello");
let s2 = s1;
println!("{}, world!", s1);
```

为了进行真正的拷贝，可以调用很多对象(包括string)实现的`clone`方法：

```rust
let s1 = String::from("hello");
let s2 = s1.clone();
println!("s1 = {}, s2 = {}", s1, s2);
```

而栈中的int,char等类型的变量开销比较低，所以会拷贝，也不存在ownership转移的问题，这是因为它们实现了`Copy` trait。注意，实现了`Drop` trait的类型无法实现`Copy` trait。

* slice 属于引用，也没有所有权(ownership)： 

和Python的slice切片类似，String、数组等都支持切片，语法是`&foo[m..n]`或者`foo[m..=n]`，后者包含`n`，而前者只到`n-1`。

slice 属于引用，也是没有所有权的，其实和普通引用的区别只是slice的范围小于整体的范围。

字符串类型`String`对应的切片引用类型为`&str`，字面值其实就是`&str`类型，字面值和字符串slice都是不可变的。

数组比如i32数组所对应的切片类型是`&[i32]`。



#### 7.2 lifetime标记

- 函数声明中引用的lifetime标记

如上所述，若函数返回是引用，不可能是来自函数内部的"外借"，那返回的引用只可能是传输参数的引用之一。

但是rust 的 lifetime checker 又需要知道返回值的生命周期，所以就需要生命周期标注。

声明周期标注到引用的语法如下：

```rust
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```

函数声明中的标注的例子如下：

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

这种标注的必要性就类似于泛型的函数定义：泛型函数可能应用于各种类型，函数所返回引用的lifetime也可能符合各种输入引用的声明周期(这里的符合指的是有相同标记的最小lifetime，比如这里lonogest的返回引用的lifetime等于调用longest函数的传入参数x, y的最小的lifetime)。

* struct声明中引用的lifetime标记

例子：

```rust
struct ImportantExcerpt<'a> {
	part: &'a str,
}
```

* 编译器的引用lifetime标记自动推断

编译器会遵循3条规则来推断，推断后如果还有问题就会报错：

1. 每个输入参数都有自己的 lifetime 标记，比如`fn foo(x: &i32, y: &i32);`会被推断为`fn foo<'a, 'b>(x: &'a i32, y: &'b i32);`；
2. 如果只有1个输入参数，那么将所有输出参数标为输入参数的标记；

3. 如果声明是个struct的方法而非一个普通函数，那么可能会有`&self`或`&mut self`，这种情况下输出的lifetime参数都标为和 `self` 一样。

第1、2条规则在很多只有1个输入引用、1个或多个输出引用的情况下很有用。

第3条规则，让我们在定义struct 及其方法时很少需要手动标注lifetimes。

首先，一般情况下和泛型参数类似，需要在`impl`和struct名字之后各标注，因为标注也是这个struct 的一部分。如：`impl<'a> ImportantExcerpt<'a> {...}`。然后，之后的method一般不用加lifetime 标记，因为第三条规则。

* Result 返回的lifetime标记：

`Err`如果携带字符串，需要标记为`'static`，例：

```rust
pub fn new(args: &[String]) 
		-> Result<Config, &'static str> {
     	...
}
```



## 7. 泛型 (generic data type)

(对应书中第10章)

注意泛型由于面向多种可能的类型，所以有些方法，甚至例如比较大小的`std::cmp::PartialOrd` trait 都是没有实现的，所以需要有 "trait bounds" 的概念。

#### 7.1 泛型函数

比如书中如下例子：`largest` 泛型函数接收list的切片slice(也即slice，如笔记第6节所述)，main中传入largest的是list的引用(等价于list整体的slice？)。**但是这里编译不能通过，因为泛型假定不只是当前的`i32`和`char`可能调用`>`比较，还有很多类型没有比较的trait。**

```rust
fn largest<T>(list: &[T]) -> T {
    let mut largest = list[0];
 		for &item in list.iter() {
        if item > largest {
            largest = item;
        }
    }
 		largest
}
fn main() {
    let number_list = vec![34, 50, 25, 100, 65];
 		let result = largest(&number_list);
    println!("The largest number is {}", result);
 		let char_list = vec!['y', 'm', 'a', 'q'];
 		let result = largest(&char_list);
    println!("The largest char is {}", result);
}
```

#### 7.2 泛型数据结构struct

例子如下，注意如果定义相应struct变量时，同一个泛型标识符只能定义成同一种变量，所以这里区分了`T`和`U`。

```rust
struct Point<T, U> {
    x: T,
    y: U,
}
 fn main() {
    let both_integer = Point { x: 5, y: 10 };
    let both_float = Point { x: 1.0, y: 4.0 };
    let integer_and_float = Point { x: 5, y: 4.0 };
}
```

#### 7.3 泛型枚举enum

常见的`Option`和`Result`定义如下，它们都可以用`match`结构进行分支，或者用更简洁的`if let`语法。

```rust
enum Option<T> {
    Some(T),
    None,
}
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

关于`match`和`if let`，以下两种用法等价：

```rust
let some_u8_value = Some(0u8);
// 用match：
match some_u8_value {
    Some(3) => println!("three"),
    _ => (),
}
// 用 if let
if let Some(3) = some_u8_value {
    println!("three");
}
```



## 8. 错误处理

Rust中，错误处理可以分为两大类，不可恢复的(unrecoverable)或者可恢复的(recoverable)。

#### 8.1 不可恢复的panic

不可恢复的即panic，它还可以通过主动调用`panic!`宏来进行，并且可以用`RUST_BAKTRACE=1 cargo run`来进行在panic的时候自动打印函数调用栈。

#### 8.2 可恢复的Result

可恢复的错误处理一般用`enum Result`返回结果，如：

```rust
use std::fs::File;
fn main() {
    let f = File::open("hello.txt");
 		let f = match f {
        Ok(file) => file,
        Err(error) => {
            panic!("There was a problem opening the file: {:?}", error)
        },
    };
}
```

这里用`match`分支将open的Result类型返回结果解析成file或者error对象，并且在error的情况下主动panic! (这里只是没选择进行恢复)。

**Reuslt的shortcuts**

Rust有很多关于这些错误的语法糖：

`.unwrap()`在Result为`Ok`是直接返回`Ok`中的值，在`Err`时直接panic。

`.expect("my err message ...")`和unwrap很像，只是多了panic时的错误提示字符串。

`.unwrap_or_else()`在Ok时和`.unwrap()`相同，在Err时允许不引起panic而是允许程序员自定义处理错误。自定义的形式是闭包`closure`，例：

```rust
use std::process;
fn main() {
    let args: Vec<String> = env::args().collect();
  	let config = Config::new(&args).unwrap_or_else(|err| {
      		println!("Problem parsing arguments: {}", err);
      		process::exit(1);
		});
  	...
}
```



#### 8.3 错误传播 "?"

`?`可以跟在被调用的函数/方法后，它的作用的是在Result为`Err` 的情况下将错误传给上一层的调用者。这样做很自然有个前提：当前函数的返回值也为Reuslt 枚举。例：

```rust
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();
		File::open("hello.txt")?.read_to_string(&mut s)?;
		Ok(s)
}
```



## 9. 测试



#### 9.1 Rust的测试机制

类似`#[derive]`这个rust属性，写测试代码时在`fn`前需要加`#[test]`这个属性。rust中，属性即代码的元数据。这样`cargo test`的时候会运行标记有`test`的代码。

`assert!`宏在test中很常用。它与`==`结合的"升级版"封装有`assert_eq!`和`assert_ne!`。`assert!`宏同样接收第二个作为格式字符串输出的参数。

在`#[test]`和测试函数体间加上`#[should_panic]`可以测试应当panic的情况。但是，这不太精确，因为它的精度在测试函数的粒度，为了更准确加入了`expected`参数，它指定的字符串应该是所期望panic的子串才算测试通过，如：

```rust
impl Guess {
    pub fn new(value: u32) -> Guess {
        if value < 1 {
            panic!("Guess value must be greater than or equal to 1, got {}.",
                   value);
        } else if value > 100 {
            panic!("Guess value must be less than or equal to 100, got {}.",
                   value);
        }
 Guess {
            value
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;
		#[test]
    #[should_panic(expected = "Guess value must be less than or equal to 100")]
    fn greater_than_100() {
        Guess::new(200);
    }
}
```

* test线程

Rust test 默认多test被**多线程**分别并行执行，某个thread死了，就认为是哪个test失败了。由于多线程，所以rust测试挺快的，但是要注意测试函数间不要用共享环境，比如工作文件或者共享环境变量，所以当你想控制线程数时可以用 `--test-threads`标志：

```rust
cargo test -- --test-threads=1
```

这样，虽然test执行慢了，但是多个test之间真正做到了不相互影响。

* test函数输出

类似的，`—nocapture`参数则可以放test 中的stdout真正展示给我们，而不是默认地被cargo test隐藏起。但是注意由于tests是并行执行，stdout也可能互相交错。

* 只执行部分test

可以在`cargo test`后直接跟要执行test函数所包含的子串。

如果某些测试很少执行或者执行很费时间所以一般不执行，应该在`#[test]`和测试函数中间加上`#[ignore]`，要执行时需要：

```bash
cargo test -- --ignored
```



#### 9.2 单元测试和集成测试

Rust 社区将测试分为两大类：**单元测试** unit tests 和 **集成测试** integration tests。

* 单元测试

rust的单元测试支持私有函数的测试，其被放到有`#[cfg(test)]`标识的test module里面。例子：

```rust
// src/lib.rs
pub fn add_two(a: i32) -> i32 {
    internal_adder(a, 2)
}
 fn internal_adder(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn internal() {
        assert_eq!(4, internal_adder(2, 2));
    }
}
```

* 集成测试

而集成测试指的是在project目录中与`src`目录平级的`tests`目录，目录下有多个rs文件，每个代表一个集成测试。集成测试与单元测试不同，不用指定`#[cfg(test)]`，rust编译器会自动确定；必须要`extern crate XXX`，因为`tests`文件夹中每个test都是单独的crate。例子：

```rust
// tests/integration_test.rs 
extern crate adder;

#[test]
fn it_adds_two() {
    assert_eq!(4, adder::add_two(2));
}
```

可以用`cargo test --test TEST_NAME`来只测`TEST_NAME.rs`这个集成测试。

需要注意的是，如果我们的多个测试rs文件要共享一些代码，我们不能命名成类似`tests/common.rs`而应该创建成`tests/common/mod.rs`，这样避免rust 将 common也当成一个测试，这是因为rust 的集成测试不会将`tests`的子目录源码当做一个测试。例子：

```rust
// tests/integration_test.rs 
extern crate adder;

mod common;

#[test]
fn it_adds_two() {
    common::setup();
    assert_eq!(4, adder::add_two(2));
}
```



**TDD (test-driven development)步骤**

1. 想到然后写出一个肯定会失败的测试
2. 修改被测试代码使这个测试通过
3. 调整代码使它可以一直通过这个测试
4. 回到第一步



## 10. Project 管理

#### 10.1 什么应该放main.rs

一般比较大的程序应该将主要逻辑放到`lib.rs`而不是`main.rs`，`main.rs`只应该包括：

* 命令行参数解析(太复杂的话也要放lib.rs)
* 其他的一些配置
* 调用`lib.rs`中的某些run运行函数
* 处理run运行函数的错误

`main.rs`处理的少是因为我们无法对`main`函数进行测试，所有的测试都放在`lib.rs`了。根本目的是保证main中的代码可以用人眼分辨出对错。





## 11. 函数式编程

（rust book chapter 13）

#### 11.1 闭包 closure

注意，闭包虽然不用在输入变量标明参数的类型，但是不能在后续使用时在一种以上的类型中使用，这是因为每个closure都有自己隐含的类型，当我们想把closure放入struct时，我们也一定要显式标明类型。



## 12. 智能指针

（rust book chapter 14）

tbc

## 13. crate.io

（rust book chapter 15）

tbc

## 14. 并发和多线程编程

（rust book chapter 16）



#### 14.1 Rust 线程

有些语言使用1：1的线程模型，有些使用M：N的。使用M：N的线程模型需要较多的编程语言runtime代码支持。由于Rust为了保证随时可以调用C语言来保证高性能，需要使用小runtime，所以用了和操作系统线程1：1的线程模型。

创建一个线程需要向`thread::spawn`函数中传入一个闭包，如下是书中的例子：

```rust
// src/main.rs
use std::thread;
use std::time::Duration;
 fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });
 for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
   handle.join().unwrap();
}

Klabnik, Steve. The Rust Programming Language (p. 345). No Starch Press. Kindle 版本. 
```

但需要注意，在rust中，如主线程退出，子线程也会马上退出。由于`thread::spawn`的返回时是一个`JoinHandle`，我们可以用`join`方法等待线程完成（`handle.join().unwrap();`）。

如果spawn线程的闭包代码中要使用主程序的变量，由于rust的所有权机制，需要交所有权转移到闭包内，方法是在`||`前加`move`关键字：

```rust
thread::spawn(move || {
  											....
											});
```

#### 14.2 线程通信

* 例1：线程间消息传递（channel）通信

比如书中例子说明了如何move和channel（关于channel，go语言有类似的概念，go语言对线程通信的观点是“Do not communicate by sharing memory; instead, share memory by communicating.”）来实现线程间通信。

```rust
use std::thread;
use std::sync::mpsc;
 fn main() {
    let (tx, rx) = mpsc::channel();
 		thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();         // 子线程发送
      	// 子线程发送val后，val不能再被子线程使用
    });
 		let received = rx.recv().unwrap(); // 主线程接收
   																		// 阻塞到接收成功
    println!("Got: {}", received);
}
```

上述代码中，`rx.recv()`会阻塞到消息接收完成，并返回`Result<T, E>`；与之对应的，可以选择使用`rx.try_recv()`来达到非阻塞立即返回的效果。

还需注意，除了主线程的变量可以用过闭包前的`move`转移到子线程中，通过`send()`发送的变量也被move到了接收方，因此代码中`val`不能在被`send`之后被读写。

* 例2：线程间共享内存通信

任何编程语言中，消息传递通信就像是单所有权，当消息被发走之后就不应在使用它。上例也说明，Rust通过编译器对此作出了限制。

而共享内存是一种多所有权形式，同一片内存可以被多个线程并发读写。要达到共享数据的临时独占，人们一般使用Mutex（mutaul exclusive）锁。但由于互斥量的管理十分tricky，因此很多人热衷于channel。不过Rust的类型系统和所有权规则可以避免很多mutex相关的错误：

#### 14.3. Sync和Send traits

上述大部分Rust并发都实现于基础库而非语言层面，而Sync和Send这两种std::marker traits是少有的集成于Rust语言的与并发相关的概念。

实现`Send`这一marker trait的类型允许被在线程之间传送，大多数Rust类型实现了`Send`。但包括`Rc<T>`在内的少数类型没有实现`Send`，因此无法在创建线程时被"move"到闭包代码中使用。例如，对于`Rc><T>`，需要用支持并发的`Arc<T>`代替。

`Sync`这一marker trait意味着：对于一个实现`Sync`的类型，它可以在多个线程中被安全地引用。也就是说，如果类型`T`是Sync，那么`&T`就是Send。

全由Send构成的类型还是Send。全由Sync构成的类型还是Sync。手动实现Send和Sync需要使用到unsafe Rust code，不推荐。



## 16. unsafe Rust

#### 16.1. Raw pointers

从引用创建immutable和mutable的raw pointer的例子如下：

```rust
let mut num = 5;
 let r1 = &num as *const i32;
let r2 = &mut num as *mut i32;
```

从特定内存地址创建immutable和mutable的raw pointer的例子如下：

```rust
let address = 0x012345usize;
let r = address as *const i32;
```

从引用创建可以保证raw pointer真的指向内容，但是后者（从内存地址创建）无法保证，因为特定地址内容可能会被编译器优化掉或者根被就没有。

定义raw pointer并非unsafe，但是对它们的使用需要在unsafe代码块进行：

```rust
let mut num = 5;
let r1 = &num as *const i32;
let r2 = &mut num as *mut i32;
 unsafe {
    println!("r1 is: {}", *r1);
    println!("r2 is: {}", *r2);
}
```

