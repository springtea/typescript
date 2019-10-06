# 基础

## 原始数据类型

### 布尔值

+ 在 TypeScript 中，`boolean`是 JavaScript 中的基本类型，而 `Boolean`是 JavaScript 中的构造函数。

### 数值

+ 用number定义

```typescript
let decLiteral: number = 6;
//十进制表示法
let hexLiteral: number = 0xf00d;
// ES6 中的二进制表示法
let binaryLiteral: number = 0b1010;
// ES6 中的八进制表示法
let octalLiteral: number = 0o744;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
```

### 字符串

```typescript
let myName: string = 'Tom';
let myAge: number = 25;

// 模板字符串
let sentence: string = `Hello, my name is ${myName}.
I'll be ${myAge + 1} years old next month.`;
```

+ 其中 ```用来定义 [ES6 中的模板字符串](http://es6.ruanyifeng.com/#docs/string#模板字符串)，`${expr}`用来在模板字符串中嵌入表达式。

### 空值

+ 声明一个 `void`类型的变量没有什么用，因为你只能将它赋值为 `undefined`和 `null`

```typescript
let unusable: void = undefined;
```

### null和undefined

+ 与 `void`的区别是，`undefined`和 `null`是所有类型的子类型。也就是说 `undefined`类型的变量，可以赋值给 `number`类型的变量：

```typescript
// 这样不会报错
let num: number = undefined;

```

+ 而 `void`类型的变量不能赋值给 `number`类型的变量：

```typescript
let u: void;
let num: number = u;

// Type 'void' is not assignable to type 'number'.
```

## 任意值

### 认识任意值

如果是一个普通类型，在赋值过程中改变类型是不被允许的：

```typescript

```

```typescript
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;


// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```



```

```

但如果是 `any`类型，则允许被赋值为任意类型。

```typescript

let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;



```

### 任意值的属性和方法

在任意值上访问任何属性都是允许的：



```typescript
let anyThing: any = 'hello';
console.log(anyThing.myName);
console.log(anyThing.myName.firstName);
```

也允许调用任何方法：



```typescript
let anyThing: any = 'Tom';
anyThing.setName('Jerry');
anyThing.setName('Jerry').sayHello();
anyThing.myName.setFirstName('Cat');
```

可以认为，**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**。

### 未声明类型的变量

+ **变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型**：

  ```typescript
  let something;
  something = 'seven';
  something = 7;
  
  something.setName('Tom');
  
  
  ```

## 类型推论

### 什么是类型推论

以下代码虽然没有指定类型，但是会在编译的时候报错：



```typescript
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;


// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

事实上，它等价于：



```typescript
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;


// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成** `any`**类型而完全不被类型检查**：



```typescript
let myFavoriteNumber;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```



## 联合类型（union-types)

### 认识联合类型

```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```



```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = true;


// index.ts(2,1): error TS2322: Type 'boolean' is not assignable to type 'string | number'.
//   Type 'boolean' is not assignable to type 'number'.
```

联合类型使用 `|`分隔每个类型。

这里的 `let myFavoriteNumber: string | number`的含义是，允许 `myFavoriteNumber`的类型是 `string`或者 `number`，但是不能是其他类型。

### 访问联合类型的属性和方法

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**：



```typescript
function getLength(something: string | number): number {
    return something.length;
}


// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

上例中，`length`不是 `string`和 `number`的共有属性，所以会报错。

访问 `string`和 `number`的共有属性是没问题的：



```typescript
function getString(something: string | number): string {
    return something.toString();
}
```

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：



```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错


// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```

**上例中，第二行的 `myFavoriteNumber`被推断成了 `string`，访问它的 `length`属性不会报错。**

**而第四行的 `myFavoriteNumber`被推断成了 `number`，访问它的 `length`属性时就报错了。**

## 接口

### 什么是接口

在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）。

TypeScript 中的接口是一个非常灵活的概念，除了可用于对类的一部分行为进行抽象以外，也常用于对「对象的形状（Shape）」进行描述。

### 简单的例子



```typescript
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```



**定义的变量比接口少了一些属性是不允许的：**



```typescript
interface Person {
    name: string;
    age: number;
}


let tom: Person = {
    name: 'Tom'
};


// index.ts(6,5): error TS2322: Type '{ name: string; }' is not assignable to type 'Person'.
//   Property 'age' is missing in type '{ name: string; }'.
```

**多一些属性也是不允许的：**



```typescript
interface Person {
    name: string;
    age: number;
}


let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};


// index.ts(9,5): error TS2322: Type '{ name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Object literal may only specify known properties, and 'gender' does not exist in type 'Person'.
```

可见，**赋值的时候，变量的形状必须和接口的形状保持一致**。

### 可选属性

有时我们希望不要完全匹配一个形状，那么可以用可选属性：



```typescript
interface Person {
    name: string;
    age?: number;
}


let tom: Person = {
    name: 'Tom'
};
```



```typescript
interface Person {
    name: string;
    age?: number;
}


let tom: Person = {
    name: 'Tom',
    age: 25
};
```

可选属性的含义是该属性可以不存在。

这时**仍然不允许添加未定义的属性**：



```typescript
interface Person {
    name: string;
    age?: number;
}


let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};


// examples/playground/index.ts(9,5): error TS2322: Type '{ name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Object literal may only specify known properties, and 'gender' does not exist in type 'Person'.
```

### 任意属性

有时候我们希望一个接口允许有任意的属性，可以使用如下方式：



```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}


let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

使用 `[propName: string]`定义了任意属性取 `string`类型的值。

需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集**：



```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}


