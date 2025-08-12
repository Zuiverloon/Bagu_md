https://www.youtube.com/watch?v=un6ZyFkqFKo

## general

Go is a GC language, but no jvm, and a sidecar(called Go runtime, handling gc) along with compiled go program

## Variable

### type

bool  
string  
int int8 int16 int32 int64  
uint uint8 uint16 uint32 uint64 uintptr  
byte // alias for uint8
rune // alias for int32, represents a Unicode code point(a char in a string)  
float32 float64
complex64 complex128

### declare a var

```go
var num int
var pi float64 = 3.14
```

```go
var num int
var f float64
var hasPermission bool
var s string
// all empty
fmt.Printf("%v %f %v %q\n", num, f, hasPermission, s)
// 0 0.000000 false ""


```

```go
// short var declaration
empty:=""
num:=10 // inferred as integer
temperature:=0.0 // inferred as float
g:=0.86+0.5i // complex
var isFunny=true // inferred as bool

// same line declaration
a,b := 1,"haha"
```

int, uint, float64, complex128 are preferred. Size of int,uint depends on OS(32 or 64)

### constant

```go
const PI = 3.14
//const does not support :=
PI=3.15 // no, const cannot be overwritten
```

Consts must be known at compiled time

```go
const a = 1
const b = 2
const c = a+b
// valid

var s string = "s"
const a = s
// invalid
```

### string formatting

fmt.Printf() print the result  
fmt.Sprintf() return the result  
can always use %v, but better to use type-specific variant %s(string), %d(int), %f(decimal), %.2f(precision is 2 decimals)

```go

```

### condition

```go
if height>4{

} else if height>5{

} else {

}

// if with initial statement
if height=getHeight();height<1{

}
```

## functions

```go
func sub(x int, y int) int {
    return x-y
}

// syntax sugar: if all params share the same type, then declare in the last one
func add(x,y int) int {
    return x+y
}

f func(func(int,int)int, int) int
```

### memory related

```go
x:=5
y:=x // x and y not pointing to the same memory space
increment(x)
// x is still 5 because increment receive the copy of x
```

### ignore return value

```go
x,_ = getName() // returns "a", "b"
// useful because go does not allow unused variable
```

### named returned value

```go
func f()(x,y int){
    // initialize x,y here with zero value
    return // automatically return x and y
}
// above and below are the same
func f() (int,int) {
    var x int
    var y int
    return x,y
}
```

## struct

structured data

```go
type car struct{
    Make string
    Model string
    Height int
    Width int
    FrontWheel Wheel // (nested struct)
}
type Wheel struct{
    Radius int
}
myCar := car{}
myCar.FrontWheel.Radius=5
```

```go
//anonymous struct
myCar:=struct{
    Make string
    Model string
} {
    Make:"tesla",
    Model:"model 3",
}
```

### embedded struct

look like inheritance, but actually it is syntax sugar to bring attributes to top level

```go
type car struct{
    make string
    model string
}
type truck struct{
    car // inherit all attributes of cars
    bedSize int
}
myCar:=truck{
    bedSize:10,
    car:car{
        make:"toyota",
        model:"model",
    }
}
fmt.Printf(myCar.make) //attributes are brought to top level
```

### struct methods

not having a function on struct, but a receiver(special parameter of function)

```go
type rect struct{
    width int
    height int
}
// area has a receiver of r
func (r rect) area() int{
    return r.height*r.width
}

r:=rect{
    height:3,
    width:5,
}
fmt.Printf("%v\n", r.area())
```

## interface

collections of method signature  
a type never declares that it implements a given interface. Once the proper methods defined, the type automatically fulfill the interface  
a type can implement multiple interface as long as methods are fulfilled. eg the empty interface **interface{}** is implemented by all types

