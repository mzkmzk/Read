# 编写可维护的JavaScript

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




