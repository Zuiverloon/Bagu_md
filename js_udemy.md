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
