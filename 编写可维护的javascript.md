# 编写可维护的JavaScript

# 总结

1~7章在讲很基本很基本的东西,初学者可以稍微看看,如果不是,可以多读读jquery,或者收益更大

第8章的避免空比较,说了下 typeof instanceof 讲的还可以,可能这块我之前也全面的研究过

第10章抛出自定义错误和第11章不是你的对象不要动,对我有用的感觉不多,基本几条,已经写出来了

至于后面的自动化部分,,,都是基于ant讲,,但我主要用webpack,也没细看

评分 2.5(总分5)

页数 226


# 第10章 抛出自定义错误

## 10.4 何时抛出错误

1. 一旦修复了一个很难调试的错误
2. 如果在编写代码的时,思考一下:'我希望某些事情不会发生,如果发生的话,我的代码会变成一坨屎'
3. 如果在编写代码给别人用,思考一下他们的使用方式,在特定的情况下抛出错误

# 第11章 不是你的对象不要动

## 11.3 更好的途径

### 11.3.1 基于对象的继承

```javascript
var person = {
    name: 'person_k',
    sayName: function(){
        console.log(this.name);
    }
}

var myPerson = Object.create(person,{
    name: {
         value: 'myPerson_k'
    }
})

person.sayName() //person_k
myPerson.sayName() //myPerson_k

```

Object.create的原理类似于

```javascript
Object.create = function (parent) {
    function F() {}
    F.prototype = parent;
    return new F();
};
```


### 11.3.2 基于类型的继承

1. 原型继承
2. 构造器继承

```javascript

function Person(name) {
    this.name = name
}

function Author(name) {
    Person.call(this, name); //继承构造器 this是new Author的时创造的this
}

Author.prototype = new Person(); //继承原型


```

## 11.4 关于Polyfill的注解

例如 String.trim Array.foEach 这些有些浏览器没兼容的东西

作者不建议使用pollfill,因为如果用了polyfills,就无法分离自己的bug和浏览器的bug

# 第13章 文件和目录结构

## 13.1 最佳实践

1. 一个文件只包含一个对象





