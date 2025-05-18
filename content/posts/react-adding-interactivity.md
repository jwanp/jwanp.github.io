---
title: '[React] Adding Interactivity'
date: 2025-05-18T16:55:10+09:00
draft: false
categories: ['Tech']
featuredImage: /images/tech/React-adding-interactivity.png
---

_Illustrated by [Rachel Lee Nabors](https://nearestnabors.com/)_

{{< line_break >}}

## Adding Interactivity

React의 함수는 기본적으로 pure해야 한다. 하지만 우리는 사용자의 상호작용에 의해서 함수가 렌더링 된 이후에도 값을 바꾸거나 어떤 동작을 수행하고 싶을때가 있다. 해당 React [공식문서의 Adding Interactivity](https://react.dev/learn/adding-interactivity) 파트에서는 이러한 값들을 어디에서 어떻게 처리해야 하는지 알려준다.

대표적으로 **event handler**들은 purity를 유지할 필요가 없다. 따라서 이러한 event handler 함수 안에 뭔가를 바뀌는 것들을 넣어 주면 좋다. 예를 들어서 타이핑에 따라서 변수의 값을 바꾼다던지, 버튼을 눌렀을때 함수 바깥에 있는 리스트를 바꾸는 등의 행동을 할 수 있다.

여기에서 정리한 것은 공식문서의 내용을 모두 정리한 것이 아니라 내가 생각했을때에 까먹기 쉽거나 했갈릴 수 있는 내용이어서 다시한번 읽어보면 좋을 만한 내용들을 정리 한 것이다.

중요도 표시는 더 깊게 공부하면 좋을만한 내용이지 리액트 개념자체의 중요도를 나타낸 것은 아니다.

{{< line_break >}}

### [Adding event handlers](https://react.dev/learn/responding-to-events#adding-event-handlers)

button 이나 컴포넌트에 event handler를 더할때
호출하면 안되고 event handler를 전달해야 한다.

| passing a function               | calling a function                 |
| -------------------------------- | ---------------------------------- |
| `<button onClick={handleClick}>` | `<button onClick={handleClick()}>` |

React는 해당 컴포넌트가 렌더링 될때 {} 안에 있는 코드를 바로 실행 한다. 만약 함수를 호출하는 코드를 적으면 렌더링 될때 해당 함수가 바로 실행 된다.

{{< line_break >}}

### [Naming conventions](https://react.dev/learn/responding-to-events#naming-event-handler-props)

event handler의 **함수의 이름**을 지을때는 **handle**으로 시작한 event의 이름을 지어야 한다.
event handler를 **prop으로** 넘겨 줄때에 그 prop의 이름은 **on**으로 시작하여 이름을 지어야한다.

```tsx
function PlayButton({ movieName }) {
  function handlePlayClick() {
    alert(`Playing ${movieName}!`);
  }

  return <Button onClick={handlePlayClick}>Play "{movieName}"</Button>;
}
```

{{< line_break >}}

### [Event propagation](https://react.dev/learn/responding-to-events#event-propagation)

이건 기본 브라우저의 행동 중 하나인 [event bubbling](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/Event_bubbling)과 같다. 즉 이벤트 핸들러가 계속해서 부모를 참조해서 실행한다. 예를 들어서 div 안에 button이 있고 button 과 div 모두 onClick event handler가 등록되어 있다면 먼저 button의 event handler를 실행 시키고 다음으로 div의 onClick event handler를 실행 시킨다.

```jsx
export default function Toolbar() {
  return (
    <div
      className="Toolbar"
      onClick={() => {
        alert('You clicked on the toolbar!');
      }}>
      <button onClick={() => alert('Playing!')}>Play Movie</button>
      <button onClick={() => alert('Uploading!')}>Upload Image</button>
    </div>
  );
}
```

- Playing! => You clicked on the toolbar! 순서대로 콘솔창에 프린트된다.

{{< line_break >}}

- 따라서 이를 방지 하기 위해서는 다음과 같이 `(e)=>{e.stopPropagation();}`을 작성해 줘야 한다.

```jsx
<button
  onClick={(e) => {
    e.stopPropagation();
    onClick();
  }}>
  {children}
</button>
```

{{< line_break >}}

만약 이벤트는 방지하고 싶은데 이벤트가 실행됐는지 알고 싶을때가 있으면 함수 마지막에 Capture을 써주면 해당 함수가 가장 먼저 실행된다.

```jsx
<div
  onClickCapture={() => {
    /* this runs first */
  }}>
  <button onClick={(e) => e.stopPropagation()} />
  <button onClick={(e) => e.stopPropagation()} />
</div>
```

{{< line_break >}}

### [Prevent Default behavior](https://react.dev/learn/responding-to-events#preventing-default-behavior)

browser에서 행동하는 기본 동작들이 있다. 예를 들어서 form태그 안의 button을 클릭하면 페이지를 자동으로 리로드 하는데, 이를 방지하기 위해서는 `e.preventDefault()` 함수를 실행 하면 된다. browser의 기본 동작은 [Preventing default behavior](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/Events#preventing_default_behavior)에서 확인 할 수 있다.

```jsx
export default function Signup() {
  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        alert('Submitting!');
      }}>
      <input />
      <button>Send</button>
    </form>
  );
}
```

---

지금 까지는 event handler를 사용할때에 까머다시 상기하면 좋은 내용들을 정리한 내용이 었다.
이러한 event handler를 통해서 UI를 보여주는 함수가 렌더링된 이후에도 무언가를 바꿀 수 있다. 이러한 바꾸는 값들은 보통 `useState()` 안에 저장한다.

### [Meet your first Hook](https://react.dev/learn/state-a-components-memory)

hook은 use으로 시작하는 함수들이다. 이러한 hook들은 React가 렌더링 되는 동안만 사용할 수 있다. 따라서 이러한 훅들은 컴포넌트의 최상위 부모 혹은 해당 훅을 선언한 곳에서만 사용할 수 있다.

{{< line_break >}}

### [Giving a component multiple state variables](https://react.dev/learn/state-a-components-memory)

State를 선언할때 서로 상관이 없고 따로 동작한다면 두개의 다른 useState를 선언해도 된다. 하지만 항상 **둘이 함께 업데이트 되는 변수라면 object안에 함께 선언하는 것이 좋다.**

왜냐하면 setState를 할때마다 재렌더링이 일어나는데, 두개의 변수가 동시에 업데이트 되면 두번의 다른 렌더링이 일어나기 때문이다. 따라서 두개의 변수를 object안에 함께 선언 함으로 변수가 업데이트 되었을때 한번만 재 랜더링이 일어나게 하는 것이 좋다.

추가 적인 정보는 [Choosing the State Structure](https://react.dev/learn/choosing-the-state-structure) 에서 확인 할 수 있다.

{{< line_break >}}

### [How does React know which state to return?](https://react.dev/learn/state-a-components-memory#how-does-react-know-which-state-to-return) ⭐️⭐️

React의 훅이 어떠한 방식으로 동작하는지 나와있다. HTML가 JS코드가 주어져 있고 [React Hooks: Not Magic, Just Arrays.](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e) 글을 읽어 보면 좋다고 한다.

{{< line_break >}}

### [State is isolated and private](https://react.dev/learn/state-a-components-memory#state-is-isolated-and-private)

State를 여러번 호출하더라도 공유되지 않는다. 만약 같은 hook으로 두개의 변수를 선언 했더라도 두개의 변수는 완전 다르게 동작한다. 따라서 만약에 두개의 컴포넌트에서 하나의 hook 변수를 공유하고 싶다면, 가장 가까운 부모 컴포넌트에서 hook을 선언한뒤에 props으로 넘겨 줘야한다.

{{< line_break >}}

### [React commits changes to the DOM](https://react.dev/learn/render-and-commit#step-3-react-commits-changes-to-the-dom)

React app에서의 화면 변화는 항상 세가지의 단계로 이루어져 있다.

1. Trigger: 어떠한 행동으로 인해서 값들이 바뀐다.
2. Render: 어떠한 값들이 바뀌었는지 파악하여 업데이트 되어야 하는 컴포넌트를 호출한다.
3. Commit: 가장 최근 DOM과 비교하여 최소한의 DOM업데이트를 진행한다.

이때 tag기준으로 컴포넌트의 다른 값들이 변경되어도 해당 tag에 영향이 없다면 update하지 않는다.
예를 들어서 `Clock`컴포넌트의 `time` props가 바뀌더라도 input에 있는 값은 그대로 남는다.

```jsx
export default function Clock({ time }) {
  return (
    <>
      <h1>{time}</h1>
      <input />
    </>
  );
}
```

{{< line_break >}}

### [Rendering takes a snapshot in time ](https://react.dev/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time)

React의 컴포넌트의 **state들은 하나의 snapshot**처럼 여겨진다.

이러한 특성으로 인해서 특히 다음과 같은 사항을 조심해야한다. 다음과 같은 Counter 컴포넌트 안의 버튼을 클릭했을때 setState를 3번했지만 number은 여전히 1이다. **왜냐하면 3개의 number모두다 0이기 때문이다.**

```jsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button
        onClick={() => {
          // 클릭하면 다음 렌더에 number = 1 이 할당되어 있다.
          setNumber(number + 1);
          setNumber(number + 1);
          setNumber(number + 1);
        }}>
        +3
      </button>
    </>
  );
}
```

즉 다음과 같이 실행 되는 것이다.

```jsx
<button
  onClick={() => {
    setNumber(0 + 1);
    setNumber(0 + 1);
    setNumber(0 + 1);
  }}>
  +3
</button>
```

다음과 같은 경우도 마찬가지로 alert에 5가 아니라 0이 뜬다.

```jsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button
        onClick={() => {
          setNumber(number + 5);
          alert(number); // 0
        }}>
        +5
      </button>
    </>
  );
}
```

하지만 만약에 re-render하기 전에 가장 최신의값(re-render가 된뒤에 가져올 값)을 가져오려면 어떻게 해야 할까?

updater function을 이용하면 된다. 이는 `state => state + 1` 형태로 쓰며 setState안에 state를 쓰는 것이 아니라 해당 함수를 쓰면 state안에 있는 queue에 접근해서 최신 값을 불러 온다.

```jsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button
        onClick={() => {
          setNumber((n) => n + 1); // 1 을 Queue에 더한다.
          setNumber((n) => n + 1); // 2 을 Queue에 더한다.
          setNumber((n) => n + 1); // 3 을 Queue에 더한다.
        }}>
        +3
      </button>
    </>
  );
}
```

**updater function**의 이름은 해당 변수의 처음 나오는 글자들로 해주는 것이 일반적이다.

```jsx
setEnabled((e) => !e);
setLastName((ln) => ln.reverse());
setFriendCount((fc) => fc * 2);
```

---

{{< line_break >}}

### [Updating Objects in State](https://react.dev/learn/updating-objects-in-state)

- **immutable:** read-only 값들로서 변수의 값들을 바꿀 수 없다. Javascript에서는 numbers, strings, boolean들이 있다.

- **mutation**: 변수의 값들을 바꾸는 것들을 의미한다. 예를들어 Object나 Array안의 있는 값들을 바꿀 수 있다.

`const [position, setPosition] = useState({ x: 0, y: 0 });` 이러한 object가 있을때
`position.x = 5`으로 해당 object안의 속성들을 바꾸는 것을 **mutation**이라고 한다.

따라서 React의 state가 snapshot처럼 동작하게 하려면 우리는 state에 Javascript **Object를 할당 했더라도 read-only 처럼 여겨야 한다.**

즉 state값들을 업데이트 하고 이로 인해 re-render를 trigger하고 싶다면, **mutation을 쓰는 대신에 새로운 Object를 할당해 줘야한다.**

```jsx
// ❎ screen 업데이트 안됨
onPointerMove={e => {
	position.x = e.clientX;
	position.y = e.clientY;
}}

// ✅ 잘 동작함
onPointerMove={e => {
	setPosition({
		x: e.clientX,
		y: e.clientY
	});
}}
```

{{< line_break >}}

#### [Using a single event handler for multiple fields](https://react.dev/learn/updating-objects-in-state#using-a-single-event-handler-for-multiple-fields)

우리는 object안에 있는 다른 값들은 유지하고 하나의 속성만 바꾸고 싶을때 `...`(shallow copy - 1 level)을 이용해서 새로운 object를 할당 할 수 있다.

이때 속성의 이름마다 event handler를 작성해야 해서 번거롭다. 하지만 []를 이용하여 dynamic name으로 특정 속성을 지정할 수 있다. 특히 form data를 수정할때 편리한 것 같다.

```jsx
export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com',
  });

  // event handler함수 하나만으로 모든 이름을 수정한다.
  function handleChange(e) {
    setPerson({
      // shallow copy (한 단계만 복사함으로 nested object는 다시 해줘야함)
      ...person,
      // dynamic name : 값
      [e.target.name]: e.target.value,
    });
  }

  return (
    <>
      <label>
        First name:
        <input name="firstName" value={person.firstName} onChange={handleChange} />
      </label>
      <label>
        Last name:
        <input name="lastName" value={person.lastName} onChange={handleChange} />
      </label>
      <label>
        Email:
        <input name="email" value={person.email} onChange={handleChange} />
      </label>
      <p>
        {person.firstName} {person.lastName} ({person.email})
      </p>
    </>
  );
}
```

만약 이렇게 여러번 nested된 object들을 copy하기 번거롭다면 [Immer](https://github.com/immerjs/use-immer) 라이브러리를 사용

{{< line_break >}}

### [Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state) ⭐️⭐️

React에서 Array를 업데이트 할때도 Object와 마찬가지로 해줘야한다. 그렇게 하기 위해서 `filter()` 과 `map()`과 같은 mutate하지 않는 함수들을 활용할 수 있다.

|           | avoid (mutates the array)           | prefer (returns a new array)                                                                                        |
| --------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| adding    | `push`, `unshift`                   | `concat`, `[...arr]` spread syntax ([example](https://react.dev/learn/updating-arrays-in-state#adding-to-an-array)) |
| removing  | `pop`, `shift`, `splice`            | `filter`, `slice` ([example](https://react.dev/learn/updating-arrays-in-state#removing-from-an-array))              |
| replacing | `splice`, `arr[i] = ...` assignment | `map` ([example](https://react.dev/learn/updating-arrays-in-state#replacing-items-in-an-array))                     |
| sorting   | `reverse`, `sort`                   | copy the array first ([example](https://react.dev/learn/updating-arrays-in-state#making-other-changes-to-an-array)) |

공식문서에서는 각 방법에 대한 예시를 보여주지만 `slice`를 활용한 **inserting** 예시만 가지고 왔다.

```jsx
const nextArtists = [
  // Items before the insertion point:
  ...artists.slice(0, insertAt),
  // New item:
  { id: nextId++, name: name },
  // Items after the insertion point:
  ...artists.slice(insertAt),
];
setArtists(nextArtists);
```
