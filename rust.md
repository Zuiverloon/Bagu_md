## ownership

Each value in Rust has an owner.  
There can only be one owner at a time.  
When the owner goes out of scope, the value will be dropped.

## reference / borrowing

创建 reference 就是 borrowing
函数中如果是引用变量，不能修改该变量。如果想修改，就要用 mut reference
如果对某个变量已经有了一个 mut reference，就不能有第二个引用。
如果是在不同 scope 中，则可以有多个 mut reference，在同一个 scope 中不能同时有两个

```rust
let mut s = String::from("hello");

    {
        let r1 = &mut s;
    } // r1 goes out of scope here, so we can make a new reference with no problems.

    let r2 = &mut s;
```

如果已经有了一个 immutable reference，之后也不能有 mut reference
一个引用的 scope 是从引入开始，到最后一次使用结束

```rust
let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    println!("{} and {}", r1, r2);
    // variables r1 and r2 will not be used after this point

    let r3 = &mut s; // no problem
    println!("{}", r3);
```

## slice

string slice &str
literal string 的类型就是&str，是不可变引用

```rust
//获取一段string slice
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```
