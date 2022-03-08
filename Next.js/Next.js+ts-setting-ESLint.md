# Next.js 초기 세팅

[[Next.js + TS] 초기세팅 - (2) ESLint, Prettier](https://abangpa1ace.tistory.com/194)

ESLint는 JavaScript, TypeScript의 코딩 스타일 도구. JS코드가 ECMAScript Specification에 부합하는지 검사하는 툴

## eslint + prettier 설정

### 1. Next.js typescript로 세팅

```bash
npx create-next-app --typescript
```

- 본문에는 eslint를 설치해주는데 할 필요없다. 기본적으로 설치되어있다.

### 2. eslint 초기화

```bash
npx eslint --init
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
- 프로젝트에 맞게 설정하기
  
### 3. styled-components 설치하기
```bash
npm i styled-components
```
TypeScript 사용 시 추가 install
```bash
npm i @types/styled-components
```
- TypeScript에서 리액트 라이브러리에 대한 type 정의가 되어있지 않기 때문.
- type을 정의한 라이브러리 설치가 필요.

#### 1. _document.tsx
```tsx
// _document.tsx
import React from "react";
import Document, { Html, Head, Main, NextScript } from "next/document";
import { ServerStyleSheet } from "styled-components";
class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const sheet = new ServerStyleSheet();
    const originalRenderPage = ctx.renderPage;

    try {
      ctx.renderPage = () =>
        originalRenderPage({
          enhanceApp: (App) => (props) =>
            sheet.collectStyles(<App {...props} />),
        });

      const initialProps = await Document.getInitialProps(ctx);
      return {
        ...initialProps,
        styles: (
          <>
            {initialProps.styles}
            {sheet.getStyleElement()}
          </>
        ),
      };
    } finally {
      sheet.seal();
    }
  }

  render() {
    return (
      <Html>
        <Head />
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

export default MyDocument;
```

#### 2. babel 설정
- Next.js는 최초 SSR 이후 내부 라우팅을 통해 페이지가 이동되면서 CSR을 하게 된다.
- 서버에서 생성하는 해시값과 브라우저에서 생성하는 해시값이 서로 달라서 문제가 발생.

이를 위해 바벨 플러그인 패키지 설치가 필요하다.

```bash
npm i -D babel-plugin-styled-components
```
그리고 root path에 .babelrc 파일을 생성해 공식문서를 참고하여 아래와 같이 작성.
```json
{
	"presets": ["next/babel"],
	"plugins": [
		[
			"styled-components",
			{
				"ssr": true,
			}
		]
	]
}
```

> 참고 <br>
> [[Next.js + TS] 초기세팅 - (2) ESLint, Prettier](https://abangpa1ace.tistory.com/194) <br>
> [next.js 에 typescript, storybook 설정하기](https://carpediem9911.tistory.com/44) <br>