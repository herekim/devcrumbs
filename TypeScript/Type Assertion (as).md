# 문제 상황

버튼 클릭 시 `event.target.name` 처리를 해야했는데 에러가 났음

```js
  // MouseEvent<HTMLButtonElement>
  // - React에서 제공하는 MouseEvent Type
  // - Generic으로 Element Type
  const handleAs = (event: MouseEvent<HTMLButtonElement>): void => {
    event.preventDefault();

    // event.target의 프로토타입 체인에 HTMLButtonElement.prototype이 있는지 체크
    if (!(event.target instanceof HTMLButtonElement)) return;

    // event.target의 타입을 HTMLButtonElement로 단언
    const eventTarget = event.target as HTMLButtonElement;

    // event.target.name의 타입을 PaymentMethodType으로 단언
    setState(eventTarget.name as TestType);
  };
```

## as

- as는 타입 단언이다
- 내가 TypeScript보다 더 정확히 타입 정보를 알고 있을 때 사용한다

## 그 밖의 알게된 것

- React에서는 MouseEvent 타입을 제공한다
- `instanceof` 는 좌항의 프로토타입 체인에 우항의 프로토타입이 있는지 확인한다
