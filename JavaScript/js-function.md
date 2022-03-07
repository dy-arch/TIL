# 함수

💡 **함수란**

어떤 작업을 수행하기 위해 필요한 문(statement)들의 집합을 정의한 코드 블록이다. 함수는 이름과 매개변수를 가지며 필요한 때에 호출하여 코드 블록에 담긴 문들을 일괄적으로 실행할 수 있다.

## 1. 함수 정의

### 1-1 함수 선언문

함수 선언문(Function declaration)방식으로 정의한 함수는 **function** 키워드와 이하의 내용으로 구성.

1. 함수명
    
    함수 선언문의 경우, 함수명은 생략할 수 없다. 함수명은 함수 몸체에서 자신을 재귀적(recursive) 호출하거나 자바스크립트 디버거가 해당 함수를 구분할 수 있는 식별자이다.
    
2. 매개변수 목록
    
    0개 이상의 목록으로 괄호로 감싸고 콤마로 분리한다. 다른 언어와의 차이점은 매개변수의 타입을 기술하지 않는 다는 것.
    
3. 함수 몸체
    
    함수가 호출되었을 때 실행되는 문들의 집합. 중괄호({ })로 문들을 감싸고 **return** 문으로 결과값을 반환할 수 있다.
    

```js
// 함수 선언문
function square(number) {
	return number * number;
}
```

### 1-2 함수 표현식

자바스크립트의 함수는 일급 객체이므로 아래와 같은 특징이 있다.

1. 무명의 리터럴로 표현 가능.
2. 변수나 자료 구조(객체, 배열...)에 저장할 수 있다.
3. 함수의 파라미터로 전달할 수 있다.
4. 반환값(return value)으로 사용할 수 있다.

위의 특성을 이용하여 함수 리터럴 방식으로 함수를 정의하고 변수에 할당할 수 있다.

이러한 방식을 함수 표현식(Function expression)이라 한다.

```js
//함수 표현식
const square = function(number) {
	return number * number;
}
```

함수 표현식 방식으로 정의한 함수는 함수명을 생략할 수 있다. 이러한 함수를 익명 함수**(anonymous function)** 이라 한다. 함수 표현식에서는 함수명을 생략하는 것이 일반적이다.

```js
//기명 함수 표현식
const foo = function multiply(a, b) {
	return a * b;
};

// 익명 함수 표현식(anonymous function expression)
const bar = function(a, b) {
  return a * b;
};

console.log(foo(10, 5)); // 50
console.log(multiply(10, 5)); // Uncaught ReferenceError: multiply is not defined
```

함수는 일급객체이기 때문에 변수에 할당할 수 있는데, 이 변수는 함수명이 아니라 할당된 함수를 가리키는 참조값을 저장한다.

호출시 함수명이 아닌 함수를 가리키는 변수명을 사용하여야 한다.

## 2. 함수 호이스팅

```js
const res = square(5);

function square(number) {
	retunr number * number;
}
```

- 위 코드에서 함수 선언문으로 정의되기 이전에 함수 호출이 가능하다.
- 함수 선언의 위치와는 상관없이 함수 호출이 가능한데 이것을 함수 호이스팅(Function Hoisting)이라 한다.
- **자바스크립트는 ES6의 let, const를 포함하여 모든 선언(var, let, const, function, function*, class)을 호이스팅(Hoisting)한다.
→ let, const도 호이스팅?? >>> 이 부분에 관해서는 TDZ를 보고 다른 페이지에서 기술하자**


💡 **호이스팅이란** 

var 선언문이나 function 선언문이 해당 Scope의 선두로 옮겨진 것처럼 동작하는 특성을 말한다. 즉, 자바스크립트는 모든 선언문(var, let, const, function, function*, class)이 선언되기 이전에 참조 가능하다.
함수 선언문으로 정의된 함수는 자바스크립트 엔진이 스크립트가 로딩되는 시점에 바로 초기화하고 이를 VO(variable object)에 저장한다. 즉, **함수 선언, 초기화, 할당이 한번에 이루어진다.** 그렇기 때문에 함수 선언의 위치와는 상관없이 소스 내 어느 곳에서든지 호출이 가능하다.



**다음은 함수 표현식으로 함수를 정의한 경우이다**

