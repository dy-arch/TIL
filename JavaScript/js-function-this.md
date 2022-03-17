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