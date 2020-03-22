---
title: js基础系列之继承
date: 2018-11-13 23:04:40
tags: js
---
js实现继承的不仅仅是原型链继承，今天带你看看更牛逼的继承方式

<!-- more -->

## 原型链继承

    function Father() {
        this.color = ['color1','color2']
    }
    Father.prototype.getColor = function () {
        return this.color
    }
    function Son() {
    
    }
    
    Son.prototype = new Father()
    
    let instance = new Son()
    instance.color.push('color3')
    console.log(instance.color) // ['color1','color2','color3']
    
    let instance2 = new Son()
    console.log(instance2.color) // ['color1','color2','color3']
    
    let instance3 = new Father()
    
    console.log(instance3.color) // ['color1','color2']
    
问题:如果是包含引用类型的原型属性会被所有实例共享；创建子类型的实例，不能向父类型的构造函数中传参

## 借用构造函数继承

    function Father() {
        this.color = ['color1','color2']
    }
    function Son() {
        Father.call(this) // 在子类型中调用父类型的构造函数，每一个实例创建的时候都会走到这里，相当与每个实例都有一个color的副本
    }
    
    let instance = new Son()
    instance.color.push('color3')
    console.log(instance.color) // ['color1','color2','color3']
    
    let instance2 = new Son()
    console.log(instance2.color) // ['color1','color2']
    
    let instance3 = new Father()
    console.log(instance3.color) // ['color1','color2']
    
问题:没啥复用性了，所有的东西都放构造函数里了

## 组合继承

    function Father(name) {
        this.name = name
        this.color = ['color1','color2']
    }
    Father.prototype.sayName = function () {
        console.log(this.name)
    }
    function Son(name,age) {
        Father.call(this,name)
        this.age = age
    }
    Son.prototype = new Father()
    Son.prototype.sayAge = function () {
        console.log(this.age)
    }
    
    let instance = new Son('wangyuqing','23')
    instance.color.push('color3')
    console.log(instance.color) // ['color1','color2','color3']
    instance.sayName()
    instance.sayAge()
    
    let instance2 = new Son('chenjun',25)
    console.log(instance2.color) // ['color1','color2','color3']
    instance2.sayName()
    instance2.sayAge()
    
解决了原型链和借用构造函数的缺点

## 原型式继承

    let person = {
       name:'person',
       family:['mother','me']
    }
    let person1 = Object.create(person)
    person1.name = 'person1'
    person1.family.push('father')
    console.log(person1.name)
    console.log(person1.family)
    
    let person2 = Object.create(person)
    person2.name = 'person2'
    console.log(person2.name)
    console.log(person2.family)
    
    console.log(person.name)

问题：和原型链继承一样，实例会对引用类型的属性进行共享

## 寄生式继承

    function creatAnthor(data){
        let clone = Object.create(data)
        clone.sayHi = function () {
            console.log('hi')
        }
        return clone
    }
    let person = {
        name:'person',
        family:['mother','me']
    }
    let person1 = creatAnthor(person)
    person1.sayHi()
    person1.name = 'person1'
    person1.family.push('father')
    console.log(person1.name)
    console.log(person1.family)
    
    let person2 = creatAnthor(person)
    person2.name = 'person2'
    person2.sayHi()
    console.log(person2.name)
    console.log(person2.family)
    
问题：和原型式继承基本一样，只是封装了一个方法返回了一个新对象，这个对象不仅有原来的data的属性和方法，还有自己的方法。其实就是在原型式继承上增强一下。与构造函数模式类似，会有做不到复用的情况。

## 重中之重：寄生组合式继承

    
    function Father(name) {
        this.name = name
        this.color = ['color1','color2']
    }
    Father.prototype.sayName = function () {
        console.log(this.name)
    }
    function Son(name,age) {
        Father.call(this,name)
        this.age = age
    }
    
    // Son.prototype = new Father()  组合继承的实现，会执行两次父类构造函数
    // -----新增-----
    // 其实就是把父类的原型指给子类的原型，但因为怕只想原型的过程中constructor丢失，就手动指一下
    let prototype = Object.create(Father.prototype) // 创建对象
    prototype.constructor = Son // 增强对象
    Son.prototype = prototype // 指定对象
    
    Son.prototype.sayAge = function () {
        console.log(this.age)
    }
    
    let instance = new Son('wangyuqing','23')
    instance.color.push('color3')
    console.log(instance.color) // ['color1','color2','color3']
    instance.sayName()
    instance.sayAge()
    
    let instance2 = new Son('chenjun',25)
    console.log(instance2.color) // ['color1','color2','color3']
    instance2.sayName()
    instance2.sayAge()

解决了执行两次父类构造函数的问题








































