# Jest 사용방법

### 프로젝트 생성

새 프로젝트 생성

```jsx
mkdir my-jest-app
cd my-jest-app
npm init -y

```

Jest 설치

```jsx
npm install --save-dev jest
```

설치 완료후 package.json 파일에 테스트 명령어 추가

```jsx
{
  "scripts": {
    "test": "jest"
  }
}

```

### 테스트 코드 예시

test.js 파일 생성후

```
test("1 is 1", () => {
  expect(1).toBe(1);
});
```

이후 터미널에 npm test 를 실행하게 되면 초록색 글씨로 테스트가 통과했다고 나온다.

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 PASS  ./test.js
  ✓ 1 is 1 (3ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.868s, estimated 1s
Ran all test suites.
```

일반적으로 테스트는 다음과 깉이 일정한 패턴으로 작성한다.

```jsx
test("테스트 설명", () => {
  expect("검증 대상").toXxx("기대 결과");
});
```

toXxx 부분에 사용되는 함수를 Test Matcher라 한다

그리고 npm test를 실행하면 프로젝트 내에 모든 테스트 파일을 찾아서 테스트를 실행해준다. Jest는 기본적으로 test.js 로 끝나고나,

test 디렉터리 안에 있는 파일을 모두 테스트 파일로 인식한다

따라서 특정 테스트 파일만 실행하고 싶은 경우는 npm test<파일명 이나 경로> 를 입력하면 된다.

---

## 3. **비동기 코드 테스트하기 (시간이 걸리는 코드)**

때로는 코드가 **시간이 걸리는 작업**을 할 수 있어요. 예를 들어 서버에서 데이터를 가져오는 코드를 테스트해 볼게요.

### 3.1. 비동기 함수 작성하기

1초 후에 데이터를 돌려주는 함수를 만듭니다. (`fetchData.js`)

```jsx
javascript
코드 복사
// fetchData.js
function fetchData() {
  return new Promise((resolve) => {
    setTimeout(() => resolve('데이터 로드 완료!'), 1000);
  });
}
module.exports = fetchData;

```

### 3.2. 비동기 함수 테스트하기

아래처럼 `fetchData.test.js` 파일을 만들어요.

```jsx
javascript
코드 복사
// fetchData.test.js
const fetchData = require('./fetchData');

test('비동기 데이터는 "데이터 로드 완료!"가 되어야 한다.', async () => {
  const data = await fetchData();  // 데이터를 기다림
  expect(data).toBe('데이터 로드 완료!');  // 결과가 맞는지 확인
});

```

- **`async` / `await`**: 비동기 코드를 기다렸다가 실행하는 방법이에요.

터미널에서 `npm test`를 실행해 보세요! 성공하면:

```bash
bash
코드 복사
PASS  ./fetchData.test.js
✓ 비동기 데이터는 "데이터 로드 완료!"가 되어야 한다. (1001ms)

```

---

## 4. **모킹(Mock) 테스트하기: 가짜 데이터 사용하기**

서버에서 데이터를 받아오는 코드 대신, 가짜 데이터를 써서 테스트할 수 있어요.

### 4.1. 데이터 요청 함수 만들기 (`user.js`)

```jsx
javascript
코드 복사
// user.js
const fetch = require('node-fetch');

async function getUser(id) {
  const response = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`);
  return response.json();
}

module.exports = getUser;

```

### 4.2. 모킹 테스트 코드 작성 (`user.test.js`)

```jsx
javascript
코드 복사
// user.test.js
const getUser = require('./user');
jest.mock('node-fetch', () => jest.fn());  // fetch를 가짜로 만들기

const fetch = require('node-fetch');  // 가짜 fetch 가져오기

test('유저 정보 가져오기', async () => {
  fetch.mockResolvedValue({
    json: () => ({ id: 1, name: '홍길동' }),  // 가짜 데이터
  });

  const user = await getUser(1);  // 유저 가져오기
  expect(user.name).toBe('홍길동');  // 이름이 맞는지 확인
});

```

이제 `npm test`로 테스트하면 가짜 데이터를 사용한 테스트도 성공합니다!

---

**React 컴포넌트 테스트하기**

만약 **React**를 사용하고 있다면, 아래처럼 컴포넌트도 테스트할 수 있어요.

### 6.1. React 컴포넌트 만들기 (`Hello.js`)

```jsx
javascript
코드 복사
// Hello.js
import React from 'react';

function Hello({ name }) {
  return <h1>Hello, {name}!</h1>;
}

export default Hello;

```

### 6.2. 테스트 코드 작성하기 (`Hello.test.js`)

```jsx
javascript
코드 복사
// Hello.test.js
import { render, screen } from '@testing-library/react';
import Hello from './Hello';

test('컴포넌트가 "Hello, 홍길동!"를 렌더링해야 한다.', () => {
  render(<Hello name="홍길동" />);
  expect(screen.getByText('Hello, 홍길동!')).toBeInTheDocument();
});

```

**React 테스트 실행**은 이전과 똑같이 `npm test` 명령어로 할 수 있어요!

---

## Jest Matcher 함수

Jest에서는 Matcher가 아주 중요하다. expect()로 실제 값을 예상하고, Matcher를 사용해 기대한 결과와 일치하는지 비교한다.

### 1. 기본 Matcher : toBe()와 toEqual()의 차이

toBe() - 정확한 일치 비교(===)

```jsx
test("toBe: 정확한 값 비교", () => {
  expect(2 + 2).toBe(4); // 4와 정확히 일치하는지 확인
});
```

- **원시값(숫자, 문자열, 불리언)** 같은 경우 정확히 일치해야 통과

toEqual() - 객체나 배열의 값 비교

```jsx
test("toEqual: 객체 비교", () => {
  const obj = { name: "홍길동" };
  expect(obj).toEqual({ name: "홍길동" }); // 객체 내부 값이 같은지 비교
});
```

- 객체나 배열은 **참조값**이 달라도 내부 내용이 같으면 통과합니다.

### 2. 참/거짓 관련 Matcher

toBeTruthy() - 참(true)처럼 평가되는 값

```jsx
test("toBeTruthy: 참처럼 평가되는 값", () => {
  expect(1).toBeTruthy(); // 1은 true처럼 평가됨
});
```

toBeFalsy() = 거짓(false)처럼 평가되는 값

```jsx
test("toBeFalsy: 거짓처럼 평가되는 값", () => {
  expect(0).toBeFalsy(); // 0은 false처럼 평가됨
});
```

### 3. 숫자 관련 Matcher

toBeGreaterThan() - 크다

```jsx
test("toBeGreaterThan: 5는 3보다 크다", () => {
  expect(5).toBeGreaterThan(3);
});
```

toBeGreaterThanOrEqual() - 크거나 같다

```jsx
test("toBeGreaterThanOrEqual: 5는 5보다 크거나 같다", () => {
  expect(5).toBeGreaterThanOrEqual(5);
});
```

toBeLessThan() - 작다

```jsx
test("toBeLessThan: 3은 5보다 작다", () => {
  expect(3).toBeLessThan(5);
});
```

toBeCloseTo() - 부동소수점 숫자 비교

부동소수점 숫자(소수점 아래 긴 숫자)는 `toBe`로 정확히 비교하기 어렵기 때문에 `toBeCloseTo`를 사용한다.

```jsx
test("toBeCloseTo: 소수점 비교", () => {
  expect(0.1 + 0.2).toBeCloseTo(0.3); // 0.30000000000000004 문제 해결!
});
```

### 4. 문자열 관련 Matcher

toMatch() - 정규표현식으로 문자열 비교

```jsx
test("toMatch: 문자열에 특정 단어 포함", () => {
  expect("Hello Jest").toMatch(/Jest/); // 'Jest'가 포함되어 있는지 확인
});
```

### 5. 배열과 iterable 관련 Matcher

toContain() - 배열에 특정 값 포함

```jsx
test("toContain: 배열에 값 포함", () => {
  expect([1, 2, 3]).toContain(2); // 2가 배열에 포함되어 있는지 확인
});
```

toContainEqual() - 객체 배열에 특정 객체 포함

```jsx
test("toContainEqual: 객체 배열에 같은 객체 포함", () => {
  expect([{ id: 1 }, { id: 2 }]).toContainEqual({ id: 2 });
});
```

### 6. **예외 처리 (에러) 관련 Matcher**

toThrow() - 에러를 던지는지 확인

```jsx
function throwError() {
  throw new Error("에러 발생!");
}

test("toThrow: 에러 발생 테스트", () => {
  expect(() => throwError()).toThrow("에러 발생!");
});
```

### 7. **비동기 코드 관련 Matcher**

`resolves` – **Promise가 성공했는지 확인**

```jsx
test("resolves: 비동기 함수 성공 확인", async () => {
  const promise = Promise.resolve("성공!");
  await expect(promise).resolves.toBe("성공!");
});
```

`rejects` – **Promise가 실패했는지 확인**

```jsx
test("rejects: 비동기 함수 실패 확인", async () => {
  const promise = Promise.reject("실패!");
  await expect(promise).rejects.toBe("실패!");
});
```

---

### 8. **Mock 함수와 호출 관련 Matcher**

`toHaveBeenCalled()` – **함수가 호출되었는지 확인**

```jsx
const mockFn = jest.fn();

test("toHaveBeenCalled: 함수가 호출되었는지 확인", () => {
  mockFn();
  expect(mockFn).toHaveBeenCalled(); // mockFn이 호출된 적 있는지 확인
});
```

`toHaveBeenCalledTimes()` – **함수가 몇 번 호출되었는지 확인**

```jsx
test("toHaveBeenCalledTimes: 정확한 호출 횟수 확인", () => {
  mockFn();
  mockFn();
  expect(mockFn).toHaveBeenCalledTimes(2); // 2번 호출되었는지 확인
});
```

`toHaveBeenCalledWith()` – **특정 인수로 호출되었는지 확인**

```jsx
test("toHaveBeenCalledWith: 특정 인수로 호출 확인", () => {
  mockFn("Hello", 42);
  expect(mockFn).toHaveBeenCalledWith("Hello", 42); // 인수 확인
});
```

---

### 9. **Snapshot 테스트 (React 컴포넌트 등 UI 테스트)**

Jest는 **스냅샷(snapshot)**을 사용해 컴포넌트가 예상대로 렌더링되는지 확인할 수 있다

```jsx
import renderer from "react-test-renderer";
import Hello from "./Hello";

test("Hello 컴포넌트 스냅샷 테스트", () => {
  const tree = renderer.create(<Hello name="홍길동" />).toJSON();
  expect(tree).toMatchSnapshot(); // 스냅샷과 비교
});
```

- 첫 실행 시 스냅샷 파일이 생성된다.
- 코드가 바뀌면 스냅샷과 달라져 테스트가 실패

---

## 10. **마무리: 언제 어떤 Matcher를 써야 할까?**

- **정확한 값 비교**: `toBe()`
- **객체/배열 비교**: `toEqual()`
- **참/거짓 비교**: `toBeTruthy()`, `toBeFalsy()`
- **숫자 비교**: `toBeGreaterThan()`, `toBeLessThan()`
- **문자열 비교**: `toMatch()`
- **배열에 값 포함 확인**: `toContain()`
- **에러 테스트**: `toThrow()`
- **비동기 함수 테스트**: `resolves`, `rejects`
