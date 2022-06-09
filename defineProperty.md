# Object.defineProperty

## 1. 什么意思

- Object 对象
- define 定义
- Property 属性

## 2. 定义

```javascript
var obj = {}

console.log(obj);

var newObj = Object.defineProperty(obj, 'a', {
    value: 1
})

console.log(obj);

console.log(obj === newObj); // ture  --> defineProperty() 方法会直接在一个对象上定义一个新属性、或者修改一个对象的现有属性，并返回这个对象。
```

## 3. 调用

```javascript
var obj = {}

console.log(obj.defineProperty()); // error --> 必须通过 Object.defineProperty() 来调用
```

## 4. 描述

```javascript
/**
 * @param:obj 对象
 * @param:prop 属性名
 * @param:descriptor 属性描述对象
 */
Object.defineProperty(obj, 'a', {
    value: 1,
})


let obj = {}

Object.defineProperty(obj, 'a', {
    value: 1
})

obj.a = 2;
console.log(obj.a); // 1 --> 属性默认不可修改的

delete obj.a;
console.log(obj.a); // 1 --> 属性默认是不可重写的

for (const objKey in obj) {
    console.log(objKey); // '' -->  属性默认是不可枚举的
}
```

## 5. decriptor

```javascript
/**
 * {
 *     // 数据描述符，存取描述符
 *     // 数据描述符：是一个具有值的属性
 *     // 存取描述符：是由getter和setter函数所描述的属性
 * }
 */
```

[数据描述符和存取描述符的区分](#数据描述符、存取描述符)

## 6. configureable

```javascript
let obj = {}

Object.defineProperty(obj, 'a', {
    value: 1,
    configurable: true, // 当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除
    enumerable: true, // 能否枚举
    writable: true, // 可否被重写
})

console.log(obj.a); // 1

delete obj.a;

console.log(obj.a); // undefined  --> 可以成功删除

for (const objKey in obj) {
    console.log(objKey); // a --> 可以成功枚举
}

obj.a = 2;
console.log(obj.a); // 2 --> 可以成功重写
```

## 7. getter setter

```javascript
let obj = {};

Object.defineProperty(obj, 'a', {
    // value: 1,
    get() {  // 访问属性时，会调用此函数，该函数的返回值会被用作属性的值
        console.log('get a:', 1);
        return 1;
    },
    set(newVal) {
        console.log('set a', newVal);
    }
})

console.log(obj.a);

obj.a = 2; // 控制台：set a 2 --> 在给 obj.a 赋值时，会被 defineProperty 拦截，然后运行， set ，并把 2 作为参数传入 set
```

### 数据描述符、存取描述符

**如果一个描述符不具有 value、writable、enumerable、configurable 属性，那么它将被认为是一个数据描述符

如果一个描述符同时拥有 value 或 writable 和 get 或 set 键，则会产生一个异常**

## 8. 获取对象`key`的方法

```javascript
let obj = {};

Object.prototype.b = 2;
Object.defineProperty(obj, 'a', {
    value: 1,
})

for (const objKey in obj) {
    console.log(objKey); // 'b' -->  该方法不仅能获取自身的可枚举属性，还可以获取原型链上的属性。 a 是不可枚举的，但 b 是通过 ES3 创建，默认是可枚举的
}

console.log(Object.keys(obj)); // [] 该方法只能获取自身的可枚举属性，不能获取原型链上的属性

console.log(Object.getOwnPropertyNames(obj)); // ['a'] 该方法可以获取自身的可枚举属性，也可以获取原型链上的属性
```

## 一道有意思的相关题

```javascript
let _default = 0;
Object.defineProperty(window, 'a', {
    get() {
        return ++_default;
    }
})

if (a === 1 && a === 2 && a === 3) {
    console.log('a is 1, 2 and 3');
}
```

