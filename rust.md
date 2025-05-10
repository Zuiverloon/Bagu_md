## Rust

### Variable and Mutability

Only mutable variables can be assigned new values.

```rust
let x = 5;
println!("This is x:{}",x);
x = 6; //error! cannot assign twice to immutable variable x

let mut y;
y = 7;
println!("This is y:{}",y);

const SECOND:i8 = 60;
// constants can never be mutable
println!("This is SECOND:{}",SECOND);
```

### scalar data type

Scalar types represents a single values: integers, floating points, boolean, characters.

```rust
let x: i8 = 10;
//  let x: i8 = 255; // error, overflowed
println!("this is x : {}",x);

let _y: u8 = 255;
// let y: u8 = -1; // error

// integer literals
let decimal = 02_55;
println!("{}", decimal); // 255
let hex = 0xff;
println!("{}", hex); // 255
let oct = 0o377;
println!("{}", oct); // 255
let binary = 0b1111_1111;
println!("{}", binary); // 255

let byte = b'A';
println!("{}", byte); // 65

let _f1 = 2.0; // f64 by default
let _f2: f32 = 2.0; // f32

let _t = true;
let _f: bool = false;

let c = 'c';
println!("{}", c); // c
```

### Compound type - Tuple

tuple: fixed size, elements can have different types

```rust
let mut tup = (1,"haha",true);
tup.0 = 2;
let (x,y,z) = tup;
println!("{} {} {}",x,y,z); // 2 haha true
```

### Compound type - array

array: fixed size, elements have the same type

```rust
let mut arr = [1,2,3,4];
let arr_1:[i32; 3] = [5,6,7];
arr[0] = 10;
println!("{}",arr[0]); // 10
println!("{}",arr[9]); // error! out of bound
```

if to modify values in tuple and array, mark it as mut.

### Functions

```rust
fn main(){
	print_phase("Hello"); // hello
    println!("{}",gcd(18,12)); // 6
    println!("{}",multi_return_values(true)); // true
}
fn print_phase(phrase: &str){
    println!("{}", phrase);
}

fn gcd(mut a: u64, mut b:u64) -> u64{
    while a!=0 {
        if a<b {
            let c = a;
            a = b;
            b = c;
        }
        a = a%b;
    }
    b
}

fn multi_return_values(flag: bool) -> bool{
    if flag{
        true
    } else {
        false
    }
}
```

### Control flow

```rust
// if
let one = -1;
if one == 1{
    println!("true");
}
else if one > 0{
    println!("maybe");
}
else{
    println!("false");
}

// loop
loop {
    println!("Loop");//infinite loop
}

// return value from loop
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}

// let mut num = 0;
'counter : loop{ // loop label must start with '
    println!("num={}",num);
    let mut decrease = 5;
    loop{
        println!("Decreasing:{}",decrease);
        if decrease == 4{
            break;
        }
        if num == 2{
            break 'counter;
        }
        decrease-=1;
    }
    num+=1;
}

//while
let mut cnt = 0;
while cnt<10{
    println!("cnt = {}",cnt);
    cnt+=1;
}

// for loop
let vec: Vec<i8> = (0..10).collect();
for num in vec{
    println!("{}",num); // 0 1 2 3...
}
for num in (0..4).rev(){
    println!("{}",num); // 3 2 1 0
}
```

## ownership

Ownership is a rule that govern how a rust program manages memory
Each value in Rust has an owner.  
There can only be one owner at a time.  
When the owner goes out of scope, the value will be dropped.
When a variable goes out of scope, rust call `drop` for us

```rust
let s1 = String::from("hello");
let s2 = s1; // after that, s1 no longer valid
// this is called MOVE

println!("{s1}, world!");
```

### Move

Move the ownership of the value from one variable to another for **most type**

```rust
let x = vec!["hello"];
let y = x;
println!("{:?}", x);
//err borrow of moved value x
```

### Clone(deep copy)

Clone can take the value of a variable without taking the ownership. A very expensive operation.

```rust
let x = vec!["hello"];
let y = x.clone();
let z = y.clone();
println!("{:?}", x);
println!("{:?}", y);
println!("{:?}", z);
/*
["hello"]
["hello"]
["hello"]
*/
```

### Copy

A copy is implemented on types that are stored on stack such as int, boolean, float, tuple(if every element implements copy)
a type or any of its parts implemented drop, we are not allowed to annotate it wth copy

```rust
let x = 1;
let y = x;
println!("x = {}, y = {}", x,y);
// x = 1, y = 1
```

### More move

Passing a variable to a function will move or copy, just as assignment does.

