# 클로저

## 클로저란

- 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어(예: 하스켈, 리스프, 얼랭, 스칼라 등)에서 사용되는 중요한 특성이다.
- MDN에서는 클로저에 대해 다음과 같이 정의한다.

> "A closure is the combination of a function and the lexical environment within which that function was declared." <br>
> 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.

```js
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

outerFunc();
```
- outerFunc 함수 내부에서 innerFunc가 정의되고 호출되었다.
- innerFunc 내부에서 자신을 포함하고 있는 outerFunc의 x 변수에 접근할 수 있다.
- 클로저는 자신이 생성될 때의 환경(Lexical environment)을 기억하는 함수라고 말할 수 있다.

## 렉시컬 스코프
**자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다. 이를 렉시컬 스코프(정적 스코프)라 한다.**

```js
const x = 1;

function foo() {
  const x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo();
bar();
```
- 위 예제에서 foo()와 bar()의 결과는 1이다.
- 이처럼 함수를 호출한 위치는 상위 스코프 결정에 어떠한 영향도 주지 못한다.

## 2. 함수 객체의 내부 슬롯 **[[Environment]]**

함수 정의가 평가되어 함수 객체를 생성할 때 자신이 정의된 위치에 의해 결정된 상위 스코프의 참조를 함수 객체 자신의 내부 슬롯 **[[Environment]]** 에 저장한다. 이때 자신의 내부 슬롯 **[[Environment]]** 에 저장된 상위 스코프의 참조는 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 가리킨다. <br>
그 이유는 함수 정의가 평가되어 함수 객체를 생성하는 시점은 상위 함수가 평가 또는 실행되고 있는 시점이기 때문이다.

```js
const x = 1;

function foo() {
  const x = 10;

  // 상위 스코프는 함수 정의 환경(위치)에 따라 결정된다.
  // 함수 호출 위치와 상위 스코프는 아무런 관계가 없다.
  bar();
}

// 함수 bar는 자신의 상위 스코프인 전역 렉시컬 환경을 [[Environment]]에 저장하여 기억한다.
function bar() {
  console.log(x);

  // 함수 hi는 자신의 상위 스코프인 bar 함수 렉시컬 환경을 [[Environment]]에 저장하여 기억한다.
  function hi() {
    console.log('hi');
  }
  hi();
}

foo();  // ?
bar();  // ?
```
- 함수 foo와 bar는 전역 실행 컨텍스트가 평가될 때, foo, bar 함수 객체가 생성된다. 때문에 [[Environment]] 슬롯에 실행 중인 실행 컨텍스의 렉시컬 환경 참조가 저장된다.
- 함수 hi는 bar 함수가 평가되면서 함수 객체가 생성된다. 함수 hi [[Environment]] 슬롯에는 실행 중인 컨텍스트, bar 함수 실행 컨텍스트의 렉시컬 환경이 저장된다.

## 3. 클로저와 렉시컬 환경
```js
const x = 1;

// ①
function outer() {
  const x = 10;
  const inner = function () { console.log(x); };  // ②
  return inner;
}

// outer 함수를 호출하면 중첩 함수 inner를 반환한다.
// 그리고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 팝되어 제거된다.
const innerFunc = outer();  // ③
innerFunc();  // ④ 10
```
- ③ outer 함수를 호출하면 중첩 함수 inner를 반환하고 생명 주기를 마감한다. 다시말해 outer 함수의 실행이 종료되면 outer 함수의 실행 컨텍스트는 실행 컨텍스트에서 제거된다.
- 이때 outer 함수의 변수 x와 값 10을 저장하고 있던 outer 변수 또한 생명 주기를 마감한다. 변수 x는 유효하지 않기 때문에 접근할 수 있는 방법은 없어보인다.
- 하지만 ④ innerFunc 호출 결과는 마감된 outer 함수의 지역 변수 x의 값이 출력된다.
- 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 클로저라고 부른다.

**실행 과정**
1. outer 함수가 평가되어 함수 객체가 생성될 때 자신이 정의된 위치에 의해 상위 스코프를 [[Environment]] 내부 슬롯에 저장한다.
2. outer 함수가 호출되어 평가될 때 outer 함수 객체의 [[Environment]] 내부 슬롯에 저장된 전역 렉시컬 환경을 outer 함수 렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 할당한다.
3. outer 함수 실행 단계에서 inner 함수 객체가 생성된다. (inner 함수는 표현식이기 때문에 런타임에 평가된다.)
4. inner 함수는 자신의 [[Environment]] 내부 슬롯에 현재의 실행 컨텍스트의 렉시컬 환경인 outer 함수의 렉시컬 환경을 저장한다.
5. outer 함수의 실행이 종료되면 inner 함수를 반환하고 outer 함수의 생명 주기가 종료된다. 이때 outer 함수의 렉시컬 환경은 inner 함수의 [[Envrionment]] 내부 슬롯에 의해 참조되고 있고 inner 함수는 전역 변수  innerFunc에 의해 참조되고 있으므로 가비지 컬렉션의 대상이 되지 않는다.

## 4. 클로저의 활용

클로저는 상태를 안전하게 변경하고 유지하기 위해 사용한다. 상태가 의도치 않게 변경되지 않도록 상태를 안전하게 은닉하고 특정함수에게만 상태 변경을 허용하기 위해 사용한다.

### 1. 의도치 않은 상태 변화 가능성
```js
// 카운트 상태 변수
let num = 0;

// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태를 1만큼 증가시킨다.
  return ++num;
};

console.log(increase());
console.log(increase());
console.log(increase());

// increase 함수만이 num 변수를 통제할 수 없다.
num += 1;
```

### 2. 상태 유지 불가능
```js
// 카운트 상태 변경 함수
const increase = function () {
  let num = 0;

  // 카운트 상태를 1만큼 증가시킨다.
  return ++num;
};

// 이전 상태를 유지하지 못한다.
console.log(increase());  // 1
console.log(increase());  // 1
console.log(increase());  // 1
```

### 3. 정보 은닉
```js
// 카운트 상태 변경 함수
const increase = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저
  return function () {
    // 카운트 상태를 1만큼 증가시킨다.
    return ++num;
  }
}());

console.log(increase());  // 1
console.log(increase());  // 2
console.log(increase());  // 3
```
- increase 함수만을 통해 상태를 변경할 수 있다.
- 이는 의도치 않게 상태가 변경되지 않도록 정보를 은닉할 수 있다.