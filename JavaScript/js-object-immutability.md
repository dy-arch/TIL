# 객체와 변경불가성(Immutability)

작성시간: 2022년 3월 1일 오후 4:35

- Immutability(변경불가성)는 객체가 생성된 이후 그 상태를 변경할 수 없는 디자인 패턴을 의미한다. Immutability는 함수형 프로그래밍의 핵심 원리이다.
- 객체는 참조(reference) 형태로 전달하고 전달 받는다. 객체가 참조를 통해 공유되어 있다면 그 상태가 언제든지 변경될 수 있기 때문에 문제가 될 가능성이 크다.
- 이에 대한 해결 방법은 비용이 조금 필요하지만 객체를 불변객체로 만들어 프로퍼티의 변경을 방지하고 변경이 필요한 경우에는 참조가 아닌 객체의 방어적 복사(defensive copy)를 통해 새로운 객체 생성한 후 변경한다.
- 또는 **Observer 패턴**으로 객체의 변경에 대처할 수도 있다.

### 1. immutable value vs mutable value

JavaScript의 원시 타입(primitive data type)은 변경 불가능한 값(immutable value)이다.

- Boolean
- null
- undefined
- Number
- String
- Symbol (New in ECMAScirpt6)

원시 타입 이외의 모든 값은 객체(Object) 타입이며 객체 타입은 변경 가능한 값(mutable value)이다. 즉, 객체는 새로운 값을 다시 만들 필요없이 직접 변경이 가능하다는 것.

JavaScript의 문자열은 변경 불가능한 값(immutable value)이다. 이런 값을 **원시값(primitive values)**이라고 한다. 이는 메모리 영역에서 변경이 불가능하다는 뜻. (재할당은 가능하다)

```jsx
let str = 'Hello'; //1
str = 'world';     //2
```

1. 첫 번째 구문이 실행되면 메모리에 문자열 ‘Hello’가 생성 
2. 식별자 str은 메모리에 생성된 문자열 ‘Hello’의 메모리 주소를 가리킨다.
3. 두 번째 구문이 실행되면 생성된 문자열 ‘Hello’를 수정하는 것이 아니라 새로운 문자열 ‘world’를 메모리에 생성한다.
4. str은 ‘world’의 메모리주소를 가리킨다.

→ 문자열 ‘Hello’와 ‘world’는 모두 메모리에 존재하고 있다. 그저 str이 가리키는 메모리 주소가 바뀌었을 뿐이다.

**이는 String의 메소드에서도 나타난다.**

```jsx
const statement = 'I am an immutable value'; // string은 immutable value

const otherStr = statement.slice(8, 17);

console.log(otherStr);   // 'immutable'
console.log(statement);  // 'I am an immutable value'
```

- 문자열은 원시값이다. 즉, 변경 불가능한 immutable value이다.
- 때문에 slice는 새로운 문자열을 생성하여 연산한 다음 반환하는 것이다.

**하지만 객체(배열, 함수, {})는?**

```jsx
const arr = [];
console.log(arr.length); // 0

const v2 = arr.push(2);    // arr.push()는 메소드 실행 후 arr의 length를 반환
console.log(arr.length); // 1
```

배열(객체)의 메소드 push()는 직접 대상 배열을 변경한다. 그 이유는 배열은 객체이고 객체는 immutable value가 아닌 변경 가능한 값이기 때문이다.

```jsx
const foo = {
	val: 10,
}

const bar = foo;
console.log(foo.val, bar.val); // 10 10
console.log(foo === bar);      // true

bar.val = 20;
console.log(foo.val, bar.val); // 20 20
console.log(foo === bar);      // true
```

위의 코드와 같이 foo의 객체를 변경했지만 의도치 않게 bar의 객체도 변경이 되어버렸다. 때문에 추가대응이 필요하다.