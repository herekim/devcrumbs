# 사용 상황

- Next.js에서 JWT를 담는 공통 헤더가 필요했음
- 방식은 axios의 defaults, interceptors 메소드 두 가지 중 interceptors 선택
- localStorage에 토큰을 저장

# 문제 상황

- 문제는 Next.js에서 프리렌더링 시 localStorage에 접근할 수 없는 것

# 해결

- recoil persist로 저장 (결국은 localStorage)
- `_app.tsx` 파일에서 AxiosContainer 컴포넌트로 감싸줌

  ```tsx
  function MyApp({ Component, pageProps }: AppProps) {
    return (
      <SessionProvider session={pageProps.session} basePath="...">
        <QueryClientProvider client={queryClient}>
          <Hydrate state={pageProps.dehydratedState}>
            <RecoilRoot>
              <CssBaseline />
              <AxiosContainer>
                <Component {...pageProps} />
              </AxiosContainer>
            </RecoilRoot>
          </Hydrate>
        </QueryClientProvider>
      </SessionProvider>
    )
  }
  ```

- AxiosContainer 컴포넌트에서는 useAxiosInstance를 한번 호출

  ```tsx
  const AxiosContainer = ({ children }: ReactNodeProps) => {
    useAxiosInstance()

    return <>{children}</>
  }
  ```

- useAxiosInstance는 RecoilRoot 컴포넌트 내부에서만 사용되므로 안전하게 token을 localStorage에 위치한 recoil persist에서 꺼내올 수 있음

  ```tsx
  const useAxiosInstance = () => {
    const token = useRecoilValue<TokenInterface>(tokenState)

    axios.interceptors.request.use(async (config) => {
      return {
        ...config,
        headers: {
          ...config.headers,
          Authorization: `Bearer ${token.accessToken}`,
        },
      }
    })
  }
  ```
