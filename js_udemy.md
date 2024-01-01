### at function

```js
let arr = [3, 2, 1];
console.log(arr.at(0)); // 3
console.log(arr[-1]); // undefined
console.log(arr.at(-1)); // 1

// at also works on string
console.log("jonas".at(0)); // j
console.log("jonas".at(-1)); // s
```

### loop array: foreach

```js
const arr = [54, 23, 43];

arr.forEach(function (element, index, array) {
  console.log(index + " " + element + " " + array);
});
// 0 54 54,23,43
// 1 23 54,23,43
// 2 43 54,23,43
```

### foreach with maps and sets

```js
// map
const currencies = new Map([
  ["USD", "US Dollar"],
  ["EUR", "Euro"],
  ["GBP", "Pound Sterling"],
]);

currencies.forEach(function (value, key, map) {
  console.log(key + " " + value + " " + map);
});
// USD US Dollar [object Map]
// EUR Euro [object Map]
// GBP Pound Sterling [object Map]

// set
const currencies = new Set(["USD", "CNY"]);
currencies.forEach(function (value, key, st) {
  console.log(index + " " + value + " " + st); // same key as value. mostly write as function(value, _, set){...}
});
// USD USD [object Set]
// CNY CNY [object Set]
```

### data transformations: map, filter, reduce

map: modify every element and return a new arr

````js
// map
const eurToUsd = 1.1;
const arr = [1,2,3];
console.log(arr.map(function(val){
  return val*eurToUsd;
})); // [ 1.1, 2.2, 3.3000000000000003 ]

filter: filter elements satisfying conditions
```js
const arr = [1,2,3];
console.log(arr.filter(function(val){
    return val>1;
})); //  2 3
````

reduce: boil down all elements into one value

```js
const arr = [1, 2, 3];
console.log(
  arr.reduce(function (acc, val) {
    // the first arg of callback is accumulator
    return acc + val;
  }, 2)
); // need to set accumulator in the second arg of reduce
// 8
```

### Chaining method

```js
const arr = [1, 2, 3];
console.log(arr.filter((val) => val > 1).map((val) => val * 2)); // [4,6]
```

### find

return the first element that satisfies the condition

```js
const arr = [-2, 0, 3, -1, 9];
console.log(arr.find((val) => val > 0)); // 3
```

### findIndex

return the index of the first element that satisfies the condition

```js
const arr = [-2, 0, 3, -1, 9];
console.log(arr.findIndex((val) => val > 0)); // 2
```

### some and every

some method is similar to includes, but it allows more flexible conditions.

```js
const arr = [-2, 0, 3, 9];
console.log(arr.includes(3)); // true
console.log(arr.some((val) => val < 10 && val > 0)); // true
console.log(arr.every((val) => val < 10 && val > 0)); // false
```

### flat and flatmap

flat method will flat the array if there is any nested array in it. can specify the depth of flat

```js
const arr = [[1, 2, 3], [4, 5, 6], 7, 8];
console.log(arr.flat()); // [1,2,3,4,5,6,7,8]

const arrDeep = [[[1, 2], 3], [[4, 5], 6], 7, 8];
console.log(arrDeep.flat()); // [[1,2],3,[4,5],6,7,8]
console.log(arrDeep.flat(2)); // [1,2,3,4,5,6,7,8]
```

flatmap method conbines flat and map.(calling map and then flat)

```js
const arr = [[1, 2, 3], [4, 5, 6], 7, 8];
console.log(arr.flatMap((val) => (Array.isArray(val) ? val : val * 2))); // [1,2,3,4,5,6,14,16]
```

### sorting arrays

sort() function treats all elements as string and mutate the original array. Need a call back function to sort numbers.

```js
const strarr = ["John", "Liz", "Gaeul"];
console.log(strarr.sort()); // [ 'Gaeul', 'John', 'Liz' ]

const intarr = [-1, -9, 22];
console.log(intarr.sort()); // [ -1, -9, 22 ] sort by alphabetical order
console.log(intarr.sort((a, b) => a - b)); // [ -9, -1, 22 ] sort by number asc
```

### creating and filling arrays

```js
const arr = new Array(7);
console.log(arr); // [ <7 empty items> ]
console.log(arr.fill(1)); // [1,1,1,1,1,1,1]

const arr = new Array(7);
console.log(arr); // [ <7 empty items> ]
console.log(arr.fill(1, 3)); // [ <3 empty items>, 1, 1, 1, 1 ]

console.log(Array.from({ length: 7 }, () => 2)); // [2,2,2,2,2,2,2]
console.log(Array.from({ length: 7 }, (_, i) => i + 2)); // [2,3,4,5,6,7,8]
```

## Numbers, Dates, Intl, Timers

all numbers in js are floating point decimals

```js
//Coercion
console.log(Number("23"));
console.log(+"23"); // js will do type coercion when seeing + operator

//Parsing
console.log(Number.parseInt("23")); // 23
console.log(Number.parseFloat("5.4")); // 5.4

//Check if it is NaN
console.log(Number.isNaN(20)); // f
console.log(Number.isNaN("20")); // f
console.log(Number.isNaN(+"20X")); // t
console.log(Number.isNaN(20 / 0)); // f it is infinity

