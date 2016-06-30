## 变量提升
 
>  [JavaScript的变量：变量提升](http://www.w3cplus.com/javascript/javascript-hoisting-in-details.html)
 
 **变量提升只是提升变量的声明，并不会把赋值也提升上来**
 
```
name = "W3cplus";
var name;
console.log(name);

// 等同

var name; // 代码编译阶段
name = "W3cplus"; // 代码运行阶段
console.log(name); // 代码运行阶段 => W3cplus

```

```
console.log(name);
var name = "W3cplus";

// 等同

var name; // 代码编译阶段
console.log(name); // 代码运行阶段 => undefined
name = "W3cplus": // 代码运行阶段
```

```
var foo = 1;

function bar() {
    if(!foo) {
        var foo = 10;
    }
    console.log(foo);
}
bar();

// 等同

var foo = 1;

function bar () {
    var foo; // 定义局部变量foo
    if (!foo) { // foo是undefined (false),那么!foo就是true，所以会执行if语句块
        foo = 10;
    }
    console.log(foo);
}

bar(); // => 输出的结果是10
```

如果变量在函数体内声明，它的作用域是函数作用域。否则，它就是全局作用域。变量将会在执行进入作用域时被创建。块不会定义新的作用域，只有函数声明和程序才可以。变量在创建的时候会被初始化为undefined。如果变量声明语句带有赋值操作，则赋值操作只有在被执行的时候才会发生，而不是创建的时候。
