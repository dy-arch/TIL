# 함수 호출 방식에 의해 결정되는 this

자바스크립트의 함수는 호출될 때, 매개변수로 전달되는 인자값 이외에 ```arguments```객체와 ```this```를 암묵적으로 전달 받는다.

```js
// JavaScript
function square(number) {
  console.log(arguments);
  console.log(this);

  return number * number;
}

square(2);
```
```java
// Java
public Class Person {
  private String name;

  public Person(String name) {
    this.name = name;
  }
}
```
자바스크립트의 ```this``` keyword는 Java와 같은 익숙한 언어의 개념과 달라 개발자에게 혼란을 준다.

Java에서의 this는 인스턴스 자신(self)을 가리키는 참조변수이다. this가 객체 자신에 대한 참조값을 가지고 있다는 뜻이다. <br>
Java 코드의 생성자 함수 내의 this.name은 멤버변수를 의미하며 name은 생성자 함수가 전달받은 매개변수를 의미한다.

하지만 자바스크립트의 경우 Java와 같이 this에 바인딩되는 객체는 한가지가 아니라 해당 함수 호출 방식에 따라 this에 바인딩되는 객체가 달라진다.

### 함수 호출 방식과 this 바인딩

자바스크립트의 경우 함수 호출 방식에 의해 ```this```에 바인딩할 어떤 객체가 동적으로 결정된다.

함수의 호출 방식은 아래와 같다.

```js
const foo = function () {
  console.dir(this);
};

// 1. 함수 호출
foo(); // window
// window.foo();

// 2. 메소드 호출
const obj = { foo: foo };
obj.foo();  // Object foo => obj

// 3. 생성자 함수 호출
const instance = new foo(); // foo => instance

// 4. apply/call/bind 호출
const bar = { name: 'bar' }
foo.call(bar);      // bar
foo.apply(bar);     // bar
foo.bind(bar)();    // bar
```

## 1. 함수 호출

- 전역객체(Global Object)는 모든 객체의 유일한 최상위 객체를 의미하며 일반적으로 Browser-side에서는 ```window```, Server-side(Node.js)에서는 ```globar``` 객체를 의미한다.
- 전역객체는 전역 스코프(Global Scope)를 갖는 전역변수(Global variable)를 프로퍼티로 소유한다.
- 글로벌 영역에 선언한 함수는 전역객체의 프로퍼티로 접근할 수 있는 전역 변수의 메소드이다.

```js
const ga = 'Global variable';

console.log(ga);
console.log(window.ga);

function foo() {
  console.log('invoked!');
}

window.foo();
```

기본적으로 ```this```는 전역객체(Global object)에 바인딩된다. 전역함수는 물론이고 심지어 내부함수의 경우도 ```this```는 외부함수가 아닌 전역객체에 바인딩된다.

```js
function foo() {
  console.log("foo's this: ", this)     // window
  function bar() {
    console.log("bar's this: ", this)   // window
  }
  bar();
}
foo();
```

또한 메소드의 내부함수일 경우에도 ```this```는 전역객체에 바인딩된다.

```js
const value = 1;

const obj = {
  value: 100,
  foo: function() {
    console.log("foo's this: ", this);                // obj
    console.log("foo's this.value: ", this.value);    // 100
    function bar() {
      console.log("bar's this: ", this);              // window
      console.log("bar's this.value: ", this.value)   // 1
    }
    bar();
  }
};

obj.foo();
```

콜백함수의 경우에도 ```this```는 전역객체에 바인딩된다.

```js
const value = 1;

const obj = {
  value: 100,
  foo: function () {
    setTimeout(function () {
      console.log("callback's this: ", this);           //window
      console.log("callback's this.value", this.value)  // 1
    }, 100)
  }
};

obj.foo();
```

- 내부함수는 일반 함수, 메소드, 콜백함수 어디에서 선언되었든 관계없이 this는 전역객체를 바인딩한다.
- 더글라스 크락포드는 "이것은 설계 단계의 결함으로 메소드가 내부함수를 사용하여 자신의 작업을 돕게 할 수 없다는 것을 의미한다" 라고 말한다.

### 회피 방법
```js
const value = 1;
const obj = {
  value: 100,
  foo: function () {
    const that = this;      // Workaround : this === obj

    console.log("foo's this: ", this)                 // obj
    console.log("foo's this.value: ", this.value);    // 100
    function bar() {
      console.log("bar's this: ", this);              // window
      console.log("bar's this.vaule: ", this.value);  // 1

      console.log("bar's that: ", that);              // obj
      console.log("bar's that.value: ", that.value);  // that.value
    }
    bar();
  }
};

obj.foo();
```
- foo 함수 내부에 that 상수가 obj객체를 가리키도록 하고 bar 내부에서 사용하고 있다.

