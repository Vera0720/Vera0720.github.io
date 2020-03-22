---
title: es6系列之语法简介
date: 2019-12-11 00:24:08
tags: js
---

今年其实用了很多es6的语法，但没有完整系统的学习整理过，今天整理下，也算是给2019年一个交代
话不多说，我们进入正题

<!-- more -->

### 块作用域

1.let

    console.log(a);
    let a;

这个时候会报referenceError错误，因为过早访问let声明的饮引用导致的这个referenceError严格说叫做临时死亡区错误。在let声明前会存在一个临时存在的死亡区域，对变量的任何操作都会报语法错误

2.const
用于创建常量，必须要有显示的初始化。其次，这个值不会因为const而被锁定或者不可变，只是赋值本身不可变，意思是什么呢，看下面这个代码

    const obj = {}
    obj.a = 1
    console.log(obj.a)
如果我们想让obj也变得不可变更。那么就必须使用 Object.freeze( obj ); 来创建一个不可变对象值
    
    const obj = Object.freeze({});
    obj.a = 123; // TypeError
    
Object.freeze(obj) 就如同const一样，陷阱也一模一样。Object.freeze(obj) 只能保证obj本身是不可变的，也就是不能添加/删除属性，也不能给属性重新赋值。但是，对于属性值的内容，却是可以变更的。

    const obj = Object.freeze({ foo: {} });
    obj.a = 123 // TypeError
    obj.foo = {}  // TypeError
    obj.foo.qux = 'abc'; // 可以输出'abc'

3.块作用域函数
先调用，后声明，与let相反

### spread/rest

展开收集运算符...，扩展运算符底层就是for of

扩展运算符(spread)
场景：使用在数组之前。
作用：将一个数组转为用逗号分隔的参数序列
    
    function foo(x,y,z) {
      console.log(x,y,z);
    }
    
    let arr = [1,2,3];
    foo(...arr); // 1 2 3　
    
