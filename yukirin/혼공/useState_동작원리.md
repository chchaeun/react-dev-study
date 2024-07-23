## 1. `setState()`를 호출할 때 마다, 컴포넌트가 무조건 리렌더링 되나?

```javascript
import { useState } from 'react';
...
const [state, setState] = useState(0);
```

값이 변경돼야 리렌더링 된다. 즉, `setState(0)`와 같이 0으로 변경하면 리렌더링이 일어나지 않는다. 이유는 다음과 같다.

- setState가 호출되면 React는 상태가 변경될 것이라고 예상하고, 해당 컴포넌트를 리렌더링할 준비를 한다.
- React는 이전 상태와 새 상태를 비교한다. 이 때의 **비교는 얕은 비교이다. 즉, 객체의 참조 또는 원시 값을 비교**한다.
- 상태가 변경됨이 감지 되어야 리렌더링 프로세스를 진행한다. 아니면 스킵한다.

<br />

## 2. 그럼 state를 두번 호출하면 두번 리렌더링 되나?

```javascript
const [state, setState] = useState(0);
setState(1);
setState(2);
```

아니다. 값을 여러번 바꾸더라도 최종적으로 업데이트된 state로 리렌더링은 한번만 일어난다.

- React는 state가 변경되면 다음 렌더링 큐에 넣고 모든 이벤트 핸들러의 코드 실행을 기다렸다가 `dispatchSetState`을 통해 state 업데이트가 시작된다.
  - React 18 이전에는 `dispatchSetState`이 아닌, `dispatchAction`
- 또한 `dispatchSetState`은 하나의 객체에 대한 업데이트를 모아서 한번에 처리한다.

<br />

## 3. `dispatchSetState`는 어떻게 업데이트를 한번에 처리하나?

React에서 `useReducer`, `useContext` 등도 모두 업데이트가 한번에 처리되며 이들 역시 내부적으로 `dispatchSetState`를 사용한다.

> [ReactFiberHooks.js - line 3415](https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiberHooks.js#L3415)

```javascript
function dispatchSetState(fiber, queue, action) {
  const lane = requestUpdateLane(fiber); // 현재 업데이트에 대한 업데이트 우선순위 결정

  const update = {
    // 업데이트에 대한 정보를 포함하는 업데이트 객체 생성
    lane,
    action,
    hasEagerState,
    eagerState,
    next,
  };

  if (isRenderPhaseUpdate(fiber)) {
    // 렌더링 페이즈 내부일 때 업데이트를 현재 단계 렌더링 큐에 추가
    enqueueRenderPhaseUpdate(queue, update);
  } else {
    // 렌더링 페이즈 외부일 때
    if (is(eagerState, currentState)) {
      // 이전과 새로운 상태가 동일하면 리렌더링 다음 과정 중지
      enqueueConcurrentHookUpdateAndEagerlyBailout(fiber, queue, update);
      return;
    }
    // 이전과 새로운 상태가 동일하지 않다면 다음 렌더링에서 업데이트 진행되도록 상태 저장
    // 업데이트는 실제 다음 리렌더링 시작될 때 처리
    const root = enqueueConcurrentHookUpdate(fiber, queue, update, lane);
  }
}
```

- 업데이트 우선순위를 결정하고, 업데이트 정보를 포함하는 업데이트 객체를 생성한다.
- 렌더링 페이즈 내부라면? 현재 렌더링에 업데이트 내용이 포함되도록 현재 렌더링 큐에 추가한다.
- 렌더링 페이즈 외부라면? 다음 렌더링에 업데이트 되도록 업데이트 할 준비를 한다.
  - 이전과 새로운 상태가 동일하다면 아무 것도 하지 않고 리렌더링 예약을 피하며 / 동일하지 않다면, 리렌더링이 일어나도록 예약한다.

### 렌더링 페이즈 내부 `setState()` 예시

```javascript
function MyComponent() {
  const [count, setCount] = useState(0);

  if (count < 5) {
    setCount(count + 1); // 렌더링 중 상태 업데이트
  }

  return <div>{count}</div>;
}
```

- 위 예시에서 MyComponent는 렌더링 중에 `setCount`를 호출한다.
- React는 이 상태 업데이트를 즉시 현재 렌더링 페이즈 업데이트 큐에 추가하고, 현재 작업을 계속 진행한다.

### 렌더링 페이즈 외부 `setState()` 예시

```javascript
function MyComponent() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount((prevCount) => prevCount + 1); // 커밋 페이즈에서 상태 업데이트
    }, 1000);
    return () => clearInterval(timer);
  }, []);

  return <div>{count}</div>;
}
```

- 위 예시에서 `setInterval` 내부의 `setCount` 호출은 커밋 페이즈에서 발생한다.
- React는 이 상태 업데이트를 큐에 저장하고, 다음 렌더링 사이클에서 처리한다.

<br />

## 4. 실제로 state의 업데이트가 일어나는 곳을 어딜까?

앞서 `dispatchSetState`에서는 직접적으로 state를 업데이트하지 않았다. 단지 업데이트 객체를 생성하고, 저장할 뿐이었다. 그렇다면 도대체 state는 어디서 업데이트 해줄까?

|                                     처음 마운트될 때                                      |                                       업데이트될 때                                       |
| :---------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------: |
| ![image](https://github.com/user-attachments/assets/123e5dbb-a6ef-4288-955e-5e7f2068a0a2) | ![image](https://github.com/user-attachments/assets/9ede3364-93cd-4a28-9ccd-0d1b7f34dcf4) |

React의 `useState` 훅은 컴포넌트의 상태를 관리하는 데 사용되며 이는 컴포넌트가 처음 렌더링될 때와 업데이트 될 때 다르게 동작한다.

- 컴포넌트의 state가 변경되면 `dispatchSetState`가 state의 여러 업데이트를 병합하여 하나로 처리한다고 했다.
- 그리고 그 과정에서 `dispatchSetState`는 상태가 업데이트 되었다면 리렌더링을 예약한다.
- 리렌더링이 일어난다면 `useState`는 `updateState`를 return하도록 동작된다. 이 `updateState`가 state를 업데이트 해준다.

<br />

## 5. state가 업데이트 되었다면 화면 DOM에 어떻게 반영될까?

- state 변경이 감지되면 Reconciler는 상태 변경 후 새로운 가상 DOM을 생성하고, 이전 가상 DOM과 diffing하여 변경 사항을 추적한다.
- 이 단계에서 변경된 부분만 찾아내어 실제 DOM에 적용할 최소한의 업데이트를 결정한다.
- Reconciler가 결정한 변경 사항은 Renderer에 의해 가상 DOM을 기반으로 실제 DOM에 반영된다.