위 방법 이외에도 자바스크립트는 this를 명시적으로 바인딩할 수 있는 apply, call, bind 메소드를 제공한다.
```js
const value = 1;

const obj = {
  value: 100,
  foo: function () {
    console.log("foo's this: ", this)                 // obj
    console.log("foo's this.value: ", this.value);    // 100
    function bar(a, b) {
      console.log("bar's this: ", this);              // obj
      console.log("bar's this.vaule: ", this.value);  // 100
      console.log("bar's arguments: ", arguments);
    }
    bar.apply(obj, [1, 2]);
    bar.call(obj, 1, 2);
    bar.bind(obj)(1, 2);
  }
}

obj.foo(();
```

## 2. 메소드 호출

함수가 객체의 프로퍼티 값이면 메소드로서 호출된다. 이때 메소드 내부의 ```this```는 해당 메소드를 소유한 객체, 즉 해당 메소드를 호출한 객체에 바인딩된다.

```js
const obj1 = {
  name: 'Lee',
  sayName: function () {
    console.log(this.name);
  }
}

const obj2 = {
  name: 'Kim',
}

obj2.sayName = obj.sayName;

obj1.sayName();   // Lee
obj2.sayName();   // Kim
```

프로토타입 객체도 메소드를 가질 수 있다. 프로토타입 객체 메소드 내부에서 사용된 this도 일반 메소드 방식과 마찬가지로 해당 메소드를 호출한 객체에 바인딩된다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
}

const me = new Person('Lee');
console.log(me.getName());      // Lee

Person.prototype.name = 'Kim';
console.log(Person.prototype.getName());  // Kim
```

## 3. 생성자 함수 호출

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');
console.log(me)     // { name: "Lee" }

// new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수로 동작하지 않는다.
const you = Person('Kim');
console.log(you);   // undefined
```
new 연산자와 함께 생성자 함수를 호출하면 this 바인딩이 메소드나 함수 호출 때와는 다르게 동작한다.

### 3-1 생성자 함수 동작 방식

new 연산자와 함께 생성자 함수를 호출하면 다음과 같은 수순으로 동작한다.

> 1. 빈 객체 생성 및 this 바인딩 <br>
> 생성자 함수의 코드가 실행되기 전 빈 객체가 생성된다. 이 객체가 생성자 함수가 새로 생성하는 객체이다. 이후 **생성자 함수 내에서 사용되는 this는 이 빈 객체를 가리킨다.** 그리고 생성된 빈 객체는 생성자 함수의 prototype 프로퍼티가 가리키는 객체를 자신의 프로토타입 객체로 설정한다.
> 2. this를 통한 프로퍼티 생성 <br>
> 생성된 빈 객체에 this를 사용하여 동적으로 프로퍼티나 메소드를 생성할 수 있다. this는 새로 생성된 객체를 가리키므로 this를 통해 생성한 프로퍼티와 메소드는 새로 생성된 객체에 추가된다.
> 3. 생성된 객체 반환
> - 반환문이 없는 경우, this에 바인딩된 새로 생성한 객체가 반환된다. 명시적으로 thisㄹ르 반환하여도 결과는 같다.
> - 반환문이 this가 아닌 다른 객체를 명시적으로 반환하는 경우, this가 아닌 해당 객체가 반환된다. 이때 this를 반환하지 않은 함수는 생성자 함수로서의 역할을 수행하지 못한다. 따라서 생성자 함수는 반환문을 명시적으로 사용하지 않는다.

```js
function Person(name) {
  // 생성자 함수 코드 실행전 --------------1
  this.name = name; //---------------------2
  //생성된 함수 반환 ----------------------3
}

const me = new Person('Lee');
console.log(me.name);
```

### 3-2 객체 리터럴 방식과 생성자 함수 방식의 차이

객체 리터럴 방식과 생성자 함수 방식의 차이를 비교해 보자.

```js
const foo =  {
  name: 'foo',
  gender: 'male',
}

console.dir(foo);

// 생성자 함수 방식
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
}

const me = new Person('Lee', 'male');
console.dir(me);

const you = new Person('Kim', 'female');
console.dir(you);
```
- 객체 리터럴 방식과 생성자 함수 방식의 차이는 프로토타입 객체([[Prototype]])에 있다.
- 객체 리터럴 방식의 경우, 생성된 객체의 프로토타입 객체는 Object.prototype이다.
- 생성자 함수 방식의 경우, 생성된 객체의 프로토타입 객체는 Person.prototype이다.

### 3-3 생성자 함수에 new 연산자를 붙이지 않고 호출할 경우

**일반함수와 생성자 함수에 특별한 형식적 차이는 없으며 함수에 new 연산자를 붙여서 호출하면 해당함수는 생성자 함수로 동작.**