```go
type shape interface {
	getPeri() int
}

type triangle struct {
	l int
}

type square struct {
	l int
}

func (s square) getPeri() int {
	return s.l * 4
}

func (t triangle) getPeri() int {
	return t.l * 3
}

func f(s shape) {
	fmt.Printf("%v\n", s.getPeri())
}

f(square{l: 4}) // 16
f(triangle{l: 4}) // 12
```

### type assertion

```go
type shape interface {
	area() float64
}
type circle struct {
	radius float64
}

type square struct {
	l float64
}

func (c circle) area() float64 {
	return 2.0 * c.radius
}

func (c square) area() float64 {
	return 4 * c.l
}

func f(s shape) {
	_, ok := s.(circle) // if s is a circle, true,else false
	fmt.Printf("%v\n", ok)
	fmt.Printf("%v\n", s.area())
}

s := circle{
	radius: 1.0,
}
q := square{l: 1.0}

f(s) // true
f(q) // false
```

### type switch

can do similar logic as type assertion

```go
func f(s shape) {
	switch v := s.(type) {
	case circle:
		fmt.Printf("%T is circle", v) // main.circle is circle
	case square:
		fmt.Printf("%T is square", v)
	default:
		fmt.Printf("is other")
	}
}

```

## Error

no need try catch block

```go
func getUser() (User, error) {

}

user,err := getUser()
if err!=nil{

}
// something
```

### error interface

An error is any type that implements the simple builtin error interface

```go
type error interface{
    Error() string
}
```

### error package

```go
import "errors"
var err error = errors.New("Something went wrong")
```

## loops

```go
// loops can omit sections of a for loop
for i:=0;i<10;i++{

}

for i:=0;;i+=2{

}
```

**No while loop in Go**

```go
i:=10
for i<10{

}
```

## Slices

slice is a dynamically view of a array.  
In go, 99% we do not work with array  
slice holds reference to underlying array.  
assign one slice to another, both of them refer to the same array  
when updating a value via a slice, the arr also got updated  
if passing a slice into function, we are passing by reference

### arrays

```go
var myInts [10]int
primes:=[6]int{2,3,5,7,11,13}
sli:=primes[1:4] // {3,5,7}
```

### make

```go
// func make([]int, len, cap) []T
mySlice:=make([]int, 5,10)// length is 5, but only when length exceed 10, then reallocation a new underlying array
```

### variadic

```go
func sum(nums ...int)int{
    // nums is a slice
    ans:=0
    for i:=0;i<len(nums);i++{
        ans+=nums[i]
    }
    return ans
}
sum(1,2,3)
```

### spread operator

```go
func sum(nums ...int)int{
    // nums is a slice
    ans:=0
    for i:=0;i<len(nums);i++{
        ans+=nums[i]
    }
    return ans
}
arr:=[]int{1,2,3}
sum(arr...)
```

### append

```go
slice:=[]int{2,3,4}
slice=append(slice,4)

```

should always assign slice back after appending

### slice of slice

```go
slice := [][]int{}
slice = append(slice, []int{1, 2})
slice = append(slice, []int{1, 2, 3})
fmt.Println(slice[0])  // [1,2]
fmt.Println(slice[1])  // [1,2,3]
```

### range

```go
slice := []int{5, 6, 7}
for index, element := range slice {
    fmt.Println(index, element)
}
// 0 5
// 1 6
// 2 7
```

## Map

```go
mp := make(map[string]int)
mp["haha"] = 2
fmt.Println(len(mp)) // 1
fmt.Println(mp["haha"]) // 2

// delete an element
delete(mp,key)

// check if a key exist
elem,ok := m[key] // ok=true, exist;..false, not exist

//iterate
for key, value := range myMap {
    fmt.Printf("Key: %s, Value: %d\n", key, value)
}

```

if the parameter is a map, then it is passed by reference

### key types

map keys may be of any type that is comparable  
What is not comparable? slices, map, functions
What is comparable? int, string, struct
If the key is missing, when getting, you get a zero value
If the map is nil and we get from a map, then still zero value

```go
mp:=map[string]map[string]int
mp["a"]["b"]
```

