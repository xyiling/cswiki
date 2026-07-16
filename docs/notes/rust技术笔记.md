## 入门 rust

1. 使用let声明变量，rust会自动判断类型
2. 使用const或static，需要显式指定类型，右值必须是编译期常量或者常量表达式
3. if let简化match语法
    ```rust
    let result: Result<String, &str> = Ok(String::from("ok"));
    // 只关心正确执行后的结果
    if let Ok(v) = result {println!("{}", v)}
    // 或者使用下面的4行
    match result {
      Err(why) => panic!("error: {:?}", why),
      Ok(v) => println!("ok: {}", ok)
    }
    let result: Result<String, &str> = Ok(String::from("ok"));
    ```

4. 变量可变性mut

    - 使用`let`定义变量，默认不可变，任何变量要可变，必须显式使用`mut`关键字。

5. 变量遮蔽

    - rust支持重复定义变量，后面声明的变量会导致前面的变量无效，支持修改变量类型。
    - 需要考虑到前面的变量不再使用，否则可能导致不被期待的结果。

6. 常量

    - 使用`const`定义常量，常量的值在编译时必须确定，不能在运行时改变。
    - 所有字母都必须大写。
    - 必须指定类型，如`const MAX_HEIGHT: i32 = 100;`

7. 引用和借用
    - 引用：变量的地址。
    - 借用&：对变量的引用，不改变变量的所有权。
    - 借用默认是不可变的引用，需要可变的话，必须显式使用`mut`关键字，即`&mut 变量名`。
    - 只能同时存在一个可变引用，或者多个不可变引用。

8. 切片`&s[0..5]`
    - 切片：对数组、字符串等数据结构进行切片操作，返回一个新的数据结构。
    - 切片操作不会改变原始数据结构，返回的是一个新的数据结构。
    - 切片操作可以使用索引或范围来指定切片的范围。

## 基础语法
```rust
const PI: f64 = 3.141592653589; // 编译期确定的值
static NAME: &str = "xyiling";   //  全局唯一值
// String::from()与"str".to_string()是在运行期确定的
// const PLATRORM: String = String::from("Macbook Pro"); // error, String::from()是在运行时确定值
// const PLATFORM: String = "MacbookPro".to_string();    // to_string()同理
let platform: String = "MacbookPro".to_string();
println!("{}", platform);
// 常量表达式
const fn square(v: u32) -> u32 {
  v * v
}
const RESULT: u32 = square(2);
println!("RESULT = {}", RESULT);
```

### 文件读写
```rust
// 文件操作往往都伴随错误产生，因此rust对文件的操作结果都使用Result<T>表示

// jupyter 会将上一个cell的内容传递到下一个cell，因此会在内部维护一些变量或相关信息
// 一些变量就会变成unused，但是rust默认对未使用的变量进行严格管控，存在则直接报错
// 禁用unused检查
#![allow(unused_variables)] 

use std::fs::File;
use std::io::prelude::*;
use std::path::Path;
// 打开文件
let path = Path::new("genshin.txt");
let display = path.display(); // 

// 以只读模式打开文件
let mut file:File = match File::open(&path) {
  Err(why) => panic!("文件打开失败：{}, {:?}", display, why),
  Ok(file) => file,
};
let mut s: String = String::new();
match file.read_to_string(&mut s) {
  Err(why) => panic!("无法读取文件{}: {:?}", display, why),
  Ok(_) => print!("{} contains: \n{}", display, s),
}
```

### 创建文件
```rust
use std::fs::File;
use std::io::prelude::*;
use std::path::Path;

static 钟离命座: &'static str = "岩者 六合引之为骨\n石者 八荒韫玉而明\n珪璋 暝仍不移其晖\n黄琮 破而不夺其坚\n苍璧 驱之长昭天理\n金玉 礼予天地四方";
let path = Path::new("out/a.txt");
let display = path.display();
let mut file: File = match File::create(&path) {
  Err(why) => panic!("cannot create file {}, {:?}", display, why),
  Ok(file) => file
};

match file.write_all(钟离命座.as_bytes()) {
  Err(why) => {
    panic!("写入{}失败，{:?}", display, why)
  },
  Ok(_) => println!("写入{}成功", display)
}
```

### 读取行
```rust
use std::fs::File;
use std::io::{self, BufRead};
use std::path::Path;


fn read_lines<P>(filename: P) -> io::Result<io::Lines<io::BufReader<File>>> 
where P: AsRef<Path>, {
  let file = File::open(filename)?;
  Ok(io::BufReader::new(file).lines())
}

if let Ok(lines) = read_lines("out/a.txt") {
  // 使用迭代器，返回一个可选的字符串
  for line in lines {
    if let Ok(info) = line {
      println!("{}", info)
    }
  }
}
```


### 封装Result
```rust
// 封装Result
// 使用#[derive(Debug)]，支持{:?}输出内容，否则需要实现Display trait才能被println
#[derive(Debug)]
enum MathError {
    DivisionByZero,
}

fn divide(x: f64, y: f64) -> Result<f64, MathError> {
    if y == 0.0 {
        Err(MathError::DivisionByZero)
    } else {
        Ok(x / y)
    }
}

let result: f64 = match divide(5.0, 2.0) {
  // 要求有返回值，因此需要使用panic替代println，因为panic会直接停止程序
  Err(why) => panic!("divide error: {:?}", why),
  Ok(result) => result
};

println!("result = {}", result);
// 输出：result = 2.5
```


### 子进程
process::Output 结构体表示已结束的子进程（child process）的输出
process::Command 结构体是一个进程创建者（process builder）
```rust
use std::process::Command;

fn show_version() {
  let output = Command::new("rustc")
    .arg("--version")
    .output().unwrap_or_else(|e| {
      panic!("failed to exec proc: {}", e)
    });
  
  if output.status.success() {
    let s = String::from_utf8_lossy(&output.stdout);
    print!("rustc succeeded and stdout was: \n{}", s);
  } else {
    let s = String::from_utf8_lossy(&output.stderr);
    print!("rustc failed and stderr was:\n{}", s);
  }
}
show_version()
```

### 管道
```rust
use std::io::prelude::*;
use std::process::{Command, Stdio};

static PANGRAM: &'static str = "金玉 礼予天地四方\n";

fn pipe_test() {
  // 使用wc命令计算字符数
  let process = match Command::new("wc")
    .stdin(Stdio::piped())
    .stdout(Stdio::piped())
    .spawn() {
      Err(why) => panic!("couldn't spawn wc: {:?}", why),
      Ok(process) => process,
    };
  // 将字符串写入wc进程的stdin
  match process.stdin.unwrap().write_all(PANGRAM.as_bytes()) {
    Err(why) => panic!("写入到wc的标准输入失败, {:?}", why),
    Ok(_) => println!("成功将文本输入到wc"),
  }
  let mut s = String::new();
  match process.stdout.unwrap().read_to_string(&mut s) {
    Err(why) => panic!("读取wc输出失败, {:?}", why),
    Ok(_) => print!("字符个数: {}", s),
  }
}

pipe_test()
```
