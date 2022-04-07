# Vite

## 번들링의 등장과 문제
브라우저에서 ESM(ES Modules)을 지원하기 전까지는 JavaScript 모듈화를 네이티브 레벨에서 진행할 수 없었다. 때문에 개발자들은 모듈화된 소스 코드를 브라우저에서 실행 가능한 파일로 묶어주는 "번들링(Bundiling)"이 등장하게 되었다. 대표적으로 Webpack, Rollup, Parcel과 같은 도구가 있다.

하지만 프로젝트 규모가 커지면서 Webpack과 같은 JavaScript 기반의 툴은 병목 현상이 발생하고는 했다. 길어진 개발 서버 실행 시간, 빌드 시간의 경우가 그 예이다.

## 그럼 Vite는?
- Vite 또한 번들러이다. 
- 하지만 Webpack 보다 10배 빠르고 Vue.js의 개발자 Evan You 가 만들었다. 
- Esbuild 위에 구축된 찻대 프론트엔드 빌드 도구
- 브라우저의 ES 모듈을 사용할 수 있기 때문에 번들할 필요가 없기 때문에 개발 서버 실행이 빠르다.

## 시작해보자
프로젝트 설정을 위한 CLI가 있다.
```bash
# npm 6.x react
npm create vite@latest project-name --template react

# npm 7.x react typescript
npm create vite@latest project-name -- --template react-ts
```