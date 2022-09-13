# 문제 상황

- Next.js에서 유저별 Authentication을 어떻게 구현할지 고민
- 단순 유저인지 아닌지 뿐 아니라 유저의 타입별로 접근 권한을 다르게 해야했음
- 로그인 구현은 Cookie & Session 방식으로 했음 (JWT로 바꿀 예정)
- 고려 사항은 Next.js 공식 문서에 기재된 CSR 방식과 SSR 방식, 그리고 Middleware
- CSR 방식은 필요한 컴포넌트에서 항상 custom hook을 호출해야 한다는 문제 (사실 큰 문제는 아님)
- SSR 방식은 위 문제에 더해 getServerSideProps 메소드를 매번 써야해서 서비스 자체가 무거워지는 문제
- 물론 위 방식도 필요할 상황이 있겠지만 원하는 것은 '특정 페이지 진입 시 유저 타입별로 권한 부여를 하는 것'
- Middleware를 사용하면 해당 문제를 해결할 수 있었음

# 해결

```tsx
import type { NextRequest, NextFetchEvent } from "next/server";
import { NextResponse } from "next/server";

import { USER } from "src/common/apis/user";

export const middleware = async (
  request: NextRequest,
  event: NextFetchEvent
) => {
  const baseUrl = "https://localhost:3000/";

  event.waitUntil(
    fetch(`${USER.READ_CURRENT}`, {
      method: "GET",
      headers: request.headers,
      credentials: "include",
    })
      .then((res) => res.json())
      .then((data) => {
        if (data.detail === "Unauthorized") {
          return NextResponse.redirect(new URL("/signin", baseUrl));
        }

        const { session } = request.cookies;

        if (!request.url.includes("/signin") && (!session || data.id === 0)) {
          return NextResponse.redirect(new URL("/signin", baseUrl));
        }

        return NextResponse.next();
      })
      .catch((error) => {
        //error handling...
      })
  );
};
```

# 플러스 알파

- Cookie & Session을 사용하면 sessioid 만료 시점에 만료 에러를 받았을 때 signin page로 redirect (에러 핸들링 ⭐️)
- JWT 방식을 사용했을때는 storage에 저장된 token을 header에 담아 보내면서 만료 에러를 받았을 때 signin page로 redirect
- React Query로 getUser에 대한 캐시 기간을 지정해두고 필요한 컴포넌트에서 호출하는 방식은 어떨까? (하지만 굳이 이렇게 해야할 필요가 있을까?)
- Authentication 관련 네트워크를 최소화하는 방법에는 어떤 게 있을까?

## 나름의 정리

1. Authentication은 크게 두 가지 상황에서 처리해야함, 첫째는 의도적으로 getUser와 같은 API를 만들어서 특정 페이지 혹은 컴포넌트에서 의도적으로 유저인지 아닌지 체크하는 것. 두번째는 특정 API를 호출했을때 유저 권한이 만료되거나 없어서 권한 관련 에러 핸들링을 해야하는 경우
2. Authentication을 체크해야 하는 것은 Cookie & Session 방식이든, JWT 방식이든 같음
3. 그렇다면 해당 쿼리를 처리하는 custom hook을 만들어서 필요한 컴포넌트에서 호출할 수 있음 (Next.js 공식문서)
4. Next.js에서는 해당 방법을 CSR, SSR 두가지로 처리가 가능 (SSR은 방식이 조금 다름)
5. 호출을 최소화 하는 방법에 대해서는 아직 잘 모르겠음
6. 컴포넌트별로가 아니라 유저 타입별로 한번에 Authentication을 해주고 싶다면 middleware라는 옵션이 있음
7. Middleware가 정확히 언제 동작하는지는 좀 더 알아봐야함

# 참고

[https://nextjs.org/docs/authentication](https://nextjs.org/docs/authentication)  
[https://nextjs.org/docs/advanced-features/middleware](https://nextjs.org/docs/advanced-features/middleware)  
[https://velog.io/@real-bird/Next.js-middleware](https://velog.io/@real-bird/Next.js-middleware)