```rust
let s = String::from("str");
take_ownership(s);
println!("{}",s);
//err borrow of moved value

let one = 1;
make_copy(one);
println!("{}",one);
// OK. 1

let to:String = take_and_give(from);
println!("{}",to); // from
println!("{}",from); // err borrow from moved value

let str1 = give_ownership();
println!("{}",str1);
// give

fn take_ownership(s:String){
    let x = s;
    println!("{}",x);
}

fn make_copy(i:i32){
    let x = i;
    println!("{}",x);
}

fn give_ownership() -> String{
"give".to_string()
}

fn take_and_give(s:String) -> String{
    let x = s;
    println!("{}",x);
    return x;
}
```

## reference / borrowing

in order not to pass and return value from/in a function, then we need reference.
创建 reference 就是 borrowing
函数中如果是引用变量，不能修改该变量。如果想修改，就要用 mut reference
如果对某个变量已经有了一个 mut reference，就不能有第二个引用，除非别的 reference 不再被使用了（mut 引用和 immut 引用的 scope 不能重叠）
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

other slices

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

```

## struct

```rust
let user1 = User{active:true,username:String::from("haha"), sign_in_count:0};
println!("{}",user1.username); // haha
let user2 = build_user(String::from("user2"));
println!("{}",user2.username); // user2

struct User{
    active: bool,
    username: String,
    sign_in_count: u32,
}

fn build_user(username:String) -> User{
    User{
        username,
        active:true,
        sign_in_count:1,
    }
}

let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
// user1 will be invalid, since email is moved to user2. if updating all string field, then user1 still valid

```

tuple struct without named fields

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);
```

unit-like struct without any field

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

if we need to store references in structs, need to specify lifetime.

### Method

Similar to function, but defined in struct, enum or trait
Always have the first param as self

```rust
let mut sq = Square{width:5,height:5};

println!("{}",sq.area());//25
sq.change_width(10);
println!("{}",sq.area());//50
struct Square{
    width:u32,
    height:u32,
}

impl Square{
    fn area(&self) -> u32{
        self.width * self.height
    }

    fn change_width(&mut self, new_width:u32){ // make self mutable if you want to modify the value
        self.width = new_width;
    }
}
```

associated functions:All functions defined within an impl block are called associated functions because they’re associated with the type named after the impl. We can define associated functions that don’t have self as their first parameter (and thus are not methods) because they don’t need an instance of the type to work with.Associated functions that aren’t methods are often used for constructors

```rust
impl Rectangle {
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}
let sq = Rectangle::square(3);
```

### Enum

```rust
enum Pet{
    dog,cat,fish
}

impl Pet{
    fn what_am_i(self) -> &'static str{
        match self {
            Pet::dog => "I am a dog",
            Pet::cat => "I am a cat",
            Pet::fish => "I am a fish",
        }
    }
}
let pet = Pet::dog;
println!("{}", pet.what_am_i());
// I am a dog

//enum can be used in struct, enum can have types
enum IpAddrKind{
    V4(String),
    V6
}

struct IpAddr{
    kind: IpAddrKind,
    address: String,
}
let addr = IpAddr{kind:IpAddrKind::V4(String::from("ipv4")), address:String::from("127.0.0.1")};
println!("{}",addr.address); // 127.0.0.1
```

### Option and Match

```rust
let some_number = Some(5);
let some_string = Some("a string");
let nothing:Option<i32> = None;

let x = 5;
let y:Option<i32> = Some(5);
let sum = x+y;//err cannot add i32 and option i32, need to convert

fn plus_one(x:Option<i32>) -> Option<i32>{
match x{
None => None, // if this line is missing, compiler will notify us. Match in rust is exhaustive.
Some(i) => Some(i+1),
}
}

println!("{:?}", plus_one(Some(5))); // Some(6)
println!("{:?}", plus_one(None)); // None

fn what_pet(input: &str) {
match input{
"dog" => println!("I am a dog"),
_ => println!("anything else"), // like default statement
}
}
```

### if let

if let can be used to replace match. So does while let.

```rust
let dog = Pet::dog;
if let Some(Pet::dog) == dog{
// ...
} else {
// ...
}
let mut stk = Vec::new();
stk.push(1);
stk.push(2);
stk.push(3);
while let Some(top) = stk.pop{
println!("{}",top); // 3 2 1
}
```

### More matches

```rust
let x = 1;
match x{
1|2 => println!("1 or 2"),
_ => println!("else"),
}

match x{
 1..=5 => println!("1 to 5"), // from 1 to 5 inclusive
 _ => println!("anything else"),
}

let x = Some(5);
let y = 5;
match x{
Some(10)=> println!("10"),
Some(i) if i == y => println!("is y"),
_ => println!("else"),
} // is y
```
