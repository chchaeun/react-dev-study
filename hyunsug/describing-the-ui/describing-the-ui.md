---
marp: true
class: _invert
---

# UI 표현하기

> Component? UI의 구성 요소

---

### React Component? JavaScript로 작성된 함수

> 16에서 함수형이 도입되고, 16.8버전에서 Hook이 도입된 후 현재의 React는 함수형으로 컴포넌트를 작성하는 것을 원칙으로 하고 있다

---

### React Component의 정의

```jsx
// Pascal Case의 형태로 선언한다
function Component() {
  return (
    <Flex>
      <div>Test</div>
    </Flex>
  );
}
```

> Pascal Case가 아니라면?

---

#### JSX의 변환

JSX의 파싱이 끝나고 나면 `React.createElement`로 변환된다

```jsx
function Component() {
  return React.createElement(
    Flex,
    null,
    React.createElement('div', null, 'Test')
  );
}
```

이때 소문자로 시작하면(`<component />`) `React.createElement('component', null)`로 전환된다 (예전)

---

## import/export

```tsx
// 그냥 자연스럽게 쓰고 있는 import 형태이며, default export된 형태
import ComponentA from '@/components/ComponentA';

// ESM 스타일 import
import ComponentB from '@/components/ComponentB.tsx';

// ESM 스타일, named export 형태
import { ComponentC } from '@/components/ComponentC.tsx';
```

---

## JSX?

> JSX와 React는 서로 다른 별개의 개념입니다.

위에서 `React.createElement~`의 형태로 전환됨을 보았습니다
하지만 React와 JSX의 발전으로 Babel, TypeScript 등에 의해 React의존없이 변환됩니다

> 따라서, `import React from "react";` 또한 이제는 필요가 없어졌죠

---

### 변화된 JSX 컴파일

```js
// 컴파일러에 의해 자동으로 추가됩니다!
import { jsx as _jsx } from 'react/jsx-runtime';

function App() {
  return _jsx('h1', { children: 'Hello world' });
}
```

---

### 하나의 태그로 감싸야 한다

return되는 부분은 여러 태그가 부모가 되어선 안됩니다.
하나의 객체로 완성되어야 하기에 형제로 두기를 원한다면
`Fragment` 또는 `<> </>`로 감싸줘야 하죠

---

### HTML attributes?

```html
<main>
    <div class="class1">
        Div with class name 'class1'
    </div>
    <label for="input">Text Input</label>
    <input id="input" type="text"></input>
</main>
```

일반적인 html은 이렇게 작성되죠

---

JSX로 작성할때는 for, class와 같이 JS의 예약어인 경우 `htmlFor`, `className` 같이 바꿔서 사용하며,
`-(dash)`로 연결되어야 하는 부분들은 JS의 변수명으로 지정될수 없어 `camelCase`로 작성해야 합니다.

```jsx
<main>
  <div className="class1">Div with class name 'class1'</div>
  <label htmlFor="input">Text Input</label>
  <input id="input" type="text"></input>
</main>
```

> aria-속성과 data- dataset은 예외로 dash를 사용하여 작성합니다.

---

### JSX 안에서 JS의 사용

다들 아시다시피 `{ }` 중괄호로 감싸 사용합니다

그럼 아래와 같은 변환 결과를 나타내게 되겠죠

```js
// 조건부 렌더링
_jsx('div', {
  children: isTrue
    ? _jsx('span', { children: 'True' })
    : _jsx('span', { children: 'False' }),
});
// Array 렌더링
_jsx('ul', {
  children: items.map((item) =>
    _jsx('li', { key: item.id, children: item.name })
  ),
});
```

---

### 이중 중괄호?

style attribute를 설정할때 이중으로 중괄호를 사용하게 됩니다.

```js
<div style={{ width: '100%' }} />
```

이는 JS의 객체를 style 속성에 전달하는 것으로,

```js
const styleObj = { width: '100%' };
<div style={styleObj} />;
```

와 동일한 형태입니다.

> 인라인 css 작성시에도 key를 camelCase로 작성해야 합니다

---

### children

```jsx
<Parent>
    <ChildrenComponent>
</Parent>
```

이렇게 사용되는 경우 Parent는 `children` prop을 가져야 합니다