## Advanced functions

### first-class function

a normal function, can be treated as other value(assign to variable, pass as argument, return from function)

### higher-order function

a function that takes a function as an arg, or return a function as its result, or both

```golang
func process(nums []int, callback func(int)) {
    for _, num := range nums {
        callback(num)
    }
}
```

### currying

write a function that takes a function as input, and returns a new function. Just like enhance a function or AOP(injecting some logic) in java.

```golang
func curry(f func(int, int) int) func(int) func(int) int {
    return func(a int) func(int) int {
        return func(b int) int {
            return f(a, b)
        }
    }
}

func add(a, b int) int {
    return a + b
}

func main() {
    curriedAdd := curry(add)
    addTwo := curriedAdd(2)
    fmt.Println(addTwo(3)) // Output: 5
}
```

### defer

a unique feature of go. it allows a function to execute automatically when the current ends/exits. Just like finally in java.  
Always used to close db connection, files

### closure

a closure is a function that reference variables from outside its own function body even if the scope has exited

```golang
func newCounter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

func main() {
    counter := newCounter()
    fmt.Println(counter()) // 1
    fmt.Println(counter()) // 2
}
```

### anonymous function

a function defined without a name. It's also called a function literal or a lambda function

```golang
greet := func(name string) {
    fmt.Println("Hello,", name)
}

greet("Gopher") // Output: Hello, Gopher
```

## pointer

```go
st:="hello"
stp:=&st
```

### nil pointer

if a pointer points to nothing, it will cause a runtime error.  
should always check if a pointer is nil

## Package

package other than main is library.

## Concurrency

goroutine

```go
go doSomething()
```

### channel

used to handle the result of a goroutine  
Just a goroutine safe queue

```go
ch := make(chan int)
ch <- 69
v:= <-ch
// sending to or receiving from channel is blocking operations
```

### buffered channel

```go
ch := make(chan int, 100)
```

### closing channel

```go
ch := make(chan int)
close(ch)

v, ok := <- ch
// ok is false if the channel is closed
// send to a closed channel will panic
```

### select

similar to switch, but for channel

```go
select {
    case i, ok := <- chInts:
        fmt.Println(i)
    case s, ok := <- chStrings:
        fmt.Println(s)
    default:
        // do something
        // if you want nonblocking mode, add a default
}
// the first channel with a value ready will be fired
// always work with a for loop
```

### read only channel

the channel can only be read if casting it from **_chan_** to **_<-chan_**

```go
func main() {
    ch := make(chan int)
    readCh(ch)
}

func readCh(ch <-chan int){
    // read only
}
```

### write only channel

chan<-

### Review

A send to a nil channel blocks forever
A receive from a nil channel blocks forever
A send to a closd channel panic
A receive from a closed channel return zero value immediately

## Mutex

mutex allows to lock access to data

```go
mx := sync.Mutex{}
mx.Lock()
defer mx.UnLock() // usually defer the unlock
```

### RW Mutex

In addition to lock and unlock, RWMutex has RLock and RUnlock to allow multiple readers at the same time

## Generics(release in Go1.20)

Type parameter, allow us to use variables to refer to specific types

```go
func splitAnySlice[T any](s []T)([]T, []T){
    mid := len(s)/2
    return s[:mid],s[mid:]
}
```

### Constraint

interface that allow us to write generics that only operate within the constraints of a given interface type

```go
type stringer interface {
    String() string
}

func concat[T stringer](vals []T) string{
// do something
}

```

### parametric constraint

use type parameters in interface

```go
type store[P product] interface {
    Sell(P)
}



```

## GMP model

internal scheduler model. short for Goroutine, Machine, and Processor.
https://www.topgoer.com/%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B/GMP%E5%8E%9F%E7%90%86%E4%B8%8E%E8%B0%83%E5%BA%A6.html#%E4%BA%8C%E3%80%81goroutine-%E8%B0%83%E5%BA%A6%E5%99%A8%E7%9A%84-gmp-%E6%A8%A1%E5%9E%8B%E7%9A%84%E8%AE%BE%E8%AE%A1%E6%80%9D%E6%83%B3

