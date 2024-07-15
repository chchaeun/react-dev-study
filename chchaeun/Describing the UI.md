## JSX 동작 원리
JSX: `React.createElement` 의 semantic sugar
- semantic sugar: 프로그래밍 언어에서 더 쉽거나 편하게 읽고 쓰기 위해 사용하는 것
- 처음에는 React와 함께 사용하기 위해 만들어졌지만 여러 웹 프레임워크에 채택되어 사용됨

```javascript
import React from 'react';

function App(){
	return (
		<div
			style={{
				display: 'block',
				color: 'blue'
			}}
		>
			Hello World!
		</div>
}
```

```javascript
React.createElement(
	'div',
	{
		style: {
			display: 'block',
			color: 'blue'
		}
	},
	'Hello World!'
)
```

### 왜 여러 JSX 태그를 하나로 감싸줘야 할까요?

- `react/src/jsx/ReactJSXElement.js`
```javascript
function createElement(type, config, children){
	let propName;
	
	for (propName in config){
		// 예약어 등 유효성 검사
		props[propName] = config[propName];
	}

	const childrenLength = arguments.length - 2;
	if (chilrenLength === 1){
		props.children = children;
	}else if (childrenLength > 1){
		const childArray = Array(childrenLength);
		for (let i = 0; i < childrenLength; i++){
			childArray[i] = arguments[i + 2];
		}

		props.children = childArray;
	}
	
	if (type && type.defaultProps) {
		const defaultProps = type.defaultProps;
		for (propName in defaultProps) {
			if (props[propName] === undefined) {
				props[propName] = defaultProps[propName];
			}
		}
	}

	return ReactElement(
		type,
		key,
		ref,
		undefined,
		undefined,
		getOwner(),
		props,
		...
	)
}
```

```javascript
function ReactElement(
	type,
	key,
	_ref,
	self,
	source,
	owner,
	props,
	debugStack,
	debugTask
){
	let element;

	element = {
		$$typeof: REACT_ELEMENT_TYPE,
		type,
		key,
		ref,
		props
	}

	return element;
}
```

하나의 컴포넌트 -> 하나의 자바스크립트 객체로 표현하기 때문

### React 17
- React 17 이전 JSX는 Babel에 의해 `ReactElement`로 변환
	- `import React from 'react'` 필요
- `createElement` -> legacy API
	- 불필요하게 React를 import한다.
	- 클래스 컴포넌트를 사용했을 때 의미 있었지만, 함수 컴포넌트 등장으로 의미를 잃었다.
	- createElement는 애초에 JSX를 염두해두고 만든 것이 아니다.
	- 개발자들이 createElement를 사용하여 수동으로 작성할 수 있도록 의도한 건데 이제는 가치가 없다.
	- 성능상 이슈가 있다.
	- 리액트 학습에 불필요한 개념을 포함한다.
#### 개선된 JSX Transform
```javascript
function Title() {
  return <h1 className="title">Hello!</h1>;
}
```

```javascript
import { jsx as _jsx } from "react/jsx-runtime";

function Title() {
  return _jsx("h1", {
    className: "title",
    children: " Hello!"
  });
}
```

- `react/src/jsx/ReactJSXElement.js`
```
export function jsx(type, config, maybeKey) {
  let propName;

  const props = {};

  let key = null;
  let ref = null;
  
  // ...
  // ...
  
  // key 설정
  key = '' + maybeKey;
  
  // deprecated될 코드. 점진적인 변화를 위해 존재
  key = '' + config.key;
  
  
  // props 설정
  props[propName] = config[propName];

  
  return ReactElement(
    type,
    key,
    ref,
    undefined,
    undefined,
    ReactCurrentOwner.current,
    props,
  );
}
```

기본 구조 같고, children을 props로 전달한다는 점, maybeKey 추가 등의 변경
점진적으로 legacy 제거할 것으로 보임

## Reference
- https://velog.io/@sa02045/React-createElement-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0
