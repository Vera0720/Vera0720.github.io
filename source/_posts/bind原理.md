---
title: bind原理
date: 2019-04-03 16:23:55
tags: js
---
bind() 函数会创建一个新函数（称为绑定函数），新函数与被调函数（绑定函数的目标函数）具有相同的函数体（在 ECMAScript 5 规范中内置的call属性）。当目标函数被调用时 this 值绑定到 bind() 的第一个参数，该参数不能被重写。绑定函数被调用时，bind() 也接受预设的参数提供给原函数。一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

<!-- more -->

### 思路

    var value = 2;
    
    var foo = {
        value: 1
    };
    
    function bar(name, age) {
        return {
    		value: this.value,
    		name: name,
    		age: age
        }
    };
    
    bar.call(foo, "Jack", 20); // 直接执行了函数
    // {value: 1, name: "Jack", age: 20}
    
    var bindFoo1 = bar.bind(foo, "Jack", 20); // 返回一个函数
    bindFoo1();
    // {value: 1, name: "Jack", age: 20}
    
    var bindFoo2 = bar.bind(foo, "Jack"); // 返回一个函数
    bindFoo2(20);
    // {value: 1, name: "Jack", age: 20}

    
通过上述代码可以看出bind有如下特性：
1、可以指定this
2、返回一个函数
3、可以传入参数
4、柯里化

### 第一步
- 针对第一点作用域绑定，这里可以使用apply或者call方法来实现，考虑到参数的个数，还是用apply方便些
- 针对第二点因为bind方法不会立即执行函数，需要返回一个待执行的函数（这里用到闭包，可以返回一个函数）return function(){}


    // 第一版
    Function.prototype.bind2 = function(context) {
        var self = this; // this 指向调用者
        return function () { // 实现第 2点
            return self.apply(context); // 实现第 1 点
        }
    }
    
### 第二步
- 对于第3点，使用arguments获取参数数组并作为self.apply()的第二个参数。
- 对于第4点，获取返回函数的参数，然后同第3点的参数合并成一个参数数组，并作为self.apply()的第二个参数。


    // 第二版
    Function.prototype.bind2 = function (context) {
    
        var self = this;
        // 实现第3点，因为第1个参数是指定的this,所以只截取第1个之后的参数
    	// arr.slice(begin); 即 [begin, end]
        var args = Array.prototype.slice.call(arguments, 1); 
    
        return function () {
            // 实现第4点，这时的arguments是指bind返回的函数传入的参数
            // 即 return function 的参数
            var bindArgs = Array.prototype.slice.call(arguments);
            return self.apply( context, args.concat(bindArgs) );
        }
    }

### 模拟实现第三步
有一个难点，bind有以下一个特性
- 一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器，提供的this值被忽略，同时调用时的参数被提供给模拟函数。

来个例子：

    var value = 2;
    var foo = {
        value: 1
    };
    function bar(name, age) {
        this.habit = 'shopping';
        console.log(this.value);
        console.log(name);
        console.log(age);
    }
    bar.prototype.friend = 'kevin';
    
    var bindFoo = bar.bind(foo, 'Jack');
    var obj = new bindFoo(20);
    // undefined
    // Jack
    // 20
    
    obj.habit;
    // shopping
    
    obj.friend;
    // kevin

上面例子中，运行结果this.value 输出为undefined，这不是全局value也不是foo对象中的value，这说明bind的this对象失效了，new的实现中生成一个新的对象，这个时候的this指向的是obj。

这里可以通过修改返回函数的原型来实现，代码如下：

    // 第三版
    Function.prototype.bind2 = function (context) {
        var self = this;
        var args = Array.prototype.slice.call(arguments, 1);
    
        var fBound = function () {
            var bindArgs = Array.prototype.slice.call(arguments);
            
            // 当作为构造函数时，this 指向实例，此时 this instanceof fBound 结果为 true，可以让实例获得来自绑定函数的值，即上例中实例会具有 habit 属性。
            // 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context,即为你想绑定的那个对象上
           return self.apply(
                this instanceof fBound ? this : context, 
                args.concat(bindArgs)
            );
        }
        // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值，即上例中 obj 可以获取到 bar 原型上的 friend。
        fBound.prototype = this.prototype;
        return fBound;
    }


### 第四步
上面实现中 fBound.prototype = this.prototype有一个缺点，直接修改 fBound.prototype 的时候，也会直接修改 this.prototype。

上面的例子中

    obj.__proto__.friend = "Kitty"; // 修改原型
    
    bar.prototype.friend; // Kitty 返回错误，这里被修改了 ，应该还是返回kevin

解决方案是用一个空对象作为中介，把 fBound.prototype 赋值为空对象的实例（原型式继承）

    var fNOP = function () {};			// 创建一个空对象
    fNOP.prototype = this.prototype; 	// 空对象的原型指向绑定函数的原型
    fBound.prototype = new fNOP();		// 空对象的实例赋值给 fBound.prototype

也可以使用Object.create生成一个新对象（Object.create方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。）

    fBound.prototype = Object.create(this.prototype);

最后加上如果调用 bind 的不是函数，这时候需要抛出异常

    Function.prototype.bind2 = function (context) {
        if (typeof this !== "function") {
          throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
        }
        var self = this;
        var args = Array.prototype.slice.call(arguments, 1);
        
        var fBound = function () {
            var bindArgs = Array.prototype.slice.call(arguments);
            return self.apply(
                this instanceof fNOP ? this : context, 
                args.concat(bindArgs)
            );
        }
        fBound.prototype = Object.create(this.prototype);
        return fBound;
    }






