// this is the better way to check number
console.log(Number.isFinite(20)); // t
console.log(Number.isFinite("20")); // f
console.log(Number.isFinite(+"20X")); // f
console.log(Number.isFinite(20 / 0)); // f it is infinity

// check whether a value is an int
console.log(Number.isInteger(23)); // t
console.log(Number.isInteger(23.0)); // t
console.log(Number.isInteger(23.5)); //  f
console.log(Number.isInteger(23 / 0)); // f
```

### Math and rounding

```js
console.log(Math.sqrt(25)); // 5
console.log(25 ** (1 / 2)); // 5

console.log(Math.max(1, 2, "23", 5)); // 23
console.log(Math.trunc(Math.random() * 6) + 1); // randomly get a number from 1 to 6
console.log(Math.round(23.3)); // 23
console.log(Math.round(23.9)); // 24

console.log(Math.ceil(23.3)); // 24
console.log(Math.ceil(23.9)); // 24

console.log(Math.floor(23.3)); // 23
console.log(Math.floor("23.9")); // 23 will do auto parsing

console.log((2.7).toFixed(3));
console.log((2.3735).toFixed(2)); // toFixed method will return a str
```

### BigInt

```js
console.log(875708979687809076788908076n);

const huge = 78768890809787897978907890n;
const num = 12;
console.log(huge * num); // error. cannot mix bignint and other types
console.log(typeof 20n); // bigint
console.log(11n / 3n); // 3
```

### Timer

```js
const timer = setTimeout(() => console.log("Time out"), 1000);
console.log("Waiting");
// Waiting
// Time out

clearTimeout(timer); // can stop the timer

const clock = setInterval(() => console.log(new Date()), 1000);
// will print once per second
```

## OOP in js

### Constructor Functions

```js
const Person = function (firstName, birthYear) {
  this.firstName = firstName;
  this.birthYear = birthYear;
};

const jonas = new Person("Jonas", 1991);

// 1. New {} is created
// 2. function is called, this = {}
// 3. {} link to prototype
// 4. function automatically return {}

console.log(jonas instanceof Person); // true
```

### Prototype

Every function has a prototype. All function created by calling a construction function will inherit the property and methods defined on the prototype property

```js
const jonas = new Person("jonas");
Person.prototype.calcAge = function(){
...
}
jonas.calcAge();//ok
console.log(jonas.__proto__ == Person.prototype);
//true every object has a __proto__ property
console.log(Person.prototype.isPrototypeOf(jonas));//true
```

The prototype of Person.prototype is Object.prototype
Object.prototype.prototype is null
We can add any function to Array.

```js
Array.prototype.unique = function () {
  //...
};
```

## ES6 class

it is only a syntax sugar.  
classes are not hoisted(cannot be used before declaration)
classes are first-class citizens(can be passed into functions and returned)
classes are executed in strict mode

### getter and setter

```js
const account = {
  movement: [1, 2, 3, 4],
  get latest() {
    return this.movement[this.movement.length - 1];
  },

  set latest(mov) {
    this.movement.push(mov);
  },
};

console.log(account.latest); // 4
account.latest = 5;
console.log(account.movement); // [1, 2, 3, 4, 5]
```

### static method

```js
class Person {
  static hey() {
    console.log("hey");
  }
}

Person.hey(); // hey
```

### Object.create

```js
const personProto = {
  calcAge() {
    console.log(2037 - this.birthYear);
  },

  init(firstName, birthYear) {
    this.firstName = firstName;
    this.birthYear = birthYear;
  },
};

const sarah = Object.create(personProto);
sarah.init("Sarah", 1979);
sarah.calcAge(); // 58
```

### inheritance between classes

#### using constructor

```js
const Person = function (firstName, birthYear) {
  this.firstName = firstName;
  this.birthYear = birthYear;
};
Person.prototype.calcAge = function () {
  console.log(2037 - this.birthYear);
};

const Student = function (firstName, birthYear, course) {
  Person.call(this, firstName, birthYear);
  this.course = course;
};

//Linking prototypes
Student.prototype = Object.create(Person.prototype);

Student.prototype.introduce = function () {
  console.log(`My Name is ${this.firstName}`);
};

Student.prototype.constructor = Student;

const mike = new Student("mike", 2020, "CS");
mike.introduce(); // My Name is mike
mike.calcAge(); // 17
```

### using es6 class

```js
class PersonCL {
  constructor(name, birthYear) {
    this.name = name;
    this.birthYear = birthYear;
  }

  calcAge() {
    console.log(2037 - this.birthYear);
  }
}

class StudentCL extends PersonCL {
  constructor(name, birthYear, course) {
    super(name, birthYear);
    this.course = course;
  }

  calcAge() {
    console.log(`I am ${2037 - this.birthYear}`);
  }
}

const mike = new StudentCL("mike", 2020, "CS");
mike.calcAge(); // I am 17
```

### using Object.create()

```js
const personProto = {
  calcAge() {
    console.log(2037 - this.birthYear);
  },

  init(firstName, birthYear) {
    this.firstName = firstName;
    this.birthYear = birthYear;
  },
};