let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};


// index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
// index.ts(7,5): error TS2322: Type '{ [x: string]: string | number; name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Index signatures are incompatible.
//     Type 'string | number' is not assignable to type 'string'.
//       Type 'number' is not assignable to type 'string'.
```

上例中，任意属性的值允许是 `string`，但是可选属性 `age`的值却是 `number`，`number`不是 `string`的子属性，所以报错了。

另外，在报错信息中可以看出，此时 `{ name: 'Tom', age: 25, gender: 'male' }`的类型被推断成了 `{ [x: string]: string | number; name: string; age: number; gender: string; }`，这是联合类型和接口的结合。

### 只读属性

有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 `readonly`定义只读属性：



```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}


let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};


tom.id = 9527;


// index.ts(14,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
```

上例中，使用 `readonly`定义的属性 `id`初始化后，又被赋值了，所以报错了。

**注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候**：



```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}


let tom: Person = {
    name: 'Tom',
    gender: 'male'
};


tom.id = 89757;


// index.ts(8,5): error TS2322: Type '{ name: string; gender: string; }' is not assignable to type 'Person'.
//   Property 'id' is missing in type '{ name: string; gender: string; }'.
// index.ts(13,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
```

上例中，报错信息有两处，第一处是在对 `tom`进行赋值的时候，没有给 `id`赋值。

第二处是在给 `tom.id`赋值的时候，由于它是只读属性，所以报错了。

## 数组

### 「类型 + 方括号」表示法

最简单的方法是使用「类型 + 方括号」来表示数组：



```typescript
let fibonacci: number[] = [1, 1, 2, 3, 5];
```

数组的项中**不允许**出现其他的类型：



```typescript
let fibonacci: number[] = [1, '1', 2, 3, 5];


// Type 'string' is not assignable to type 'number'.
```

数组的一些方法的参数也会根据数组在定义时约定的类型进行限制：



```typescript
let fibonacci: number[] = [1, 1, 2, 3, 5];
fibonacci.push('8');


// Argument of type '"8"' is not assignable to parameter of type 'number'.
```

上例中，`push`方法只允许传入 `number`类型的参数，但是却传了一个 `"8"`类型的参数，所以报错了。这里 `"8"`是一个字符串字面量类型，会在后续章节中详细介绍。

### 数组泛型

我们也可以使用数组泛型（Array Generic） `Array<elemType>`来表示数组：



```
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

关于泛型，可以参考泛型一章。

### 用接口表示数组

接口也可以用来描述数组：



```typescript
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

`NumberArray`表示：只要索引的类型是数字时，那么值的类型必须是数字。

虽然接口也可以用来描述数组，但是我们一般不会这么做，因为这种方式比前两种方式复杂多了。

不过有一种情况例外，那就是它常用来表示类数组。

### 类数组

类数组（Array-like Object）不是数组类型，比如 `arguments`：



```typescript
function sum() {
    let args: number[] = arguments;
}


// Type 'IArguments' is missing the following properties from type 'number[]': pop, push, concat, join, and 24 more.
```

上例中，`arguments`实际上是一个类数组，不能用普通的数组的方式来描述，而应该用接口：



```typescript
function sum() {
    let args: {
        [index: number]: number;
        length: number;
        callee: Function;
    } = arguments;
}
```

在这个例子中，我们除了约束当索引的类型是数字时，值的类型必须是数字之外，也约束了它还有 `length`和 `callee`两个属性。

事实上常用的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection`等：



```typescript
function sum() {
    let args: IArguments = arguments;
}
```

其中 `IArguments`是 TypeScript 中定义好了的类型，它实际上就是：



```typescript
interface IArguments {
    [index: number]: any;
    length: number;
    callee: Function;
}
```

关于内置对象，可以参考内置对象一章。

### any 在数组中的应用

一个比较常见的做法是，用 `any`表示数组中允许出现任意类型：



```typescript
let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }];
```

## 函数

### 函数表达式

如果要我们现在写一个对函数表达式（Function Expression）的定义，可能会写成这样：



```typescript
let mySum = function (x: number, y: number): number {
    return x + y;
};
```

