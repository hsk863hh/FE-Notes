## 什么是 instanceof
在 JavaScript 中，instanceof 是一个用于`检测对象与构造函数之间关系`的运算符，它可以判断一个对象是否是某个构造函数的实例（包括继承关系中的实例）。

## 基本用法
`对象 instanceof 构造函数`

```js
[] instanceof Array // true
```

- 返回值：boolean（true 表示对象是该构造函数的实例，false 则不是）。

### 核心作用
判断对象的原型链中是否存在该构造函数的 prototype(原型) 属性。
简单来说：如果对象是通过 new 构造函数() 创建的，或其原型链上有该构造函数的原型，则返回 true。

示例说明
1. 基础使用（直接实例）
```js
// 定义构造函数
function Person(name) {
  this.name = name;
}

// 创建实例
const person = new Person('张三');

// 判断：person 是 Person 的实例吗？
console.log(person instanceof Person); // true
console.log(person.__proto__ === Person.prototype); // true
```
2. 继承关系中的判断

instanceof 会检查整个原型链，因此子类实例也会被认为是父类的实例：

```js
// 父类
function Animal() {}

// 子类（继承自 Animal）
function Dog() {}
Dog.prototype = new Animal(); // 设置继承关系
Dog.prototype.constructor = Dog;

// 创建子类实例
const dog = new Dog();

// 判断
console.log(dog instanceof Dog);    // true（直接实例）
console.log(dog instanceof Animal); // true（父类实例，原型链中存在 Animal.prototype）
console.log(dog instanceof Object); // true（所有对象都继承自 Object）
```

3. 原生对象的判断

可用于检测数组、日期等原生对象的类型：
```js
const arr = [1, 2, 3];
const date = new Date();

console.log(arr instanceof Array);  // true（arr 是 Array 的实例）
console.log(date instanceof Date);  // true（date 是 Date 的实例）
console.log(arr instanceof Object); // true（数组也是对象）
```

## 注意事项
1. 不能检测基本数据类型: instanceof 仅适用于对象，对字符串、数字等基本类型无效：
```js
const str = 'hello';
const num = 123;

console.log(str instanceof String); // false（str 是基本类型，不是 String 对象）
console.log(new String('hello') instanceof String); // true（通过 new 创建的对象）
```

2. 与 typeof 的区别

| 特性         | instanceof                          | typeof                                 |
|--------------|-------------------------------------|----------------------------------------|
| 作用         | 判断对象是否是某个构造函数的实例    | 检测基本数据类型（返回字符串）        |
| 适用类型     | 对象                                | 基本类型、函数、undefined             |
| 继承检测     | 支持（检查原型链）                  | 不支持（仅检测直接类型）              |
| 示例         | `arr instanceof Array` → true       | `typeof arr` → `"object"`             |

## 手写一个 instanceof 函数
实现要求
- 沿着原型链向上查找
- 找到匹配的原型则返回true
- 查找到Object.prototype.__proto__为null时返回false

```js
function myInstanceof(obj, fn) {
  if(obj==null) {
    return false;
  }
  const origin = Object.getPrototypeOf(obj);
  if(origin) {
    return origin === fn.prototype || myInstanceof(origin, fn);
  }
  return false;
}

console.log(myInstanceof([], Array));
console.log(myInstanceof([], Object));
console.log(myInstanceof([], String));
```

这里使用 `Object.getPrototypeOf` 获取对象的原型对象，而不是 `obj.__proto__`（这个不是官方提供的正式api，是浏览器厂商的私有属性，不同浏览器的实现可能不同）。

这里简单的口诀就是，对象的原型对象指向构造函数的原型对象，当对象原型对象为null时，返回false。