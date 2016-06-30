> [程序的迭代](https://www.h5jun.com/post/%20iteration.html)

递归形式的迭代有调用堆栈增长的弊端，当问题规模较大时很容易导致堆栈溢出。大数值计算时，往往迭代好于递归。

**阶乘运算**：(n! = n * (n - 1) * (n - 2) ... * 3 * 2 * 1)

```
// 循环
let factorial = (n) => {
    let ret = 1;
    for(let i = 2; i <= n; i++) {
        ret *= i;
    }
    return ret;
}
console.log(factorial(5)); //120

// 递归
let factorial = (n) =>
    n > 1 ? n * factorial(n - 1) : 1;
console.log(factorial(5)); //120

// 迭代
function factorial(n, acc = 1) {
    if(n <= 1) return acc;
    return factorial(n - 1, n * acc);
}
console.log(factorial(5)); //120
```

<br>

**斐波那契数列**

```
// 循环
let fib = (n) => {
  if(n === 0) return 0;
  let a = 1, b = 0;
  for(let i = 2; i <= n; i++){
    [a, b] = [a + b, a];
  }
  return a;
};
console.log(fib(7)); //13

// 递归（计算次数为指数性增长）
let fib = (n) => {
  if(n === 0) return 0;
  if(n === 1) return 1;
  return fib(n - 1) + fib(n - 2); // 中间数被重复计算
};
console.log(fib(7)); //13

// 迭代
let fib = (n, a = 1, b = 0) => {
  if(n === 0) return b;
  return fib(n - 1, a + b, a);
};
console.log(fib(7)); //13
```

<br>

**迭代方式深度copy一个对象**

```
let deepCopy = (obj) => {
  if(obj == null) return obj;
  let copyed = {};

  for(let key in obj){
    if(typeof obj[key] === "object"){
      copyed[key] = deepCopy(obj[key]);
    }else{
      copyed[key] = obj[key];
    }
  }

  return copyed;
};

let a = {a:{x:1, y:2}, b:3, c:function(){}};
let b = deepCopy(a);
console.log(b.a.x); //1

b.a.y = 3;
console.log(b.a.y, a.a.y); //3, 2
```

<br>

**迭代实现快速排序**

```
function qsort(arr){
  if(arr.length <= 1) return arr;
  var c = arr[0], rest = arr.slice(1);
  return qsort(rest.filter(item => item < c))
    .concat([c]).concat(qsort(rest.filter(item => item >= c)));
}
```


