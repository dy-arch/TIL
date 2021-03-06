※ 공식 문서와 하단의 사이트를 참조하여 작성함
# SSR and SSG 
## 시작
SEO를 위해 Next.js 공식문서를 보던 중 SSG라는 개념이 등장했는 데, 나의 얄팍한 영어 실력과 번역기의 매끄럽지 못한 설명으로는 이해가 힘들어서 공부하고자 한다.

## 본문

### **우선 SSR**
SSR은 말 그대로 Server Side Rendering '서버 단의 렌더링'을 말한다. <br>
웹사이트에 접속하면 서버측에서 HTML을 렌더링하고 완성된 HTML파일을 유저의 브라우저로 전달을 하게 된다. 이후 이 HTML을 파싱하고 DOM tree를 생성하고 렌더링하게 되는 것이다. <br>

### **SSR은 페이지가 바뀌면 화면이 깜빡이는게 아닌가?**
Next.js를 실행해서 페이지를 이동해보면 기존의 React처럼 매끄러운 화면 이동이 일어난다. 그 이유는 Next.js가 페이지 이동마다 HTML파일을 서버에서 렌더링해서 주는 것이 아니고 첫 로딩 이후로는 CSR방식을 사용하기 때문이다. 즉 CSR방식의 기존 React에서 첫 로딩이 느리다는 것과 SEO의 단점만을 해결하기 위해 SSR의 장점만을 CSR에 합친 것이다. 앞으로는 SSR = MPA 라는 생각을 지워버리자.

### 그럼 SSG는 뭘까
공식문서에서는 다음과 같이 설명하고 있다.
- 정적 사이트 생성을 하게 되면 HTML이 서버에서 생성되지만 SSR과 달리 런타임시 서버에 없다.
- 대신 컨텐츠는 어플리케이션이 배포, HTML이 CDN에 저장되고 각 요청에 재사용될 때 빌드시에 한 번 생성된다.

Next.js 공식문서의 FOUNDATIONS 탭을 보았을 때는 무슨 말인지 이해하지 못했다. ~~(물론 나의 영어 실력도 한 몫 했다.)~~ <br>
하지만 다행히도 Pre-rendering and Data Fetching 부분에서 자세히 설명하고 있다.

Pre-rendering에는 두 가지의 형태가 있다.
- SSR : 서버 사이드 렌더링은 각 요청마다 HTML을 사전 렌더링하는 방법.
- SSG : 정적 렌더링은 빌드시에 HTML을 생성하고 요청마다 미리 생성된 HTML을 받는 것.

공식 문서에서 SSR은 항상 최신 데이터를 유지해야할 때, 사용을 권하고 있다. <br>
SSR 방식은 유저의 요청이 들어왔을 때, 서버에서 필요한 데이터를 받은 뒤 HTML을 업데이트 한 뒤에 클라이언트로 보내기 때문에 속도가 느려질 수도 있다는 언급도 있다.

SSG는 위에서 빌드시에 HTML을 미리 생성한다고 설명했다. 즉 데이터를 fetch하여 HTML을 데이터로 채운 뒤 HTML을 미리 생성해둔다는 말이 된다. 매번 새로운 글을 보여주어야 하는 SNS나 커뮤니티의 성질을 갖는 페이지라면 클라이언트에서 과거의 데이터만 렌더링되거나, 과거 데이터가 렌더링 되고 난 후 새로운 데이터가 갱신되는 찝찝한 일이 발생할 수 있다. 때문에 공식문서에서는 데이터를 받지만 정적인 블로그와 같은 페이지에 권장하고 있다.<br>
또 글을 읽다 보면 **"데이터가 있든 없든 Static Generation"** 이라는 제목을 가진 섹션이 있다. 이 섹션에서는 

> 우리가 만든 모든 페이지에서 외부 데이터를 fetch하지 않는다. 이 페이지들은 프로덕션을 위해 빌드될 때 자동으로 정적으로 생성됩니다.

라고 언급 하는데, SSG관련 메소드를 사용하지 않아도 default로 SSG로 빌드 되는 듯 하다.

