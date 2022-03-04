# Next.js + TS lint

[[Next.js + TS] 초기세팅 - (2) ESLint, Prettier](https://abangpa1ace.tistory.com/194)

ESLint는 JavaScript, TypeScript의 코딩 스타일 도구. JS코드가 ECMAScript Specification에 부합하는지 검사하는 툴

## eslint + prettier 설정

1. 일단 next를 typescript

```bash
$npx create-next-app --typescript
```

- 위 링크에는 eslint를 설치해주는데 할 필요없다. 기본적으로 설치되어있다.

---

1. eslint 초기화

```bash
$npx eslint --init
```

```bash
? How would you like to use ESLint?
    ❯ To check syntax and find problems
? What type of modules does your project use?
    ❯ (import/export)
? Which framework does your project use?
    ❯ react
? Does your project use TypeScript?
    ❯ yes
? Where does your code run?
    ❯ browser, node
? What format do you want your config file to be in?
    ❯ JSON
```