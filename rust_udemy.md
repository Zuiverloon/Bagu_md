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

### Generic

Generics: abstract standards for concrete types

```rust
struct Point<T> {
x:T,
y:T,
}

let coor = Point{x:1,y:1};
let coor1 = Point{x:'a', y:'b'};
```

### Traits

Use trait to define shared behavior in an abstract way

```rust
trait Overview {
    fn overview(&self) -> String{
        String::from("this is a course")
    } // default implementation
}

struct Course{
    headline:String,
    author:String,
}
struct AnotherCourse{
    headline:String,
    author:String,
}
impl Overview for Course{
    fn overview(&self) -> String{
        format!("{},{}",self.headline,self.author)
    }
}
impl Overview for AnotherCourse{
    fn overview(&self) -> String{
        format!("{},{}",self.headline,self.author)
    }
}
let course = Course{headline:String::from("course"),author:String::from("ZJY")};
let course1 = AnotherCourse{headline:String::from("course"),author:String::from("ZJY")};
println!("{}",course.overview()); // course,ZJY
println!("{}",course1.overview()); // course,ZJY

// if do not implement, then use the default
impl Overview for Course{}
println!("{}",course.overview()); // this is a course
```

### Trait as parameter

```rust
fn call_overview(item: &impl Overview){
println!("{}",item.overview());
}
call_overview(&course);
```

### Drop trait

a preset trait in Rust, used for freeing resources.

```rust
impl Drop for Course{
    fn drop(&mut self){
        println!("Dropping {}", self.author)
    }
}
drop(course1);
```

even if we do not explicitly call this drop method, it will be executed when the instance is dropped/out of scope

### Operator overloading

```rust
use std::ops::Add;

#[derive(Debug)]
struct Point<T>{
x:T,
y:T,
}
impl<T> Add for Point<T>
where
T:Add<Output = T>{
type Output = Self;
fn add(self, rhs: Self) -> Self{
Point{
x:self.x + rhs.x,
y:self.y + rhs.y,
}
}
}

let p1 = Point{x:1,y:1};
let p2 = Point{x:2,y:3};
let sum = p1+p2;
println!("{:?}",sum); // Point { x: 3, y: 4 }
```

## Cargo, crates and package

crates are about code sharing between projects, modules are about code sharing within a project

## Collections

### Vector

```rust
let mut nums: Vec<i32> = vec![];
nums.push(1);
nums.push(2);
nums.push(3);

let first = nums.pop(); // pop the last element
println!("{:?}", first);

let num1 = nums[1]; // copy
// &nums[1] if copy is not implemented

println!("{}", nums.len());

nums.insert(1,10); // insert 10 to pos 1
```

### Binary heap

```rust
use std::collections::BinaryHeap;


let mut bheap = BinaryHeap::new();
bheap.push(1);
bheap.push(20);
bheap.push(10);

println!("{:?}", bheap.pop());
println!("{:?}", bheap.peek());
```

### Map

```rust
use std::collections::HashMap;

let mut hm = HashMap::new();
hm.insert(1,1);
hm.insert(5,2);
hm.insert(20,3);
println!("{:?}", hm);

println!("{}", hm.contains_key(&5)); // true
println!("{:?}", hm.get(&5));// Some(2)

let one = hm.remove(&1); // 1
let ent = hm.remove_entry(&5); // (5,2)

hm.clear();
```

### Set

```rust
let mut st = HashSet::new();
st.insert(1);
st.insert(1);
st.insert(2);
st.insert(3);

for x in st.iter(){
    println!("{}",x);
} // 3 1 2

st.remove(&2);

let mut st2 = HashSet::new();
st2.insert(1);
st2.insert(3);
st2.insert(5);
st2.insert(7);

for x in st.intersection(&st2){
    println!("{}",x);
}

let inter = &st & &st2; // shorthand intersection
for x in inter.iter(){
    println!("{}",x);
} // 1 3

let uni = &st | &st2;
for x in uni.iter(){
    println!("{}",x);
} // 1 2 3 5 7

let diff = &st - &st2; //  elements that in st but not in st2
```