这是可以通过编译的，不过事实上，上面的代码只对等号右侧的匿名函数进行了类型定义，而等号左边的 `mySum`，是通过赋值操作进行类型推论而推断出来的。如果需要我们手动给 `mySum`添加类型，则应该是这样：



```typescript
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
```

注意不要混淆了 TypeScript 中的 `=>`和 ES6 中的 `=>`。

在 TypeScript 的类型定义中，`=>`用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。

在 ES6 中，`=>`叫做箭头函数，应用十分广泛，可以参考 [ES6 中的箭头函数](http://es6.ruanyifeng.com/#docs/function#箭头函数)。

### 用接口定义函数的形状

我们也可以使用接口的方式来定义一个函数需要符合的形状：



```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}


let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

### 可选参数

前面提到，输入多余的（或者少于要求的）参数，是不允许的。那么如何定义可选的参数呢？

与接口中的可选属性类似，我们用 `?`表示可选的参数：



```typescript
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

需要注意的是，可选参数必须接在必需参数后面。换句话说，**可选参数后面不允许再出现必需参数了**：



```typescript
function buildName(firstName?: string, lastName: string) {
    if (firstName) {
        return firstName + ' ' + lastName;
    } else {
        return lastName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName(undefined, 'Tom');


// index.ts(1,40): error TS1016: A required parameter cannot follow an optional parameter.
```

### 参数默认值

在 ES6 中，我们允许给函数的参数添加默认值，**TypeScript 会将添加了默认值的参数识别为可选参数**：



```typescript
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

此时就不受「可选参数必须接在必需参数后面」的限制了：



```typescript
function buildName(firstName: string = 'Tom', lastName: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```

> 关于默认参数，可以参考 [ES6 中函数参数的默认值](http://es6.ruanyifeng.com/#docs/function#函数参数的默认值)。

### 剩余参数

ES6 中，可以使用 `...rest`的方式获取函数中的剩余参数（rest 参数）：



```typescript
function push(array, ...items) {
    items.forEach(function(item) {
        array.push(item);
    });
}


let a = [];
push(a, 1, 2, 3);
```

事实上，`items`是一个数组。所以我们可以用数组的类型来定义它：



```typescript
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}


let a = [];
push(a, 1, 2, 3);
```

注意，rest 参数只能是最后一个参数，关于 rest 参数，可以参考 [ES6 中的 rest 参数](http://es6.ruanyifeng.com/#docs/function#rest参数)。

### 重载

重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。

比如，我们需要实现一个函数 `reverse`，输入数字 `123`的时候，输出反转的数字 `321`，输入字符串 `'hello'`的时候，输出反转的字符串 `'olleh'`。

利用联合类型，我们可以这么实现：



```typescript
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

然而这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。

这时，我们可以使用重载定义多个 `reverse`的函数类型：



```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

上例中，我们重复定义了多次函数 `reverse`，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。

注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。

## 类型断言

类型断言（Type Assertion）可以用来手动指定一个值的类型。

### 语法



```
<类型>值
```

或



```
值 as 类型
```

在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种。

### 例子：将一个联合类型的变量指定为一个更加具体的类型

之前提到过，当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**：



```typescript
function getLength(something: string | number): number {
    return something.length;
}


// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

而有时候，我们确实需要在还不确定类型的时候就访问其中一个类型的属性或方法，比如：



```typescript
function getLength(something: string | number): number {
    if (something.length) {
        return something.length;
    } else {
        return something.toString().length;
    }
}


// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
// index.ts(3,26): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

上例中，获取 `something.length`的时候会报错。

此时可以使用类型断言，将 `something`断言成 `string`：



```typescript
function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
```

类型断言的用法如上，在需要断言的变量前加上 `<Type>`即可。

**类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的**：



```typescript
function toBoolean(something: string | number): boolean {
    return <boolean>something;
}


// index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
//   Type 'number' is not comparable to type 'boolean'.


```

# 进阶

## 元组

> > > 数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。

元组起源于函数编程语言（如 F#），这些语言中会频繁使用元组。

### 简单的例子

定义一对值分别为 `string`和 `number`的元组：



```typescript
let tom: [string, number] = ['Tom', 25];
```

当赋值或访问一个已知索引的元素时，会得到正确的类型：



```typescript
let tom: [string, number];
tom[0] = 'Tom';
tom[1] = 25;


tom[0].slice(1);
tom[1].toFixed(2);
```

也可以只赋值其中一项：



```typescript
let tom: [string, number];
tom[0] = 'Tom';
```

但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项。



```
let tom: [string, number];
tom = ['Tom', 25];
```



```typescript
let tom: [string, number];
tom = ['Tom'];


// Property '1' is missing in type '[string]' but required in type '[string, number]'.
```

### 越界的元素

**当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型：**



```typescript
let tom: [string, number];
tom = ['Tom', 25];
tom.push('male');
tom.push(true);


// Argument of type 'true' is not assignable to parameter of type 'string | number'.
```