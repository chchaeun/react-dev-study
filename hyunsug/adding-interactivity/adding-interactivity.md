---
marp: true
theme: uncover
class: _invert
---

# 리액트와 상호작용

state와 on{Event}를 통해서...
이벤트핸들러는 순수함수일 필요가 없다

---

## EventHandler의 이름은 어떻게?

이벤트 핸들러는 onEvent={`eventHandler`}에 위치하게 되는 함수
event 객체를 자동으로 argument로 받게 된다.

이름을 어떻게 지어야 할까?

---

> 일반 함수와 이벤트 핸들러의 차이가 뭘까?
> 이벤트 객체를 받아서 실행해야만 이벤트핸들러라고 봐야할까?

---

### on- vs handle-

---

개인적인 생각으로, on- 함수는 무언가가 실행되고 나서

**`바깥으로 무언가를 알리거나, 전달해야 하는 경우`**

에 적합한 이름이지 않을까 라고 요즘은 생각한다

---

결국 prop으로 전달해서 하위 컴포넌트 등에서 특정 동작 시 추가로 실행되어야 하는 경우에
on- 형태로 전달하는 것이 적합할 것 같다

> 관습적으로 이벤트 핸들러 prop의 이름은 on으로 시작하여 대문자 영문으로 이어집니다. - [리액트 문서](https://ko.react.dev/learn/responding-to-events#naming-event-handler-props)

---

반면 handle- 함수는

**`특정한 이벤트가 발생했을 때 실행되는 함수`**

라고 생각하며, `동작`이 아닌 `대상+동작`이 보다 구체적이어서 좋다고 생각한다

`handleClick`, `handleModalToggleButtonClick`

---

```jsx
export const Profile = () => {
  return (
    <div>
      <ProfileHeader onMyPageClick={onMyPageClick} />
      <Button onClick={handleButtonClick}>버튼 클릭</Button>
    </div>
  );
};
```

---

### 이벤트 전파

컴포넌트 내에 동일 이벤트에 대해 여러 이벤트들이 등록되어야 한다면,
`event.stopPropagation()`을 사용하자

---

#### [이벤트 전파를 통한 캡처](https://ko.react.dev/learn/responding-to-events#capture-phase-events)

`on{Event}Capture`를 통해 로그 분석 등을 할 수 있다.

다만, 프로덕션에서는 제거되는 것이 좋을 수 있음

---

## useState의 사용

왜 useState는 if, for, 함수 내에서 사용될 수 없을까?
[React hooks:not magic, just arrays](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)
[위 글의 번역본](https://velog.io/@hangem422/%EB%B2%88%EC%97%AD-React-Hooks-%EB%A7%88%EC%88%A0%EC%9D%B4-%EC%95%84%EB%8B%88%EB%9D%BC-%EA%B7%B8%EC%A0%80-%EB%B0%B0%EC%97%B4%EC%9D%BC-%EB%BF%90%EC%9D%B4%EB%8B%A4)

---

useState 뿐 아니라 모든 훅(React 19에 추가될 예정인 `use` 제외)이
그런 조건을 갖는 이유는 크게 `훅 호출 순서`와 관련이 깊다.

- 리렌더링마다 호출 순서를 동일하게 유지해야 React에 의한 상태 유지/업데이트가 올바르게 일어날 수 있음
- React 내부에 훅의 순서가 '배열'로 저장되기에 다음 인덱스에 영향을 줄 수 있음

---
