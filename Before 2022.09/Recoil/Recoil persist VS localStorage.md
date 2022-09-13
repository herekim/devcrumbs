220707 Thu.

- 둘 다 localStorage에 저장
- Next.js에서 사용 시 Recoil은 아래 코드처럼 Component를 감싸기 때문에 전역 저장된 데이터를 hook 내부에서 바로 사용 가능(localStorage는 Next.js가 기본적으로 SSR이기 때문에 따로 설정이 필요함)
  ```tsx
  <RecoilRoot>
    <CssBaseline />
    <Component {...pageProps} />
  </RecoilRoot>
  ```