## GC

https://zhuanlan.zhihu.com/p/334999060

GC 负责回收堆上的内存空间

### Go1.3(Pre) 标记清除法(mark-and-sweep)

1. Stop the world 暂停程序业务逻辑
2. 找到所有可达对象，做上标记
3. 清除未标记对象
4. resume

缺点：程序卡顿，需扫描整个 heap，产生 heap 碎片

### Go1.3(Post)

先 resume，再清除

### Go1.5 三色并发标记（concurrent tri-color mark-and-sweep algorithm）

0. 需要 STW
1. 新创建的对象都是白色
2. 每次 GC 开始，从根节点开始遍历所有对象（非递归），遍历到的放入灰色
3. 遍历灰色，将灰色对象引用的对象从白色放入灰色，把本身灰色变黑色
4. 重复第三步直到没有灰色
5. 回收白色

缺点：也需要 STW，不然的话万一白色被黑色引用，白色就丢了， 或灰色连着白色时白色被破坏，子节点也都会被删除
解决第一个问题使用插入屏障（仅对堆生效，对栈不生效），当黑色节点添加一个白色节点时，把白色节点变灰
完成三色扫描后，栈上仍有可能存在黑色引用白色，此时使用 STW+三色标记
解决第二个问题使用删除屏障，如果被删除的对象为灰色或白色，则标记为灰色（保护灰色到白色的路径不断）这种方式回收精度低，一个对象被删除了最后一个指向他的指针还是活着，下一轮 GC 被清理掉

### Go1.8 混合写屏障（hybrid write barrier）

1、GC 开始将栈上的对象全部扫描并标记为黑色(之后不再进行第二次重复扫描，无需 STW)  
2、GC 期间，任何在栈上创建的新对象，均为黑色
3、被删除的对象标记为灰色
4、被添加的对象标记为灰色

### 强三色不等式

黑色不允许引用白色

### 弱三色不等式

黑色可以引用白色，但是白色上游必须有灰色

## new vs make

new 返回指针，变量的值是零值，创建的是引用类型以外的其他类型变量  
make 返回值，创建 slice、map 和 chan 等引用变量

## 内存管理 stack vs heap

go 编译器根据逃逸分析(escape analysis)决定分配在 stack 还是 heap  
stack: 只在函数中用到  
heap: 逃逸的变量(被返回的指针，长期的 object)

| Feature    | Stack                     | Heap                                       |
| ---------- | ------------------------- | ------------------------------------------ |
| Lifetime   | Short (function scope)    | Long (survives after function ends)        |
| Speed      | Fast (no GC needed)       | Slower (managed by Garbage Collector)      |
| Allocation | Automatic                 | Managed by Go runtime                      |
| Use Case   | Temporary/local variables | Returned pointers, closures, large objects |

## 多线程打印 1-100

用 channel

```golang
	var wg sync.WaitGroup
	limit:=100
	wg.Add(limit)

	ch := make(chan int,1)
	for i:=0;i<limit;i++{
		go func(number int) {
			defer wg.Done()
			for {
				t:= <- ch
				if t<number{
					ch <- t
				} else {
					fmt.Println(number)
					ch <- t+1
					return
				}
			}

		}(i)
	}

	ch <- 0
	wg.Wait()
```

用 sync.Mutex.Lock()

```golang
var wg sync.WaitGroup
	limit:=100
	wg.Add(limit)

	counter := 0

	var lock sync.Mutex
	for i:=0;i<limit;i++{
		go func(number int) {
            defer lock.Unlock()
			defer wg.Done()
			for {
				lock.Lock()
				if counter == number {
					fmt.Println(counter)
					counter++
					return
				}
				lock.Unlock()

			}
		}(i)
	}

	wg.Wait()
```
