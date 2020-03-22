---
title: js基础系列之类型
date: 2018-04-03 16:01:08
tags: js
---
js中的基本数据类型有：string,number,boolean,null,undefined,symbol
js中的内置类型有:string,number,boolean,null,undefined,symbol
<!-- more -->
### sting
string.split('')将字符串分割为数组
### number
使用的是双精度格式，64位二进制
42.toFixed(3)报syntaxError,应为42.被看作一部分，42..toFixed()或(42).toFixed(3)
NAN!=NAN
### null
判断null的条件
    
    !a&& typeof a === 'object'
### undefined
undefined是已在作用域中声明但没有被赋值，undeclared是没有被声明
### 数组
数组的方法有
pop()删除末尾的元素
push()向末尾增加一个元素，返回数组的长度
shift()删除开头第一个元素
unshift()向开头增加一个元素，返回数组的长度
splice(index,howmany,additem1,.....,additemX)返回删除的元素,操作的元素包括开始的那个元素,添加是在开始的元素前面添加的
slice(begin, end) 浅拷贝返回一个从开始到结束（不包括结束）选择的数组的一部分浅拷贝到一个新数组对象，且原数组不会被修改。
join()变成字符串
concat()合并数组，返回一个新数组
sort()排序
reverse()反转数组
indexof(查找元素，开始查找的位置)返回数组中查到这个元素第一个的索引
map()
foreach()
filter()
find()
能够改变原数组的：push,pop,shift,unshift,sort,reverse,splice
### Object
- 对象就是键/值的集合，可以通过["propName"]来获取属性值,访问属性时，引擎实际上会调用内部的默认[[GET]]操作，检查这个对象本身是不是包含这个属性，如果还没找到会查找原型链。
- 对象内部的[[class]]属性一般会和创建该对象的内建原生函数相对应，虽然null,undefined这种并不存在null(),undefined()这种原生构造函数，但[[class]]属性值依旧会返回null,undefined
- 创建一个空对象的方法：object.create(null)不会创建object.prototype这个委托
- 读取属性的特性，Object.getOwnPropertyDescriptor()
- 定义多个属性，Object.defineProperties()
#### 创建对象的模式
1.工厂模式
    
    function Person() {
      var o = new Object();
      o.name = 'hanmeimei';
      o.say = function() {
        alert(this.name);
      }
      return o;
    }
    var person1 = Person();
    
无法通过constructor识别对象，以为都是来自Object，无法得知来自Person

2.构造函数模式
    
    function Person() {
      this.name = 'hanmeimei';
      this.say = function() {
        alert(this.name)
      }
    }
    var person1 = new Person();

通过constructor或者instanceof可以识别对象实例的类别
可以通过new 关键字来创建对象实例，更像OO语言中创建对象实例
没有显示的创建对象，直接将属性和方法赋给了this，没有return语句，隐试的在最后返回return this，所以在缺少new的情况下，会将属性和方法添加给全局对象，浏览器端就会添加给window对象。
使用new操作符发生了什么
创建一个新对象 -> 将构造函数的作用域给新对象（因此this指向了这个新对象）-> 执行构造函数中的代码（为这个对象添加新属性）-> 返回新对象

3.原型模式
    
    function Person() {}
    Person.prototype.name = 'hanmeimei';
    Person.prototype.say = function() {
      alert(this.name);
    }
    Person.prototype.friends = ['lilei'];
    var person1 = new Person();

方法共享，所有的实例的方法都指向同一个
所有的方法都是共享的，没有办法创建实例自己的属性和方法，也没有办法像构造函数那样传递参数。
可以动态的添加原型对象的方法和属性，并直接反映在对象实例上。
第一次调用say方法或者name属性的时候会搜索两次，第一次是在实例上寻找say方法，没有找到就去原型对象(Person.prototype)上找say方法，找到后就会在实力上添加这些方法or属性。
不同实例对象引用的相同值改变，其他的都跟着改变

4.组合使用构造函数模式和原型模式

    function Person(name) {
      this.name = name
      this.friends = ['lilei']
    }
    Person.prototype.say = function() {
      console.log(this.name)
    }
    var person1 = new Person('hanmeimei')
    person1.say() //hanmeimei
    
构造函数模式用于定义实例属性，原型模式用于定义方法和共享的属性，每个实例都会有自己的一份实例属性的副本，但是又同时共享着对方法的引用，最大限度节省了内存
解决了原型模式对于引用对象的缺点；解决了原型模式没有办法传递参数的缺点；解决了构造函数模式不能共享方法的缺点

5.动态原型模式

    function Person(name) {
      this.name = name
      if(typeof this.say != 'function') {
        Person.prototype.say = function(){
            alert(this.name)
        }   
      }
    }
把所有信息都封装到了构造函数中，仅在必要条件的情况下通过构造函数中初始化原型。换句话说，通过判断某个应该存在的方法是否有效，来确定是否初始化原型

6.寄生构造函数模式

    function Person(name) {
      var o = new Object()
      o.name = name
      o.say = function() {
        alert(this.name)
      }
      return o
    }
    var peron1 = new Person('hanmeimei')

除了使用new操作符并把使用的包装函数叫做构造函数外，其他的和工厂模式一样
返回的对象与构造函数、构造函数的原型属性之间没有关系

7.稳妥构造函数

    function Person(name) {
      var o = new Object()
      o.say = function() {
        alert(name)
      }
    }
    var person1 = new Person('hanmeimei');
    person1.name  // undefined
    person1.say() //hanmeimei

稳妥对象就是没有公共属性，而且其方法不引用this对象

#### 继承

- 原型继承
- 借用构造函数继承
- 组合继承
- 寄生式继承
- 寄生组合式继承

### type of

type of 能够判断基本类型，但除了null,type of null == 'object'，对于对象来说，type of无法判断他的类型，除了type of function == 'function'
typeof返回的是一个字符串
typeof function(){} === 'function',但function是object的一个子类型
值有类型而变量没有类型，typeof并不是在询问变量的类型，而是变量的值的类型


### instanceof

instanceof 是通过原型链判断的，A instanceof B, 在A的原型链中层层查找，是否有原型等于B.prototype，如果一直找到A的原型链的顶端(null;即Object.proptotype.__proto__),仍然不等于B.prototype，那么返回false，否则返回true.


    // L instanceof R
    function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
        var O = R.prototype;// 取 R 的显式原型
        L = L.__proto__; // 取 L 的隐式原型
        while (true) { 
            if (L === null) //已经找到顶层
            r   eturn false; 
            if (O === L) //当 O 严格等于 L 时，返回 true
                return true; 
            L = L.__proto__; //继续向上一层原型链查找
        } 
    }


