# [UI 표현하기](https://ko.react.dev/learn/describing-the-ui)

React는 UI를 렌더링하기 위한 JavaScript 라이브러리이다. React를 통해 작은 요소들을 재사용 가능하고 중첩할 수 있는 컴포넌트로 조합할 수 있다. 그렇다면 React **컴포넌트를 만들고, 사용자화하며, 조건부로 표시하는 방법**에 대해 알아보자.
> [Why is React considered a library and not a framework?](https://www.geeksforgeeks.org/why-is-react-considered-a-library-and-not-a-framework/)

## 첫 번째 컴포넌트
### 컴포넌트 정의하기
React 컴포넌트란, 마크업으로 뿌릴 수 있는 JavaScript 함수이다. 빌드하는 방법은 아래와 같다.
- 컴포넌트 내보내기: `export default` 접두사 사용
- 함수 정의하기: `function Profile() { }`을 사용하면 `Profile`이라는 이름의 JavaScript 함수 정의 (**꼭 대문자로 시작**해야 됨)
> [사용자 정의 컴포넌트는 반드시 대문자로 시작해야합니다](https://ko.legacy.reactjs.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized)
- 마크업 추가하기: 컴포넌트를 이루는 html 태그 등을 갖는 JSX 마크업을 반환하도록 함

## 컴포넌트 import 및 export 하기
### Root 컴포넌트란 무엇인지
일반적인 React 프로젝트는 `App.js` 파일에 root 컴포넌트가 존재한다. Next.js처럼 파일 기반으로 라우팅하는 프레임워크일 경우 페이지별로 root 컴포넌트가 다를 수 있다.
- React는 `ReactDOM.render` 메서드를 사용하여 root 컴포넌트를 실제 DOM에 렌더링함
- 이는 브라우저에서 사용자에게 보여지는 html 트리의 시작점(root)임
### 컴포넌트를 import 하거나 export 하는 방법
컴포넌트 관리에 용이하도록 컴포넌트를 하나의 파일이 아닌, 여러 파일에 적절하게 저장하는 것이 좋다. 파일 이동 방법은 아래와 같다.
- 컴포넌트를 추가할 JS 파일 생성
- 새로 만든 파일에서 함수 컴포넌트를 export (default 또는 named export 방식 사용)
- 컴포넌트를 사용할 파일에서 import (default 또는 named로 import)
  - React에서는 `'./Gallery.js'` 또는 `'./Gallery'` 둘 다 가능하지만 전자가 [native ES Modules](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Modules) 사용 방법에 더 가깝다.

## JSX로 마크업 작성하기
### JSX: JavaScript에 마크업 넣기
JSX는 JavaScript를 확장한 문법으로, JavaScript 파일을 HTML과 비슷하게 마크업을 작성할 수 있도록 해준다.
- 규칙 1: 하나의 루트 엘리먼트로 반환하기
- 규칙 2: 모든 태그는 닫아주기
- 규칙 3: ~~거의~~ 대부분 캐멀 케이스로!
  - JSX는 JavaScript 객체를 반환하며, JSX에서 작성된 attribute는 JavaScript 객체의 키가 된다. 그런데, Javascript는 변수명에 대시를 포함하거나 `class`와 같은 예약어는 변수명으로 사용할 수 없다.
> [Are dashes allowed in javascript property names?](https://stackoverflow.com/questions/5516106/are-dashes-allowed-in-javascript-property-names)

> [JavaScript Reserved Words](https://www.w3schools.com/js/js_reserved.asp)


## 중괄호가 있는 JSX 안에서 자바스크립트 사용하기
JSX에 JavaScript 로직을 추가하거나 해당 마크업 내부의 동적인 프로퍼티를 참조하고 싶을 때 JSX에서 중괄호를 사용하여 JavaScript를 사용한다.

## 컴포넌트에 props 전달하기
### 컴포넌트에 props 전달하기 
React 컴포넌트는 props를 이용해 서로 통신한다. 모든 부모 컴포넌트는 props를 줌으로써 몇몇의 정보를 자식 컴포넌트에게 모든 JavaScript 값을 전달할 수 있다.
- Props는 읽기 전용 스냅샷으로, 렌더링 할 때마다 새로운 버전의 props를 받는다.
- Props는 변경할 수 없으며 **변경이 필요한 경우 부모 컴포넌트가 다른 props를 전달**해줘야 한다. 그러면 이전의 props는 버려지고, JavsScript 엔진은 기존 props가 차지했던 메모리를 회수하게 된다.
> [Memory management in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_management)

## 조건부 렌더링
### 조건부로 JSX 반환하기 
아래와 같이 `if`/`else` 문에 따라서 조건부 렌더링 되는 경우 코드는 **다른 JSX 트리를 반환**한다.
```javascript
// 1️⃣
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;

// 2️⃣
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```
- 1️⃣과 2️⃣ 예시 코드는 **실제로 완전히 동일**하다고 볼 수 있다.
- JSX 엘리먼트는 내부 상태를 보유하지 않으면 실제 DOM 노드가 아니기 때문에 인스턴스가 아니기 때문이다.

## 리스트 렌더링
### 배열을 데이터로 렌더링하기 
JavaScript 배열을 렌더링 하기 위해 `map()`과 `filter()` 같은 메서드를 사용하면 해당 객체에서 컴포넌트 리스트를 렌더링할 수 있다.
- map() 호출 내부의 JSX 엘리먼트에는 항상 key가 필요하다. key는 각 컴포넌트가 어떤 배열 항목에 해당하는지 React에 알려주어 나중에 일치시킬 수 있도록 한다.
- 이는 배열 항목이 재정렬로 인해 위치가 변경되더라도 key는 React가 생명주기 내내 해당 항목을 식별할 수 있게 해준다.
### `key`를 사용해서 리스트 항목을 순서대로 유지하기
key는 DB에서의 데이터 혹은 로컬에서 생성된 데이터(`crypto.randomUUID()` 또는 `uuid` 같은 패키지)를 사용하여 제공해야 된다. 규칙은 아래와 같다.
> [Crypto: randomUUID() method](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID)
- key는 형제 간에 고유해야 한다. 하지만 같은 key를 다른 배열의 JSX 노드에 동일한 key로 사용해도 괜찮다.
- key는 변경되어서는 안 되며 그렇게 되면 key는 목적에 어긋난다. 따라서 렌더링 중에는 key를 생성하면 안된다.
- `key={Math.random()}` 처럼 즉석에서 생성하면 렌더링 간에 key가 일치하지 않아 컴포넌트와 DOM이 매번 재생성될 수 있다. 이는 속도가 느려지고 리스트 항목 내부의 사용자 입력도 손실된다.

## 컴포넌트 순수하게 유지하기
### 순수성: 공식으로서의 컴포넌트 
자바스크립트 일부 함수는 순수하다. 순수 함수는 오직 연산만을 수행한다. 컴포넌트를 엄격하게 순수함수로 작성하면 코드베이스가 점점 커지더라도 예상밖의 동작이나 당황케하는 버그를 피할 수 있다.
- 순수 함수는 자신의 일에 집중한다. 함수가 호출되기 전에 존재했던 어떤 객체나 변수를 변경하지 않는다.
- 같은 입력, 같은 출력 같은 입력이 주어졌다면 순수함수는 같은 결과를 반환해야 한다.
### 사이드 이펙트: 의도하지(않은) 결과 
```javascript
let guest = 0;

function Cup() {
  guest = guest + 1; // 나쁜 지점: 이미 존재했던 변수를 변경하고 있다!
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <><Cup /><Cup /><Cup /></> // 2, 4, 6 순서로 출력된다.
  );
}
```
**React는 작성되는 모든 컴포넌트가 순수 함수일 거라 가정**한다. 이러한 가정은 작성되는 React 컴포넌트가 같은 입력이 주어진다면 반드시 같은 JSX를 반환한다는 것을 의미한다.
- 사이드 이펙트를 방지하기 위해서는, 컴포넌트 바깥에 선언된 변수를 수정하지 않도록 한다. 이러한 수정은 **컴포넌트가 여러번 불리면 다른 JSX를 생성한다는 것을 의미**한다.
- props를 적절하게 사용하여 변수 대신 프로퍼티로서 사용하고 사이트 이펙트를 방지한다.

## 트리로서 UI 이해하기
### 트리로서의 UI 
트리는 요소와 UI 사이의 관계 모델이며 UI는 종종 트리 구조를 사용하여 표현된다. 예를 들어, 브라우저는 [DOM](https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/Introduction)과 [CSSOM](https://developer.mozilla.org/ko/docs/Web/API/CSS_Object_Model)을 모델링하기 위해 트리 구조를 사용한다. 모바일 플랫폼도 뷰 계층 구조를 나타내는 데 트리를 사용한다.
<img width="100%" alt="스크린샷 2024-07-17 오후 8 11 40" src="https://github.com/user-attachments/assets/0977a812-b1ea-4863-bf25-22212f960193">
React는 컴포넌트로부터 UI 트리를 생성한다. 이 예에서 UI 트리는 DOM을 렌더링하는 데 사용된다.
### 렌더 트리 
컴포넌트를 중첩하면 부모 컴포넌트와 자식 컴포넌트의 개념이 생기며, 각 부모 컴포넌트는 다른 컴포넌트의 자식이 될 수 있다. React 앱을 렌더링할 때, 이 관계를 렌더 트리라고 알려진 트리로 모델링할 수 있다.
- 조건부 렌더링을 사용하면 렌더 트리가 다른 렌더링에서 변경될 수 있다. 다른 prop 값으로 인해 컴포넌트가 다른 자식 컴포넌트를 렌더링할 수 있다.
- 렌더 트리는 최상위 컴포넌트와 리프 컴포넌트를 식별하는 데 도움이 된다. 최상위 컴포넌트는 그 아래의 모든 컴포넌트의 렌더링 성능에 영향을 미치며, 리프 컴포넌트는 자주 다시 렌더링된다.
