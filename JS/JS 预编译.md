# 0. 预编译的理解

JavaScript 是解释性语言, 即编译一行, 执行一行. 但在编译前存在预编译过程.

预编译分为两种, 全局的预编译(代码执行前)和函数的预编译(函数执行前).

# 1. 函数预编译

1.生成 Activation Object 对象

2.寻找形参和变量声明, 将其作为 AO 属性名, 值为 undefined

3.形参值为实参

4.寻找函数声明, 函数名作为 AO 属性名, 值为函数体

例如

```js
 <script>
  function game(a) {
    console.log(a);
    var a = 123;
    console.log(a);
    function a(){};
    console.log(a);
    var b = function(){};
    console.log(b);
  }
  game(0);
 </script>
```

结果为

```js
ƒ a(){}
123
123
ƒ (){}
```

过程如下

1.生成 AO, 寻找形参和变量声明, 值为 undefined

```js
AO :{
    a: undefined,
    b: undefined
}
```

2.形参值为实参

```js
AO :{
    a: 0,
    b: undefined
}
```

3.函数声明, 值为函数体

```js
AO :{
    a: function a() {},
    b: undefined,
}
```

# 2. 全局预编译

1.生成 Globa Object

2.找变量声明, 如果 window 存在当前属性, 会忽略, 否则变量会作为属性名, 值为 undefined

3.找函数声明, 同 2, 如果 window 没有, 属性为函数名, 值为函数体

例如

```js
 <script>
  console.log(a);
  var a = 1;
  console.log(a);
  function game(a){
    console.log(a);
    var a = 123;
    console.log(a);
    function a() {};
    console.log(a);
    console.log(b);
    var b = function(){};
    console.log(b);
  }
  game(0);
 </script>
```

结果

```js
undefined
0
ƒ a() {}
123
123
undefined
ƒ (){}
```

# 3. 常见考题

## 例题一

```js
    <script>
      function test() {
        console.log(b);
        if (a) {
          var b = 100;
        }
        console.log(b);
        c = 234;
        console.log(c);
      }
      var a;
      test();
      a = 10;
      console.log(c);
    </script>
```

解答

```js
undefined
undefined
234
234
```

## 例题二

```js
var foo = 1;
function test() {
    console.log(foo);  
    if (!foo) {
        var foo = 10;
    }
    console.log(foo); 
}
test();
```

```js
undefined
10
```

## 例题三

```js
var a = 1;
function b() {
    console.log(a);  
    a = 10;
    return;
    function a() { }
}
b();
console.log(a); 
```

```js
function a() { }
1
```

## 例题四

```js
console.log(foo); 
var foo = "A";
console.log(foo)  
var foo = function () {
    console.log("B");
}
console.log(foo); 
foo(); 
function foo(){
    console.log("C");
}
console.log(foo)  
foo(); 
```

```js
ƒ foo(){
    console.log("C");
}
A
ƒ () {
    console.log("B");
}
B
ƒ () {
    console.log("B");
}
B
```



## 例题五

```js
var foo = 1;

function bar(a) {
    var a1 = a;
    var a = foo;
    function a() {
        console.log(a); 
    }
    a1();
}

bar(3);
```

```
1
```

