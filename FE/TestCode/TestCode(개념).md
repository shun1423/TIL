현재 많은 웹 서비스와 함께 UX/UI가 강조되고 프론트앤드 단에서도 많은 비지니스 로직을 다루게 되고 있다.

따라서 프론트앤드 단에서의 테스트도 점차 중요해지고 있는데

다양한 테스트 방법이 있는데 그중 일반적으로 작성되는 테스트 방식은 단위 테스트 (Unit Test) 이다

일반적으로 스냅샷 테스트, e2e 테스트등 다양한 테스트들이 존재한다.

테스트는 기본적으로 무언가를 검증하고 확인하는 용도이기 때문에

어떤것을 확인할것인가! 에 따라 적용하는 테스트 종류가 다르다

---

### 단위 테스트

특정한 모듈 혹은 단위가 기능을 의도한 대로 올바르게 수행하고 있는지 확인하는 것

테스트 중에서도 가장 기본이고 기저에 깔려 있어야 하는 테스트

단위테스트의 경우 범위가 한정정이므로 각 단위에 대한 테스트 코드의 양이 적고 효율적이다. 하지만 명세가 변경되면 가장 영향을 많이 받기 때문에 설계에 주의해야한다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/5e98ce70-ef9e-4aaa-a2e6-82086af04600/dbaa6c8b-7eb4-469e-b536-c681bdc85568/image.png)

---

### 테스트의 목표

1. 정확성 및 신뢰성 확보
2. 수월한 리팩터링

### 무엇을 테스트 하고 싶은지?

- 우리는 비지니스 로직을 테스트해야 한다.

1. 코드가 잘 동작 하는지 확인 (유저의 이벤트 기반 테스트)
2. 버튼을 클릭 했을때 의도한 대로 잘 동작하는지

---

### 효과적인 테스트 코드 작성 방법

테스트 코드는 한번 만들고 끝나는 것이 아닌 계속 유지 보수되고 발전시켜야 한다.

아래는 좋은 테스트 코드를 작성하기 위해 알거나 도움이 되면 좋은 것들이다.

### F.I.R.S.T 원칙

단위 테스트가 가져야 할 특성과 원칙

- Fast: 단위 테스트는 빨라야 한다.
- Isolated: 단위 테스트는 외부 요인에 종속적이지 않고 독립적으로 실행되어야 한다.
- Repeatable: 단위 테스트는 실행할 때마다 같은 결과를 만들어야한다.
- Self-validating: 단위 테스트는 스스로 테스트를 통과했는지 아닌지 판단할 수 있어야 한다.
- Timely/Through
  - Timely: 단위 테스트는 프로덕션 코드가 테스트에 성공하기 전에 구현되어야 한다
  - Through: 단위테스트는 성공적인 흐름 뿐만 아니라 가능한 모든 에러나 비 정상적인 흐름에 대해서도 대응해야 한다.

### 테스트 코드는 DAMP 하게

테스트 코드에서는 DAMP (Descriptive And Meaningful Phrases)하게 작성하라고 이야기한다.

즉 서술적이고 의미있게 작성하라는 이야기다.

일반적인 방법론인 DRY 중복을 줄이는 원칙과 충돌하지만 테스트코드는 중복이 발생해도 직관적이고 명확하게 이해되도록 테스트 코드를 작성하도록 하는게 좋다.

ex) Damp 예시

```jsx
// 첫 번째 테스트 코드
it("설명을 생략하면 이해가 되시나요?", async () => {
  const user = userEvent.setup();
  render(<Component {...preDefinedProps} />);

  const buttonElement = screen.getByRole("button", { name: "버튼입니다" });
  await user.click(buttonElement);

  expect(doSomething).toBeCalledWith(1234);
});

// 두 번째 테스트 코드
it("설명이 없어도 대충 느낌은 오시죠?", async () => {
  const something = 1234;
  const doSomething = vi.fn();
  const user = userEvent.setup();
  render(
    <Component
      {...preDefinedProps}
      something={something}
      doSomething={doSomething}
    />
  );

  const buttonElement = screen.getByRole("button", { name: "버튼입니다" });
  await user.click(buttonElement);

  expect(doSomething).toBeCalledWith(1234);
});
```

### Given - When - Then

테스트 코드는 주어진 상황에 대해 결과를 검증하는게 목적이다.

그래서 이 패턴을 이용하면 좀더 사용자 행위를 기반으로 테스트 시나리오를 정의할 수 있게 한다.

- Given : 테스트를 하기 위해 세팅하는 주어진 환경
- When : 테스트를 하기 위한 조건으로 프론트엔드에선 사용자와의 상호작용인 경우도 많다
- Then : 예상 결과를 나타내며 의도한대로 동작하는지 검증 및 확인 할수 있다.

ex)

```jsx
it("버튼을 1회 클릭하면 1번 클릭했다는 문구가 노출된다", async () => {
  // Given: 사용자와 화면이 준비되어 있고, 화면에는 버튼이 존재함
  const user = userEvent.setup();
  render(<Component />);

  // When: 사용자가 '여기를 눌러보세요'라는 버튼을 클릭함
  const buttonElement = screen.getByRole("button", {
    name: "여기를 눌러보세요",
  });
  await user.click(buttonElement);

  // Then: 문구가 나타나는지 검증함
  expect(screen.getByText("버튼을 1번 클릭했습니다.")).toBeInTheDocument();
});
```

### 개별 테스트 케이스의 목적은 명확히

각 테스트 케이스에 해당하는 테스트 코드를 작성할때 작성하고 있는 테스트 케이스의 목적이 무엇인지 명확히 생각하고 테스트 코드를 작성해야한다.

ex ) 추후 작성

### 테스트 코드의 설명도 중요하다.

테스트 코드의 설명을 대충 쓰거나 필요한 내용을 빠트리는 경우가 있는데 설명이 잘 작성되어있어야 기능을 수정 또는 확장할때 또는 디버깅할때 도움이 많이된다.

설명을 할때는 구현내용이 아닌 함수를 밖에서 본다고 생각하고 만들어야 한다.

구현내용에 집중하면 명세가 아니라 코드의 해석 설명이 나올수도 있다.

ex).

```jsx
// Hmm.. - 내부 구현에 집중되어 있고 명확하지 못한 테스트 코드입니다.
it("입력한 숫자 문자열의 첫자리가 3,4,7,8일 때만 true, 이외엔 false를 반환한다", () => {
  expect(doSomething("1234567")).toBe(false);
  expect(doSomething("2134567")).toBe(false);
  expect(doSomething("3124567")).toBe(true);
  expect(doSomething("4123567")).toBe(true);
  expect(doSomething("5123467")).toBe(false);
  expect(doSomething("6123457")).toBe(false);
  expect(doSomething("7123456")).toBe(true);
  expect(doSomething("8123456")).toBe(true);
});

// Good! - 간결하고 명확하게 함수의 명세와 정책을 담아 설명을 표현합시다.
it("2000년 이후에 태어난 사람의 주민번호 뒷자리인지 검증한다", () => {
  expect(doSomething("1234567")).toBe(false);
  expect(doSomething("2134567")).toBe(false);
  expect(doSomething("3124567")).toBe(true);
  expect(doSomething("4123567")).toBe(true);
  expect(doSomething("5123467")).toBe(false);
  expect(doSomething("6123457")).toBe(false);
  expect(doSomething("7123456")).toBe(true);
  expect(doSomething("8123456")).toBe(true);
});
```