收集运算符(rest)
场景：在函数参数之前使用。
作用：收集剩余的参数转为一个数组。

    function foo(...args) {
      console.log(args);
    }
    foo( 1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
    
总结：如何判断ES6中的运算符是扩展运算符(spread)还是收集运算符(rest)，主要取决于其作用的位置。
1.数组之前，作为扩展运算符使用，将数组转为逗号分隔的参数序列。
2.函数形参中，收集传入的参数为数组。
3.解构赋值中，收集对应的数据为数组。

### 默认参数值

    function myFunction(x = 1, y = 2, z = 3) {
        console.log(x, y, z); 
    }
    myFunction(6,7); // Outputs 6 7 3

### 解构

专用于数组解构和对象解构

1.对象属性赋值

var {x:a,y:b,z:c} = bar();重要的是abc而不是xyz，对象解构语法是在赋值对象的左侧使用了对象字面量，看下例子

- 同名变量解构赋值


    let node = {
            type : 'identifier',
            name : 'foo'
    };
    let {type,name} = node;
    console.log(type);//'identifier'
    console.log(name);//'foo'


- 不同变量解构赋值


    let node = {
                type : 'identifier',
                name : 'foo'
    };
    let {type:typelocal,name:namelocal} = node
    console.log(typelocal);//'identifier'
    console.log(namelocal);//'foo'
    
- 嵌套的对象解构

    
     let node = {
         type: "Identifier",
         name: "foo",
         loc: {
           start: {
                 line: 1,
                 column: 1
            },
           end: {
                 line: 1,
                 column: 4
            }
        }
    };
    let { loc: { start }} = node;
    console.log(loc)
    console.log(start.line); // 1
    console.log(start.column); // 1

注意： 

     let { loc: {} } = node; // 没有变量被声明！
     
对象解构形式允许多次列出同一个源属性，例如var {a:X,a:Y} = {a:1} 输出X=1，Y=1
     
2.数组解构

数组解构的语法看起来与对象解构非常相似，只是将对象字面量替换成了数组字面量。数组解构时，解构作用在数组内部的位置上，而不是作用在对象的具名属性上

    let colors = [ "red", "green", "blue" ];
    let [ firstColor, secondColor ] = colors;
    console.log(firstColor); // "red"
    console.log(secondColor); // "green"

也可以用来互换变量

    let a = 3;
    let b = 4;
    [b,a] = [a,b];

数组解构赋值同样允许在数组任意位置指定默认值。当指定位置的项不存在、或其值为undefined ，那么该默认值就会被使用

    let colors = [ "red" ];
    let [ firstColor, secondColor = "green" ] = colors;
    console.log(firstColor); // "red"
    console.log(secondColor); // "green"

嵌套的数组结构与对象类似，只是仍使用的是数组字面量

    let colors = [ "red", [ "green", "lightgreen" ], "blue" ];
    console.log(secondColor); // "green"

如果省略了var let const等声明符，就必须把整个赋值表达式用（）扩起来，因为如果不这样做，语句左侧{ }作为语法中的第一个元素就会被当作是一个块语句而不是一个对象

### filter

    var newarr = [
      { num: 1, val: 'ceshi', flag: 'aa' },
      { num: 2, val: 'ceshi2', flag: 'aa2'  }
    ]
    console.log(newarr.filter(item => item.num===2 ))

### set
Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

    Set.prototype.constructor：构造函数，默认就是Set函数。
    Set.prototype.size：返回Set实例的成员总数。
    Set.prototype.add(value)：添加某个值，返回 Set 结构本身。
    Set.prototype.delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
    Set.prototype.has(value)：返回一个布尔值，表示该值是否为Set的成员。
    Set.prototype.clear()：清除所有成员，没有返回值。
    Set.prototype.keys()：返回键名的遍历器
    Set.prototype.values()：返回键值的遍历器
    Set.prototype.entries()：返回键值对的遍历器
    Set.prototype.forEach()：使用回调函数遍历每个成员


向 Set 加入值的时候，不会发生类型转换，所以5和"5"是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（===），主要的区别是向 Set 加入值时认为NaN等于自身，会进行去重，而精确相等运算符认为NaN不等于自身。

Array.from方法可以将 Set 结构转为数组。
    
    Array.from(new Set(array));

如果想在遍历操作中，同步改变原来的 Set 结构，目前没有直接的方法，但有两种变通方法。一种是利用原 Set 结构映射出一个新的结构，然后赋值给原来的 Set 结构；另一种是利用Array.from方法。

    // 方法一
    let set = new Set([1, 2, 3]);
    set = new Set([...set].map(val => val * 2));
    // set的值是2, 4, 6

    // 方法二
    let set = new Set([1, 2, 3]);
    set = new Set(Array.from(set, val => val * 2));
    // set的值是2, 4, 6

使用set进行数组去重
    
    [...new Set(array)]

### weakset
Set和WeakSet两者名字就很像，WeakSet结构同样不会存储重复的值，不同的是，它的成员必须是对象类型的值。
    
    //初始化一个WeakSet对象
    let ws = new WeakSet([{"age":18}]);
    //结果：WeakSet {Object {age: 18}}
    
    let arr1 = [1];
    let arr2 = [2];

    //初始化一个WeakSet对象,参数是数组类型
    let ws = new WeakSet([arr1,arr2]);
    //结果：WeakSet {Array(1), Array(1)} 
    展开{
        0: Array(1)
            ->value:[1]
        1: Array(1)
            ->value:[2]
    }
        
是a数组的成员成为 WeakSet 的成员，而不是数组本身。这意味着，数组的成员只能是对象。

    WeakSet.prototype.add(value) // 向 WeakSet 实例添加一个新成员。
    WeakSet.prototype.delete(value) // 清除 WeakSet 实例的指定成员。
    WeakSet.prototype.has(value) // 返回一个布尔值，表示某个值是否在 WeakSet 实例之中。
    
WeakSet 不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet的一个用处，是储存DOM节点，而不用担心这些节点从文档移除时，会引发内存泄漏。

### map

map映射，即原数组映射成一个新的数组。Map是ES6中新增的数据结构，Map类似于对象，但普通对象的key必须是字符串或者数字，而Map的key可以是任何数据类型

如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如0和-0就是一个键，布尔值true和字符串true则是两个不同的键。另外，undefined和null也是两个不同的键。虽然NaN不严格相等于自身，但Map将其视为同一个键。

属性和操作方法
    
    Map.prototype.size // size属性返回 Map 结构的成员总数。
    Map.prototype.set(key, value) // set方法设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。
    Map.prototype.get(key) // get方法读取key对应的键值，如果找不到key，返回undefined。
    Map.prototype.has(key) // has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。
    Map.prototype.delete(key) // delete方法删除某个键，返回true。如果删除失败，返回false。
    Map.prototype.clear() // clear方法清除所有成员，没有返回值。

遍历方法

    Map.prototype.keys() // 返回键名的遍历器。
    Map.prototype.values() // 返回键值的遍历器。
    Map.prototype.entries() // 返回所有成员的遍历器。
    var map = new Map()
    // set
    map.set('name', 'John')
    map.set('age', 29)
    // 遍历实体
    for (var arr of map.entries()) {
        console.log(arr) 
    }
    // (2) ["name", "John"]
    // (2) ["age", 29]
    Map.prototype.forEach() // 遍历 Map 的所有成员。
    
Map 的遍历顺序就是插入顺序。
Map 结构转为数组结构，比较快速的方法是使用扩展运算符（...）。
    
与其他数据结构的互相转换

- Map 转为数组---扩展运算符
    
    
    const myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
    [...myMap]

- 数组转为 Map---将数组传入Map构造函数，就可以转为 Map
    
    
    new Map([
        [true, 7],
        [{foo: 3}, ['abc']]
    ])
    // 输出
    Map(2) {true => 7, {…} => Array(1)}
    -> 0: {true => 7}
    -> 1: {Object => Array(1)}
    ->   key: {foo: 3}
    ->   value: ["abc"]

- Map 转为对象---如果所有 Map 的键都是字符串，它可以无损地转为对象。


    function strMapToObj(strMap) {
        let obj = Object.create(null);
        for (let [k,v] of strMap) {
            obj[k] = v;
        }
        return obj;
    }
    
    const myMap = new Map()
    .set('yes', true)
    .set('no', false);
    strMapToObj(myMap)

如果有非字符串的键名，那么这个键名会被转成字符串，再作为对象的键名。

- 对象转为Map


    function objToStrMap(obj) {
        let strMap = new Map();
        for (let k of Object.keys(obj)) {
            strMap.set(k, obj[k]);
        }
        return strMap;
    }
    
    objToStrMap({yes: true, no: false})


- Map转为JSON
Map 的键名都是字符串，这时可以选择转为对象JSON。


    function strMapToJson(strMap) {
        return JSON.stringify(strMapToObj(strMap));
    }
    
    let myMap = new Map().set('yes', true).set('no', false);
    strMapToJson(myMap)

Map的键名有非字符串，这时可以选择转为数组 JSON。
    
    
    function mapToArrayJson(map) {
        return JSON.stringify([...map]);
    }
    
    let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
    mapToArrayJson(myMap)

- JSON转为Map
    
所有键名都是字符串。

    function jsonToStrMap(jsonStr) {
        return objToStrMap(JSON.parse(jsonStr));
    }
    
    jsonToStrMap('{"yes": true, "no": false}')
    
整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为 Map。这往往是 Map 转为数组 JSON 的逆操作。
    
    function jsonToMap(jsonStr) {
        return new Map(JSON.parse(jsonStr));
    }
    
    jsonToMap('[[true,7],[{"foo":3},["abc"]]]')

### weakmap
WeakMap结构与Map结构类似，也是用于生成键值对的集合。
WeakMap与Map的区别
- WeakMap只接受对象作为键名（null除外），不接受其他类型的值作为键名。
- WeakMap的键名所指向的对象，不计入垃圾回收机制。

WeakMap的设计目的在于，有时我们想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。当数据变化或者删除掉，需手动更改变化的数据。WeakMap 就是为了解决这个问题而诞生的，它的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。基本上，如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。一个典型应用场景是，在网页的 DOM 元素上添加数据，就可以使用WeakMap结构。当该 DOM 元素被清除，其所对应的WeakMap记录就会自动被移除。
WeakMap的专用场合就是，它的键所对应的对象，可能会在将来消失。WeakMap结构有助于防止内存泄漏。
注意，WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用

### Iterator（遍历器）
遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。
Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令for...of循环，Iterator 接口主要供for...of消费。

Iterator的遍历过程是这样的。
1.创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
2.第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员。
3.第二次调用指针对象的next方法，指针就指向数据结构的第二个成员。
4.不断调用指针对象的next方法，直到它指向数据结构的结束位置。
每一次调用next方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含value和done两个属性的对象。其中，value属性是当前成员的值，done属性是一个布尔值，表示遍历是否结束。

Iterator 接口的目的，就是为所有数据结构，提供了一种统一的访问机制，即for...of循环。当使用for...of循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口。一种数据结构只要部署了 Iterator 接口，我们就称这种数据结构是“可遍历的”（iterable）。ES6 规定，默认的 Iterator 接口部署在数据结构的Symbol.iterator属性，或者说，一个数据结构只要具有Symbol.iterator属性，就可以认为是“可遍历的”（iterable）。Symbol.iterator属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名Symbol.iterator，它是一个表达式，返回Symbol对象的iterator属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内
原生具备 Iterator 接口的数据结构如下。Array，Map，Set，String，TypedArray，函数的 arguments 对象，NodeList 对象
对于原生部署 Iterator 接口的数据结构，不用自己写遍历器生成函数，for...of循环会自动遍历它们。除此之外，其他数据结构（主要是对象）的 Iterator 接口，都需要自己在Symbol.iterator属性上面部署，这样才会被for...of循环遍历。对象（Object）之所以没有默认部署 Iterator 接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。本质上，遍历器是一种线性处理，对于任何非线性的数据结构，部署遍历器接口，就等于部署一种线性转换。不过，严格地说，对象部署遍历器接口并不是很必要，因为这时对象实际上被当作 Map 结构使用，ES5 没有 Map 结构，而 ES6 原生提供了。
调用 Iterator 接口的场合：解构赋值，扩展运算符，yield*，for...of，Array.from()，Map(), Set(), WeakMap(), WeakSet()，Promise.all()，Promise.race()

遍历语法
1.for in
数组的键名是数字，但是for...in循环是以字符串作为键名“0”、“1”、“2”等等。for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。某些情况下，for...in循环会以任意顺序遍历键名。总之，for...in循环主要是为遍历对象而设计的，不适用于遍历数组。遍历对象自身的和继承的可枚举的属性, 不能直接获取属性值。可以中断循环。
2.map
只能遍历数组，不能中断，返回值是修改后的数组。
3.for...of
有着同for...in一样的简洁语法，但是没有for...in那些缺点。不同于forEach方法，它可以与break、continue和return配合使用。提供了遍历所有数据结构的统一操作接口。
4.foreach
无法中途跳出forEach循环，break命令或return命令都不能奏效。
5.for
没啥说的了














