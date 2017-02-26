在函数内部有两个特殊的对象，arguments与this。虽然arguments的主要用途是保存参数，但它还有一个**callee属性。**指向拥有这个arguments对象的函数。

下面举一个栗子

```js
这是一个阶乘函数
function factorial(num){
    if(num<=1){
        return 1;
    }
    else {
        return num*factorial(num-1)
    }
}
使用arguments.callee
function factorial(num){
    if(num<=1){
        return 1;
    }
    else {
        return num*arguments.callee(num-1)

    }
}

```

arguments.callee**可以解除函数的耦合**



函数对象还有一个属性：caller，简而言之，指向调用当前函数的函数。

举个栗子

```js
function outer(){
    inner()
}
function inner(){
    alert(inner.caller)
}
outer()      //显示outer（）的代码
```

**arguments.callee.caller可以实现更松散的耦合**

```
function outer(){
    inner()
}
function inner(){
    alert(arguments.callee.calle)
}
outer()      //显示outer（）的代码
```