const studentProto = Object.create(personProto);
studentProto.init = function (firstName, birthYear, course) {
  personProto.init.call(this, firstName, birthYear);
  this.course = course;
};
const jay = Object.create(studentProto);
jay.init("jay", 2020);
studentProto.calcAge = function () {
  console.log(`I am ${2037 - this.birthYear}`);
};
jay.calcAge(); // I am 17
```

### private fields (only in stage 3, may become a real js feature in the future)

```js
class Account {
  #movement = [];

  #approve(val) {
    console.log(`${val} approved`);
  }
}

const acc1 = new Account();
console.log(acc1.#movement); // Private field '#movement' must be declared in an enclosing class
```

## Asynchronous

### Promise and fetchAPI

Promise: a container for an asynchronously delivered value
(Pending, settled, fulfilled / rejected)

```js
const request = fetch('');
console.log(request); // a promise

//consume a promise
const getCountryData = function(country){
fetch(`https://restcountries.com/rest/v2/name/${country}`)
    .then(response => response.json())
    .then(data => console.log(data[0]));
};
// Chaining promise
const getCountryData = function(country){
fetch(`https://restcountries.com/rest/v2/name/${country}`)
    .then(response => response.json())
    .then(data => {
    return fetch('...');
    }).then(response => response.json())
    .then(data => console.log(data));
    )
};

// handling rejected promise(lose network connection)
// way1
const getCountryData = function(country){
fetch(`https://restcountries.com/rest/v2/name/${country}`)
    .then(response => response.json(), err => alert(err))
    .then(data => console.log(data[0]));
};
// way2
const getCountryData = function(country){
fetch(`https://restcountries.com/rest/v2/name/${country}`)
    .then(response => response.json())
    .then(data => console.log(data[0]))
    .catch(err => alert(err));
};
// throw error
const getCountryData = function(country){
fetch(`https://restcountries.com/rest/v2/name/${country}`)
    .then(response => {
    if (!response.ok){
    throw new Error("Something wrong");
    }
    return response.json();
    })
    .then(data => console.log(data[0]))
    .catch(err => alert(err))
    .finally(...);
};
```

Callback queue is used to handle all asynchronous tasks.
Event loop does orchestration for js runtime, picking event from callback queue and put into call stack.
Microtasks queue is for promises, having priority over callback queue. It can starve callback queue because all microtasks will be executed before callback queue.

```js
//Build a promise
const lotteryPromise = new Promise(function (resolve, reject) {
  if (Math.random() >= 0.5) {
    resolve("You win");
  } else {
    reject("You lose");
  }
});
lotteryPromise.then((res) => console.log(res)).catch((err) => console.log(err));

const wait = function (second) {
  return new Promise(function (resolve) {
    setTimeout(resolve, second * 1000);
  });
};
```

### async/await

```js
const whereAmI = async function(country){
    await fetch(...);
    //fetch().then(); // the same as await
}
```

### try catch

```js
try {
  let x = 1;
  const y = 2;
  y = 3;
} catch (err) {
  alert(err.message);
}
```

### execute promise in parallel

```js
const data = await Promise.all([
fetch(...).then(),
fetch(...).then(),
fetch(...).then()
]);
// Other promise conbinators
Promise.race([...]);//get the first returned result, either resolved or rejected will return. Usually used to set the timeout of an API request
Promise.allSettled([]);//return all, not short circuit any rejected while Promise.all will short circuit.
Promise.allSettled([
    Promise.resolve("1"),
    Promise.reject("2"),
    Promise.resolve("3"),
    ]).then(res => console.log(res));

/*
[
  { status: 'fulfilled', value: '1' },
  { status: 'rejected', reason: '2' },
  { status: 'fulfilled', value: '3' }
]

*/

Promise.all([
Promise.resolve("1"),
Promise.reject("2"),
Promise.resolve("3"),
]).then(res => console.log(res[0])).catch(err=>console.log(err));
/*
2
*/

Promise.any();// return the first fulfilled promise, slightly different from race
Promise.race([
    Promise.reject(1),
    Promise.resolve(2)
    ]).then(res=>console.log(res)).catch(err=>console.log(err));
// 1


Promise.any([
    Promise.reject(1),
    Promise.resolve(2)
    ]).then(res=>console.log(res)).catch(err=>console.log(err));
// 2
```

## Module

if not exported, all variables in a module are not accessible to other modules.

```js
//default import
export default function () {}

import add from "/script.js";
//named import
const add = function () {};

export { add };

import { add } from "/script.js";
// the imported variable is the reference to the original variable. Any modification will affect.
```

Top level await(ES2022): await can be used in top level in module

```js
const res = await fetch("...");
```

Bundling with Parcel
Parcel will bundle all script into one file, useful when facing old browser
Babel transpile es6 to es5 to support old systems, parcel already integrate babel.

### Modern and clean code

use descriptive var names: what they contain
use descriptive function name: what they do
use strong type check === / !==
functions no more than 3 params
return same type of data as received
