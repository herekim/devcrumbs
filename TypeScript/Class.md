# 주요 키워드

## 접근 제한자

![접근제한자 표](/public/images/접근제한자.png)

1. public <br/>
2. pretected <br/>
3. private <br/>

## readonly 키워드

- readonly가 선언된 프로퍼티는 선언 시 또는 생성자 내부에서만 값을 할당 가능

## static 키워드

- 정적 메소드, 클래스의 인스턴스가 아닌 클래스 이름으로 호출

## 추상 클래스

- 상속만을 위해서 사용
- 추상 클래스를 상속받은 클래스는 추상 메소드를 정의해야함

## getter & setter

- 비공개 변수에 대한 접근과 수정을 위한 함수

```ts
class Person {

// 비공개 속성
private _age: number = 0;

// getter 함수
get age(): number {
  return this._age_;
}

// setter 함수
set age(age : number) {
  this._age = age
}

/*
let plant = new Plant()
console.log(plant.age); // 0
plant.age = 5;
console.log(plant.age); // 5
plant.age = 10;
console.log(plant.age); // 10
*/
```

# 클래스를 상태관리에 활용해보기

- 주간 스케줄 데이터를 가져올 수 있는 class
- 방법은 두 가지
  1. state, setState를 둘 다 생성자 파라미터로 전달해서 presenter의 메소드로만 관리
  2. state 값을 생성자 파라미터로 전달하고 각 setter 메소드에서 인자로 setState 함수를 넘겨받음

## 1번 방식

### 특징

- state, setState를 모두 class에 위임해서 presenter만 props로 넘겨주면 메소드로 모든 게 처리 가능하다
- state, setState를 모두 class 생성자 파라미터로 전달하기 때문에 hook 내부에 클래스 인스턴스를 생성해야한다
- hook 내부에 인스턴스를 생성하면 렌더링 시 해당 state, setState를 매번 다시 넘겨주는 특징이 있다
- getThisWeek 메소드와 같은 메소드는 클래스 내부에서만 사용되고 setToThisWeek 메소드로 this.setWeek를 변경해주는 방식이다
- state를 사용할 땐 getWeek 메소드를 사용하면 된다

### 예시

- constructor
  ```ts
  constructor(
    week: WeekType,
    setWeek: Dispatch<SetStateAction<ThisWeekReturn[]>>
    ) {
    this.week = week;
    this.setWeek = setWeek;
  }
  ```
- method example

  ```ts
  getWeek() {
    return this.week;
  }

  private getNextWeek() {
    const nextWeek = this.week.map((day) => {
      /// logic...
    });

    return nextWeek;
  }

  setToNextWeek() {
    this.setWeek(this.getNextWeek());
  }
  ```

- use example

  ```tsx
  const handleNextWeek = () => {
    presenter.setToNextWeek();
  };

  return (
    <>
      <main>
        {presenter.getWeek().map((day) => (
          /// details
        ))}
      </main>
      <button onClick={handleNextWeek}>
        Next Week
      </button>
    </>
  );
  ```

## 2번 방식

### 특징

- hook 바깥에서 전역으로 한번 클래스 인스턴스를 만든다
- useState 초기값에 클래스 초기값을 넣어준다
- 사실상 클래스 내부의 this.week는 외부로 공개되지않고 각 setter 함수의 인자로 넘겨받은
  setState로 값을 넘겨주기 위한 도구로 사용된다

### 예시

- constructor

  ```ts
  constructor(
    private week: WeekType
  ) {
    this.week = week;
  }
  ```

- method example

  ```ts
  get week() {
    return this.week;
  }

  set nextWeek(update: Dispatch<SetStateAction<WeekType[]>>) {
    this.week = this.week.map((day) => {
      /// logics...
    });

    update(this.week);
  }

  set prevWeek(update: Dispatch<SetStateAction<WeekType[]>>) {
    this.week = this.week.map((day) => {
      /// logics...
    });

    update(this.week);
  }
  ```

- use example

  ```tsx
  const [week, setWeek] = useState(weekSchedule.getWeek());

  const handleNextWeek = () => {
    presenter.nextWeek(setWeek);
  };

  return (
    <>
      <main>
        {week.map((day) => (
          /// details
        ))}
      </main>
      <button onClick={handleNextWeek}>
        Next Week
      </button>
    </>
  );
  ```

## 결과

- 상태관리를 클래스에 위임하는게 어울리지 않는 옷을 입는 것 같다
- 결국 클래스 내부의 프로퍼티와 hook 내부의 state는 계속 싱크를 맞춰야 하고
- state 값을 사용할 지 위임받은 메소드를 사용할지(e.g. getWeek()) 고민해야 한다
- 이 말은 상태관리가 분산되는 것인데, 그 자체로 좋지 않은 방식인 것 같다
- 클래스를 이용한 방식은 필요한 비즈니스 로직이나 api 처리 등에서 사용하면 좋을 것 같고, 상태 관리 관련 로직은 웬만하면 custom hook으로 처리하자