일반 함수를 호출하면 this는 전역객체에 바인딩되지만 new 연산자와 함께 생성자 함수를 호출하면 this는 생성자 함수가 암묵적으로 생성한 빈 객체에 바인딩된다. <br>
일반함수와 생성자 함수를 구분하기 위해 생성자 함수는 첫문자를 대문자로 기술하여 혼란을 방지한다. 하지만 실수는 발생할 수 있다. <br>
이러한 위험성을 회피하기 위해 다음과 같은 패턴(Scope-Safe Constructor)은 다음과 같다. 이 패턴은 대부분의 라이브러리에서 광범위하게 발생한다.

```js
// Scope-Safe Constructor Pattern
function A(arg) {
  // 생성자 함수가 new 연산자와 함께 호출되면 선두에서 빈 객체를 생성하고 this에 바인딩.

  /*
  this가 호출된 함수(arguments.callee, 본 예제의 경우 A)의 인스턴스가 아니면 new 연산자를 사용하지 않은 것이므로 이 경우 new와 함께 함수를 호출하여 인스턴스를 반환한다.
  arguments.callee는 호출된 함수의 이름을 나타낸다. 이 예제의 경우 A로 표기하여도 문제없이 동작하지만 특정함수의 이름과 의존성을 없애기 위해서 arguments.callee를 사용하는 것이 좋다.
  */
 if (!(this instanceof arguments.callee)) {
   return new arguments.callee(arg);
 }

 this.value = arg ? arg : 0;
}

const a = new A(100);
const b = A(10);

console.log(a.value);   // 100
console.log(b.value);   // 10
```

## 4. apply/call/bind 호출

this에 바인딩될 객체는 함수 호출 패턴에 의해 결정된다. 이는 자바스크립트 엔진이 수행하는 것이다. 이러한 암묵적 this 바인딩 이외에 this를 특정 객체에 명시적을 바인딩하는 방법도 제공된다. Function.prototype 객체의 apply, call 메소드가 그 예이다.

**apply**
```js
const Person = function (name) {
  this.name = name;
};

const foo = {};

// apply 메소드는 생성자함수 Person을 호출한다. 이때 this에 객체 foo를 바인딩한다.
Person.apply(foo, ['name']);

console.log(foo); // { name: 'name' }
```
- apply() 메소드를 호출하는 주체는 함수이며 apply() 메소드는 this를 특정 객체에 바인딩할 뿐 본질적인 기능은 함수 호출이다.
- 빈 객체 foo를 apply() 메소드의 첫 번째 매개변수로, argument의 배열을 두 번째 매개변수에 전달했다.
- 이때 Person 함수의 this는 foo 객체가 된다.
- foo 객체에는 name 프로퍼티가 없기 때문에 동적 추가되고 값이 할당된다.

```js
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // slice: 배열의 특정 부분에 대한 복사본을 생성한다.
  const arr = Array.prototype.slice.apply(argumetns); // arguments.slice
  // const arr = [].slice.apply(arguments);

  console.log(arr);
  return aa;
}

convertArgsToArray(1, 2, 3);
```
- apply() 메소드의 대표적인 용도는 arguments 객체와 같은 유사 배열 객체에 배열 메소드를 사용하는 경우이다.

**call**
```js
Person.call(foo, 1, 2, 3);
```
- call() 메소드의 경우 apply()와 달리 배열 형태의 인자가 아닌 각각 하나의 인자로 넘긴다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.doSomething = function(callback) {
  if (typeof callback = 'function') {
    // ------------ 1
    callback();
  }
};

function foo() {
  console.log(this.name); // ---------- 2
}

const p = new Person('Lee');
p.doSomething(foo); // undefined
```
- 1의 시점에서 this는 Person 객체이다. 하지만 2의 시점에서 this는 전역 객체 window를 가리킨다.
- 콜백함수를 호출하는 doSomething 내부 this와 콜백함수 내부의 this가 바인딩하는 객체가 다르다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.doSomething = function (callback) {
  if (typeof callback = 'function') {
    callback.call(this);
  }
};

function foo() {
  console.log(this.name);
}

const p = new Person('Lee');
p.doSomething(foo);  // 'Lee'
```
- 콜백 함수와 doSomething 메소드의 this를 call 메소드를 통해 같은 객체로 바인딩하였다.

**bind**

```js
function Person(name) {
  this.name = name;
}

Person.prototype.doSomething = function(callback) {
  if (typeof callback === 'function') {
    callback.bind(this)();
  }
};

function foo() {
  console.log(this.name);
}

const p = new Person('Lee');
p.doSomething(foo); // 'Lee'
```
- bind는 apply, call 메소드와 달리 인자로 전달된 객체가 바인딩된 **새로운 함수**를 호출한다.