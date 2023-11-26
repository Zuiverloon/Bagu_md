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