### 빌드 이후 요청 데이터가 변화가 있다면?
위에서 설명했듯이 SSG의 방식은 빌드시에만 데이터를 받아 html을 미리 생성해두는 것이다. 그렇다면 프로덕션에서 빌드 이후 데이터가 변한다면 서버측에서 다시 빌드해야 할까?

이에 대한 답은 공식문서에 있다. (~~영어가 힘들어 공식문서를 멀리 했더니 고생만 했다. 공식문서를 가까이 하자..~~) <br>
SSG를 위한 데이터 요청 API로 **getStaticProps**가 있다. 해당 API를 사용하여 데이터를 요청하게 되면 빌드시에 데이터를 요청해 Pre-Rendering하는 데 사용된다. 사용 방법은 아래와 같다.
```tsx
// page/index.tsx

import axios from "axios";
import type { GetStaticProps } from "next";
import { Landing } from "containers/Landing";

interface landingData {
  data: {
    ...
  };
}

function Home({ data }: landingData) {
  return <Landing article={data} />;
}

export const getStaticProps: GetStaticProps = async (context) => {
  const res = await axios.get("/todays/article");
  return { props: { 
      data: res.data 
    } 
  };
};

export default Home;
``` 
**빌드**

![isr 적용전](img/isr_before.png)

공식 문서를 살펴보게 되면 아래와 같이 설명하고 있다.
> getStaticProps 함수는 'props', 'redirect', 'notFound'와 선택적 'revalidate' 속성을 포함하는 객체를 반환해야 한다.

여기서 우리가 주목해야 하는 것은 'revalidate' 속성이다. 'revalidate' 속성은 페이지 재생성이 발생할 수 있는 시간이다. <br>
나의 의문에 대한 해결책을 Next는 Incremental Static Regeneration(증분 정적 재생)이라고 한다. ISR은 'revalidate' 속성에 기입한 시간(초)마다 데이터의 최신 여부를 검사하고 만약 데이터의 변화가 있다면 업데이트된 데이터롤 페이지를 다시 생성한다. 아래의 코드를 보자.

```tsx
// page/index.tsx

import axios from "axios";
import type { GetStaticProps } from "next";
import { Landing } from "containers/Landing";

interface landingData {
  data: {
    ...
  };
}

function Home({ data }: landingData) {
  return <Landing article={data} />;
}

export const getStaticProps: GetStaticProps = async (context) => {
  const res = await axios.get("/todays/article");
  return { props: { 
      data: res.data 
    },
    revalidate: 10,
  };
};

export default Home;
```
1. 빌드 시 사전 렌더링된 페이지에 대한 요청이 이루어지면 처음에는 캐시된 페이지가 표시된다.
2. 초기 요청 후와 10초 전에는 캐시되어 있는 페이지가 표시된다.
3. 10초 후 데이터의 변화를 판단해 변화되었다면 페이지를 재생성하고 그렇지 않다면 아무런 변화가 없다.

**빌드**

![isr 적용후](img/isr_after.png)
- 빌드 후 (ISR: 10 Seconds) 부분이 추가되었음을 알 수 있다.

> 참고 <br>
> [longroadhome.log - 벨로그](https://velog.io/@longroadhome/FE-SSRServer-Side-Rendering-%EA%B7%B8%EB%A6%AC%EA%B3%A0-SSGStatic-Site-Generation-feat.-NEXT%EB%A5%BC-%EC%A4%91%EC%8B%AC%EC%9C%BC%EB%A1%9C) <br>
> [Parkgang Dev Log](https://parkgang.github.io/next.js/lets-properly-understand-and-use-the-ssg-of-next.js/)<br>
> [thisiscoke Tistory](https://thisiscoke.tistory.com/entry/Nextjs-%EA%B3%B5%EC%8B%9D%EB%AC%B8%EC%84%9C-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B01-%ED%95%98%EC%9D%B4%EB%B8%8C%EB%A6%AC%EB%93%9C-%EC%95%B1-SSR-SSG-CSR)<br>
> [Next.js 공식](https://nextjs.org/docs)
