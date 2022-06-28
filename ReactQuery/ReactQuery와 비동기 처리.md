# 문제 상황

- nextjs + react-query 환경에서 fullcalendar의 이벤트 생성, 삭제 구현.
- 생성, 삭제 모달에서 확인을 누르고 동시 반영이 안됨.
- react-query 의 비동기 처리에서 문제점 발견.

```js
const handleCreateSlot = () => {
  const createSlotOption = {
    /// options
  };
  slotCreateMutation.mutate(createSlotOption);
};
```

```js
const handleDeleteSlot = (id: number) => {
  slotDeleteMutation.mutate(id);
};
```

# 해결 방법

- mutate -> mutateAsync 로 interface 변경해서 Promise를 리턴시킴
- 해당 함수에 async await 걸어서 비동기 처리
- useQuery의 refetch interface 를 사용해 refetch

```js
const handleCreateSlot = async () => {
  const createSlotOption = {
    /// options
  };
  await slotCreateMutation.mutateAsync(createSlotOption);
  await slotReadList.refetch();
};
```

```js
const handleDeleteSlot = async (id: number) => {
  await slotDeleteMutation.mutateAsync(id);
  await slotReadList.refetch();
};
```

# 부스러기 지식

- mutateAsync interface (공식문서 참조)
  Similar to mutate but returns a promise which can be awaited.
  ```
  mutateAsync: (variables: TVariables, { onSuccess, onSettled, onError }) => Promise<TData>
  ```
- refetch interface (소스 코드 참조)
  ```ts
  export interface QueryObserverBaseResult<TData = unknown, TError = unknown> {
    data: TData | undefined;
    dataUpdatedAt: number;
    error: TError | null;
    errorUpdatedAt: number;
    failureCount: number;
    isError: boolean;
    isFetched: boolean;
    isFetchedAfterMount: boolean;
    isFetching: boolean;
    isIdle: boolean;
    isLoading: boolean;
    isLoadingError: boolean;
    isPlaceholderData: boolean;
    isPreviousData: boolean;
    isRefetchError: boolean;
    isRefetching: boolean;
    isStale: boolean;
    isSuccess: boolean;
    //refetch
    refetch: <TPageData>(
      options?: RefetchOptions & RefetchQueryFilters<TPageData>
    ) => Promise<QueryObserverResult<TData, TError>>;
    remove: () => void;
    status: QueryStatus;
  }
  ```
