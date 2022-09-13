# 그래서 DECLARATIVE가 뭔데?

## How와 What으로 코드를 바라보기

- How를 최소화, What을 극대화

### How와 What의 분리 예제

- 명령형의 자바스크립트 코드

  - 분리가 안되어 있음

  ```js
  // What
  const greeting = `Hello, JavaScript!`

  // How?
  const $p = document.createElement('p')
  $p.innerHTML = greeting

  const $app = ducument.getElementById('app')
  $app.appendChild($p)
  ```

- 선언형의 자바스크립트 코드

  ```js
  // How
  function p(text) {
    const $p = document.createElement('p')
    $p.innerHTML = text
    return $p
  }
  function render(element) {
    const $app = ducument.getElementById('app')
    $app.appendChild(element)
  }

  // What
  const greeting = `Hello, JavaScript!`
  render(p(greeting))
  ```

### 상태관리의 How와 What의 분리 예제 (리액트의 상태관리 개념 차용)

- How - 어떻게 데이터를 바꿀까? 언제 데이터를 바꿀까?
- What - 무슨 데이터를 바꿀까? 어디에 값을 보여줄까?
- "상태를 뷰에 반영한다"

- 명령형 자바스크립트 코드 (How와 What이 섞여있음)

```js
// What?
let count = 2020
const $button = document.getElementById('button)

// How?
$button.addEventListener('click', () = {
  // What?
  const $const = document.getElementById('count')
  count = count + 1
  $count.innerHTML = count
})

```

- 선언형 자바스크립트 코드

```js
// ui.js
// How
function render(initialState, createElement) {
  function renderToApp(element) {
    const $app = document.getElementById('app')
    $app.innerHTML = ''
    $app.appendChild(element)
  }

  function setState(state) {
    renderToApp(createElement(state, setState))
  }

  setState(initialState)
}
```

```js
// How의 구현체인 div, p, render는 숨기고
// initialState와 createElement 함수를 넘겨줌
// React의 방식
import { div, p, render } from './helpers/ui'

// What
render({ count: 2020 }, (state, setState) =>
  div(
    //
    div(
      //
      p(`Hello, JavaScript!`),
      p(`Welcome to "Javascript is Everywhere ${state.count}"`),
    ),
    div(
      p(`My name is Heeyeol`),
      button('plus', () => {
        setState({
          count: state.count + 1,
        })
      }),
    ),
  ),
)
```

## 관심사의 분리

- 기존에는 역할에 따라 HTML/CSS/JS 를 분리
- 자바스크립트가 마크업 언어와 너무 달랐기 때문
- 하지만 Declarative UI 패턴을 통해 HTML, CSS, JS가 자연스럽게 섞일 수 있게 됨 (JS를 마치 마크업 언어를 짜듯)
- 그때부터 역할이 아닌 관심사를 기반으로 컴포넌트를 쪼갬
- 컴포넌트를 관심사에 따라 잘 쪼개는 방법?
- 컴포넌트를 잘 쪼개는 절대적인 기준은 없지만 상대적인 기준은 있음
- 우리가 표현하고자 하는 것은 '트리 구조'가 아니라 '정보'이기 때문
- 상대적인 기준 -> 표현하려는 '정보'에 집중

## 정보는 원래 복잡하다

- 뷰 일관성을 유지하기 위해 데이터를 잘 다뤄야 함
- 여기서 나온 개념이 '단일 진실 공급원(Single source of truth)'
- 상태를 여러개가 아니라 하나로 관리하는 것 중요한 것은 어떻게?
- Redux, MobX가 그래서 나온 애들
- 데이터가 그래프의 형태, 규칙을 잘 만들면 쉽게 자동화 할 수 있음
- 모델 타입 정의 -> 쉬운 정규화 -> 쉬운 상태관리 : GraphQL
- React가 UI에 대한 How를 가져갔던 것처럼, GraphQL은 데이터 처리에 대한 How를 가져간다.

## 선언적으로 데이터 요청하기

- 컴포넌트는 데이터를 필요로 함
- 우리가 그리고자 하는 데이터는 1차원적이 아니라 그래프
- GraphQL의 Fragement 문법을 사용하면 컴포넌트에서 필요한 데이터를 '표현'할 수 있음
- 각 컴포넌트에서 어떤 데이터가 필요한지 적으면 (What)
- 하나의 GraphQL 요청으로 뭉쳐줌 (How)
- 자동 상태 관리 (How)

## 결론

- 공통된 How를 잘 분석해서 꺼내고 내 코드에 What을 뭉치는 과정
- How와 What을 날카롭게 분리하는 능력이 필요

### 리팩토링 예제

- 리팩토링 전
  - express 기반

```js
app.use((req, res, next) => {
  if (!req.user) {
    res.redirect('/login')
  } else {
    next()
  }
})
```

- 1차 리팩토링
  - 단순히 힘수로 분리
  - 여전히 남은 문제는 express 기반이 아니면 redirectIfNotLoggedIn 함수를 사용할 수 없다는 것

```js
app.use(redirectIfNotLoggedIn)

const redirectIfNotLoggedIn = (req, res, next) => {
  if (!req.user) {
    res.redirect('/login')
  } else {
    next()
  }
}
```

- 2차 리팩토링
  - redirectIf 함수는 더이상 express가 아니어도 사용할 수 있음
  - compare 함수가 비교하는 함수를 자유롭게 짤 수 있고
  - 원하는 라우트 경로를 매개변수 to에 넣어줄 수 있기 때문

```js
app.use(redirectIf((req) => !req.user, '/login'))

const redirectIf = (compare, to) => {
  return (req, res, next) => {
    if (compare(res)) {
      res.redirect(to)
    } else {
      next()
    }
  }
}
```

### 참고자료

[그래서 DECLARATIVE가 뭔데? - 원지혁](https://www.youtube.com/watch?v=r7M9B_dEbCI)
