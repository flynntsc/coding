# Wrap


```
function speak(name) {
    return "Hello " + name;
}

Function.prototype.wrap = function(func) {
    return func.bind(this, this);
}

speak = speak.wrap(function(original, yourName, myName) {
    greeting = original(yourName);
    return greeting + ", my name is " + myName;
})

var greeting = speak("Mary", "Kate"); // Hello Mary,my name si Kate
```

# Pipe


```
var addOne = e => {
    return e + 1;
};

var square = e => {
    return e * e;
};

// 理解下双return
Function.prototype.pipe = function(fn) {
    return function(arg) {
        return fn.call(null, this(arg));
        // 等同return fn(this(arg));
    }.bind(this);
}

var result = [1, 2, 3, 4, 5].map(addOne.pipe(square)) //-> [4,9,16,25,36]
```

# 过程抽象

> [函数式编程离我们有多远？](https://www.h5jun.com/post/functional-how-far.html)

```
// 善用到了reduce的特性，同理forEach,map等
function reduce(fn) {
    return function(...args) {
        return args.reduce(fn.bind(this));
        // == return args.reduce(fn);
    }
}

function add(x, y) {
    return x + y;
}

function mul(x, y) {
    return x * y;
}

function concat(arr1, arr2) {
    return arr1.concat(arr2);
}

add = reduce(add);
mul = reduce(mul);
concat = reduce(concat);

console.log(add(1, 2, 3), //6
    mul(1, 2, 3, 4), //24
    concat([1, 2], [3, 4], [5, 6])); //[1,2,3,4,5,6]
```

### 支持异步

```
function reduce(fn, async) {
    if (async) {
        return function(...args) {
            return args.reduce((a, b) => {
                return Promise.resolve(a).then((v) => fn.call(this, v, b));
                // 
            });
        }
    } else {
        return function(...args) {
            return args.reduce(fn.bind(this));
        }
    }
}

function add(x, y) {
    return x + y;
}

function mul(x, y) {
    return x * y;
}

function concat(arr1, arr2) {
    return arr1.concat(arr2);
}

function asyncAdd(x, y) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(x + y), 100);
    });
}

add = reduce(add);
mul = reduce(mul);
concat = reduce(concat);

console.log(add(1, 2, 3), //6
    mul(1, 2, 3, 4), //24
    concat([1, 2], [3, 4], [5, 6])); //[1,2,3,4,5,6]

asyncAdd = reduce(asyncAdd, true);
asyncAdd(1, 2, 3).then((v) => console.log(v));
```

### 函数调用的频度控制

在实际项目中，我们有时候会遇到限制某函数调用频率的需求。例如，防止一个按钮短时间的的重复点击，防止 resize、scroll 和 mousemove 事件过于频繁地触发等。

#### throttle

throttle 可以限制函数调用的频率，常用来防止按钮被重复点击。

```
function throttle(fn, wait){
    var timer;
    return function(...args){
        if(!timer){
            timer = setTimeout(()=>timer=null, wait);
            return fn.apply(this, args);
        }
    }
}

//按钮每500ms一次点击有效
btn.onclick = throttle(function(){
    console.log("button clicked");
}, 500);
```

#### debounce

有时候我们希望函数在某些操作执行完成之后被触发。例如，实现搜索框的 Suggest 效果，如果数据是从服务器端读取的，为了限制从服务器读取数据的频率，我们可以等待用户输入结束 100ms 之后再触发 Suggest 查询：

```
function debounce(fn, delay){
    var timer = null;
    return function(...args){
        clearTimeout(timer); // 清除原先查询语句
        timer = setTimeout(() => fn.apply(this, args), delay);
    }
}

window.addEventListener("scroll", debounce(function(){
    console.log("scrolled");
}, 500));
```

#### DOM的批量操作

jQuery 的语法糖特点包括批量操作与链式调用。我们可以看一下，实际上用函数式的过程抽象思想很容易实现类似的“语法糖”。我们先考虑如何支持函数的“批量操作”。

```
function multicast(fn){
  return function(list, ...args){
      if(Array.isArray(list)){
      return list.map((item)=>fn.apply(this, [item,...args]));
    }else{
      return fn.apply(this, [list,...args]);
    }
  }
}

function add(x, y){
    return x + y;
}

add = multicast(add);

console.log(add([1,2,3], 4)); //5,6,7
```

