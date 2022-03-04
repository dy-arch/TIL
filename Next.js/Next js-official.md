# Next.js 공식문서

# 1. JavaScript에서 리액트까지

- 이 섹션에서는 리액트가 등장한 이유, 리액트의 간단한 사용법(?) 설명한다.
- DOM에 h1 태그를 만드는 과정을 코드로 보여주며 태그 하나를 만드는 데 사용되는 코드를 설명하고 있음
- 코드
    
    ```jsx
    <script type="text/javascript">
      const app = document.getElementById('app')
      const header = document.createElement('h1')
      const headerContent = document.createTextNode('Develop. Preview. Ship. 🚀')
      header.appendChild(headerContent)
      app.appendChild(header)
    </script>
    ```
    

# 2. Next.js 작동 방식

## Next.js에 적용되는 방법

- 개발환경에서 개발자와 그들의 빌드 경험을 위해 최적화 한다. 개발자 경험 향상을 목표로 TypeScript, ESLint 통합, 빠른 새로고침 등 이와 같은 기능을 제공한다. 코드를 변환하여 성능과 접근성을 높이는 것을 목표로 한다.
- 프로덕션 환경에서는 end-user, 그들의 application 사용 경험을 위해 최적화한다.
- 각각의 환경은 고려사항과 목료가 다르기 때문에 개발에서 프로덕션까지 이동하는데 많은 요구가 있다. 예를 들어 컴파인, 번들링, 축소 및 코드 분할이 필요하다.

## Next.js 컴파일러

- Next.js는 코드 변환의 대부분과 기본 인프라를 처리하여 프로덕션까지 쉽게 개발할 수 있도록 돕는다.

## 컴파일이란

- 개발자는 JSX, TS 와 같은 확장자로 개발한다. 이 것은 개발자의 효율성을 높이지만, 브라우저가 이해가능하도록 컴파일할 필요가 있다.
- 컴파일은 하나의 언어를 다른 언어 또는 다른 버전으로 출력하는 과정을 가리킨다.
- Next.js에서 컴파일은 개발환경에서 코드를 작성할 때, 그리고 application을 프로덕션으로 준비하는 빌드 단계에서 일부 발생한다.

## Minifying

- 말 그대로 축소다. 개발시에는 가독성에 최적화된 코드를 작성한다. 주석, 공백, 들여쓰기, 줄바꿈 등 코드를 실행하는데 필요하지 않은 부분을 줄여 최적화하는 과정을 말한다.
- 파일 크기를 줄여 프로그램의 성능을 향상시키는 것

## Bundling

- 개발시에는 기능, 성격 등 모듈로 나누어 작업을 한다. 만약 이러한 구조가 웹에 그대로 실행된다면 파일이 많기 때문에 요청이 많아지고 웹이 복잡해진다.
- 이러한 요청 수를 줄이는 것을 목표로 잘게 나누어진 파일, 즉 모듈들을 브라우저에 최적화된 번들로 병합하는 과정을 말한다.

## Build Time

- 빌드 시간은 프로덕션용 코드를 준비하는 일련의 단계를 일컫는다.
- 애플리케이션을 빌드하면 Next.js가 코드를 프로덕션에 맞게 최적화하여 서버에 배포하게 된다.
    - 정적으로 생성된 페이지용 HTML 파일
    - 서버에서 페이지를 렌더링 하기 위한 JavaScript 코드
    - JavaScript code for making pages interactive on the client
    - CSS 파일

## Runtime

- 어플리케이션이 빌드, 배포된 후 실행되는 기간을 말한다.

## Rendering

- React에서 작성한 코드를 HTML로 표현하는 작업을 말한다.
- Next.js에서는 서버렌더링, 정적 사이트 생성, CSR의 3가지 방법을 사용할 수 있다.’

## Pre-Rendering

- SSR, SSG는 Pre-Rendering이라고 한다. 왜냐하면 클라이언트로 전송되기 전에 외부 데이터를 fetch하고 React components를 HTML로 변환하기 때문이다.