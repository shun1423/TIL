**브라우저 동작 과정**

**브라우저란?**

브라우저(Browser)는 사용자가 인터넷에 접속하여 웹 페이지를 탐색하고 HTML 문서, 이미지 등 여러 컨텐츠를 우리에게 표현해주는 소프트웨어 또는 애플리케이션을 의미한다.

Google Chrome, Apple의 Safari, Microsoft Edge, Mozilla의 FireFox 등이 있다.

주요 기능은 사용자가 선택한 자원을 서버에 **요청**하고, 서버로부터 받은 **응답**을 브라우저에 **렌더링**하는 것이다.

**웹 브라우저와 웹 서버의 통신 과정**

사용자가 브라우저에 웹 페이지 주소를 입력하면 웹 브라우저와 웹 서버는 아래와 같이 통신한다.

!https://github.com/Study-Mole/study-cs-mole/raw/main/FrontEnd/images/network.jpg

1. 사용자가 도메인 주소를 넣으면 DNS를 통해 도메인 주소에 해당하는 IP 주소를 얻는다.
2. IP 주소 기반으로 HTTP Request가 만들어지고, 해당 IP를 가진 서버에 전송된다.
3. 서버에서는 TCP/IP 네트워크 스택을 통해 응답을 만들어 전송한다.
4. 웹 브라우저는 패킷의 body에 들어있는 HTML 파일을 렌더링 해서 웹 브라우저에 보여준다.

**브라우저 구조**

브라우저는 아래와 같은 구조를 가진다.

!https://github.com/Study-Mole/study-cs-mole/raw/main/FrontEnd/images/browser.jpg

- `User Interface`: 요청한 페이지를 보여주는 창 이외의 모든 UI
  - ex) 주소창, 새로고침, 북마크 등
- `Browser Engine`: UI와 렌더링 엔진의 중개자.
  - ex) 새로고침 버튼이 클릭 되었을 때 브라우저 엔진이 이를 수행
- `Rendering Engine`: HTML, CSS, JS를 파싱한 결과를 바탕으로 페이지를 그림
- `Network`: HTTP, HTTPS 같은 프로토콜을 이용해 외부 리소스를 얻음
- `JavaScript 인터프리터`: JavaScript를 해석하고 실행
- `UI 백엔드`: 렌더링 엔진이 분석한 렌더 트리를 브라우저에 그리는 역할. 브라우저가 동작하고 있는 운영체제의 인터페이스들을 따르는 기본적인 위젯 처리
  - ex) 안드로이드와 iOS의 서로 다른 Alert, Select Box 등
- `웹 스토리지`: 브라우저 자체에서 하드디스크와 같이 데이터를 로컬에 저장하기 위한 레이어

**렌더링 동작 과정 (Critical Rendering Path)**

