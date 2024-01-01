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

### Compound type - Vector

```rust
let mut nums = vec![1,2,3];
nums.push(4);
println!("{:?}",nums); // 1,2,3,4
nums.pop();
println!("{:?}",nums); // 1,2,3

let mut vec = Vec::new(); // same to vec!
vec.push("ahha");
vec.push("string");
println!("{:?}",vec); // ahha string

vec.reverse();
println!("{:?}",vec); // reverse the vec

let mut vect = Vec::<i32>::with_capacity(2);
println!("{}",vect.capacity()); // 2
vect.push(1);vect.push(2);vect.push(3);

println!("{}",vect.capacity()); // 4 vec will automatically increase

let v: Vec<i32> = (0..5).collect(); // create vec with iterator
println!("{:?}",v); // 0,1,2,3,4
```

### Slices

```rust
let v: Vec<i32> = (0..5).collect(); // create vec with iterator
println!("{:?}",v); // 0,1,2,3,4

let sv : &[i32] = &v[2..4];
println!("{:?}",sv); // 2,3
```

### String and &str

```rust
let name = String::from("zjy");
let course = "Rust".to_string();
// both are string
println!("{},{}",name,course); // zjy,Rust
let new_name = name.replace("zjy","haha");
println!("{}",new_name); // haha

//&str is string slice
let str1 = "hello"; // this is a &str. &str does not allocate any space on heap while a string does
let str2 = str1.to_string();
let str3 = &str2;

println!("{}","ONE".to_lowercase() == "one"); // true. == can be used to compare strings
```

### String literal

contain non utf8 chars

```rust
let rust = "\x52\x75\x73\x74";
println!("{}",rust); // rust
```

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

### Ownership

Rust compiler verifies that the program is free of memory safety err, such as dangling pointer, double free, using uninitialized memory.
Each value has a variable called owner. There can be one owner at a time. When the owner goes out of scope, the value will be dropped(freed)

```rust
fn main(){
let var = 1; // on the stack
let mut s = "hello".to_string(); // created on the heap
}
// when main is finished, var/s are dropped
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

```rust
let x = 1;
let y = x;
println!("x = {}, y = {}", x,y);
// x = 1, y = 1
```

### More move

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

### Reference and borrowing

Reference: make a reference without taking ownership aka **borrowing the value**
Two types of references: shared vs mutable  
A shared reference allows read but no modification, can have as many shared reference to a particular value as you desire  
A mutable reference allows both read and modification.
Can only have one mutable reference at a time

```rust
let mut s = String::from("hello ");
change_str(&mut s);
println!("{}",s); // hello world. Change the string without taking the ownership

fn change_str(s:&mut String){
    s.push_str("world");
}
```

## Struct and lifetime

Name field struct:

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
```

Tuple like struct

```rust
let cord = Coordinate(1,2,3);
println!("{} {} {}", cord.0,cord.1,cord.2); // 1 2 3

struct Coordinate(i32,i32,i32);
```

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

### Lifetime

Every reference has lifetime
Mostly, lifetime is implicit and inferred.
The purpose of lifetime is to prevent dangling reference.

```rust
let r;
{
    let x = 5;
    r = &x;
}// x dropped
println!("{}", r); // err:x does not live long enough
```

lifetime annotation helps us to think through the code and help rust compiler to find the dangling reference ahead of time. The annotation will not change the lifetime.

```rust
fn example<'a>(x:&'a str) -> &'a str{
x
}
```

Compiler uses 3 rules to infer lifetime when there are not explicit annotations

1. each param that is a reference gets its own lifetime param
2. if there are exactly one lifetime param, that lifetime is assigned to all output lifetime param(the return value)
3. if there are multiple input lifetime param, but one of them is a reference to self or a immutable self, the lifetime of self is assigned to all output life params.
   lifetime annotation is required when the borrow checker does not know what to infer.

### Lifetime in struct

Struct can hold reference, but we need to add lifetime

```rust
struct MyString{
text: &'a str,
}
let x = String::from("This is ...");
let y = MyString{text: x.as_str()};
// we need to make sure that x lives longer than y, otherwise we will have dangling reference.
```

### static lifetime

When giving the static lifetime, the reference can exist for the entire duration of program. All string literals have a static lifetime

```rust
let s: &'static str = "I have a static lifetime";
//when giving the string literal a static lifetime, the text of string is stored directly in the program's binary
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