```js
const res = square(5); // TypeError: square is not a function

var square = function(number) {
	return number *number;
}
```

- 이 경우 함수 호이스팅이 아닌 **변수 호이스팅**이 발생한다.


🤔 **변수든 함수든 호이스팅이 일어났다면 square 함수는 작동해야 하지 않을까?**

변수 호이스팅과 함수 호이스팅은 다른 방식으로 발생한다.
변수 호이스팅은  변수 생성 및 초기화, 할당이 분리되어 진행된다. 위 코드에서 square는 변수로 호이스팅 되어 생성과 초기화만 일어났을 것이다. 첫 번째 줄은 undefined로 초기화된 square이고 두 3번째 줄부터 square에 함수가 할당되었기 때문에 오류가 나는 것이다.


> JavaScript: The Good Parts의 저자이며 자바스크립트의 군위자인 더글러스 크락포드(Douglas Crockford)는 이와 같은 문제 때문에 함수 표현식만을 사용할 것을 권고하고 있다. 함수 호이스팅이 함수 호출 전 반드시 함수를 선언하여야 한다는 규칙을 무시하므로 코드의 구조를 엉성하게 만들 수 있다고 지적한다. <br>
또한 함수 선언문으로 함수를 정의하면 사용하기에 쉽지만 대규모 애플리케이션을 개발하는 경우 인터프리터가 너무 많은 코드를 변수 객체(VO)에 저장하므로 애플리케이션의 응답속도는 현저히 떨어질 수 있으므로 주의해야 할 필요가 있다.

## 3. First-class object (일급 객체)


☝ **다음 조건을 만족하면 일급 객체로 간주한다.**

1. 무명의 리터럴로 표현이 가능하다.

```js
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};
```

2. 변수나 자료 구조(객체, 배열 등)에 저장할 수 있다.

```js
const predicates = { increase, decrease };
```

3. 반환값으로 사용할 수 있다.

```js
function makeCounter(predicate) {
  const num = 0;

  return function () {
    num = predicate(num);
    return num;
  };
}
```

4. 함수의 매개변수에 전달할 수 있다.

```js
const increaser = makeCounter(predicates.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2
```

## 매개변수(Parameter, 인자)
함수의 작업 실행을 위해 추가적인 정보가 필요할 경우, 매개변수를 지정. 매개변수는 함수 내에서 변수와 동일하게 동작.

### 4-1. 매개변수(parameter, 인자) vs 인수(argument)

- 매개변수는 함수 내에서 변수와 동일하게 **메모리 공간을 확보**하며 함수에 전달한 인수는 매개변수에 할당.
- 만약 인수를 전달하지 않으면 매개변수는 undefined로 초기화.

```js
const foo = function (p1, p2) {
  console.log(p1, p2);
};

foo(1);
```

### 4-2 Call-by-value

- 원시타입 인수는 Call-by-value(값에 의한 호출)로 동작. 
- 즉, 함수 호출 시 원시 타입 인수를 함수에 전달할 때 매개변수에 값을 복사하여 함수로 전달.
- 때문에 함수 내에서 매개변수를 통해 값이 변경되어도 전달한 매개변소의 원시 타입 값은 변경되지 않음

  ```js
  function foo(a) {
    a += 1;
    return a
  }

  let x = 0;
  console.log(foo(x)); // 1
  console.log(x) // 0
  ```

  ### 4-3 Call-by-reference

- 객체형(참조형) 인수는 **Call-by-reference**(참조에 의한 호출)로 동작.
- 즉, 함수 호출 시 참조 타입 인수를 함수에 매개변수로 전달할 때 매개변수의 값이 복사되지 않고 참조값이 전달.
- 때문에 함수 내에서 매개변수의 참조값이 이용되어 객체의 값을 변경했을 때, 전달되어진 참조형의 값도 같이 변경.

```js
function changeVal(a, obj) {
  a += 100;
  obj.name = 'Kim';
  obj.gender = 'female';
}

let num = 100;
const obj = {
  name: 'Lee',
  gender: 'male',
}

console.log(num) // 100;
console.log(obj) // Object { name: 'Lee', gender: 'male' }

changeVal(num, obj)

console.log(num) // 100;
console.log(obj) // Object { name: 'Kim', gender: 'female' }