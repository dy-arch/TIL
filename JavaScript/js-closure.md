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