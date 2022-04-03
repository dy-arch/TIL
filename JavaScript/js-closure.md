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