웹 브라우저와 웹 서버의 통신이 끝나고, 브라우저에 출력되는 단계([웹 브라우저와 웹 서버의 통신의 4번째 단계](https://github.com/Study-Mole/study-cs-mole/tree/main/FrontEnd/Common#%EC%9B%B9-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%99%80-%EC%9B%B9-%EC%84%9C%EB%B2%84%EC%9D%98-%ED%86%B5%EC%8B%A0-%EA%B3%BC%EC%A0%95))를 Critical Rendering Path라고 한다. 이 단계는 크게 5단계로 구분된다. 웹 페이지에 사용자가 접속하게 되면, 네트워크를 통해 해당 페이지의 HTML 문서를 얻어올 수 있다. 그러면 렌더링 엔진이 읽어 들인 HTML 문서를 해석한다.

**1. 2. DOM Tree와 CSSOM Tree Build**

브라우저는 HTML, CSS, JavaScript 세 종류의 언어를 해석할 수 있다. 그 중 JavaScript는 렌더링 엔진이 아니라 별도의 JavaScript 해석기라는 별도의 레이어에서 언어를 해석한다. 따라서 렌더링 엔진에서는 HTML과 CSS를 해석한다.

1. _DOM Tree Build_

   HTTP 또는 HTTPS 통신으로 byte 형태의 HTML 파일을 가져오게 된다. 이 후 이 byte 형태의 데이터를 DOM으로 전환하는 작업을 수행한다.

2. _CSSOM Tree Build_

   렌더링 엔진은 HTML 문서를 위에서부터 한 줄씩 파싱하며 DOM을 생성한다. 그러다 CSS를 로드하는 link 태그 혹은 style 태그를 만나면 DOM 생성을 중지한 후 CSS parsing의 결과물인 CSSOM을 생성하는 과정을 진행한다.

**3. 렌더 트리 구축**

DOM 트리가 구성되는 동안 브라우저는 렌더 트리를 구성한다. 렌더 트리는 DOM 트리와 CSSOM 트리를 조합하여 만들어진다. 이는 어떠한 요소들이 보여야 하는지, 어떤 스타일이 적용되어야 하는지, 그리고 어떤 순서로 나타낼 것인지를 명세한다.

**4. 레이아웃 또는 리플로우**

이 단계에서 Rendering Tree의 각 Node들의 위치와 크기가 계산된다.

페이지에서 각 객체의 정확한 위치와 크기를 계산하기 위해 브라우저는 렌더링 트리를 루트에서부터 순회한다. viewport 내에서 각 요소의 정확한 위치와 크기를 캡쳐하는 상자모델이 출력되고, 모든 상대적인 측정 값은 픽셀로 변환된다.

**5. 페인트**

말 그대로 레이아웃 단계를 통해 화면에 배치된 엘리먼트에 색을 입히고 레이어의 위치를 결정하는 단계이다. 문서가 클수록 브라우저가 수행해야 하는 작업도 더 많아지며, 스타일이 복잡할수록 시간이 더 소요된다.

[참고]

- [프론트엔드 개발자라면 알고 있어야 할 브라우저의 동작 과정](https://yozm.wishket.com/magazine/detail/1338/)
- [Mdn, 웹페이지를 표시한다는 것: 브라우저는 어떻게 동작하는가](https://developer.mozilla.org/ko/docs/Web/Performance/How_browsers_work)
- [웹 브라우저의 동작 과정](https://velog.io/@duck-ach/Network-%EC%9B%B9-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%9D%98-%EB%8F%99%EC%9E%91-%EA%B3%BC%EC%A0%95)

### 추가

1. **스크립트 로딩 방식에 따른 실행**:

   - **동기 로딩**(기본 `<script>` 태그): 브라우저가 HTML을 파싱하다가 `<script>` 태그를 만나면 **DOM 생성과 CSSOM 생성 과정을 일시 중단**하고, 스크립트를 로드한 후 실행이 끝나면 다시 HTML 파싱을 이어간다. 즉, **렌더링 중간에 JavaScript 해석기가 실행**.
   -
   - **Async**: HTML 파싱 중 `<script async>` 태그를 만나면 JavaScript 파일을 **백그라운드에서 다운로드**하면서 동시에 HTML 파싱을 계속한다. 다운로드가 끝나는 즉시 JavaScript를 실행.
   - **Defer**: `<script defer>`는 HTML 파싱이 끝난 후에 JavaScript가 실행되므로, **완전히 HTML과 CSS가 파싱된 후** JavaScript가 실행.

1. **DOMContentLoaded 이벤트**:
   브라우저는 모든 HTML과 CSS가 파싱된 후 DOM 트리가 완성되면 `DOMContentLoaded` 이벤트를 발생시킨다. 이 시점에 실행을 원하는 JavaScript는 HTML이 완전히 로드된 후에 작동하므로, **사용자에게 보이는 초기 화면을 먼저 렌더링**할 수 있다.

### 간단한 요약

1. **HTML**을 읽어가면서 페이지 구조를 만든다.
2. HTML 도중 **CSS**를 만나면 스타일을 가져와 적용.
3. HTML 도중 **JavaScript**를 만나면 필요한 동작을 실행.
4. HTML, CSS, JavaScript 모두 끝나면 요소들을 배치하고 색을 입혀서 화면에 보여준다!
