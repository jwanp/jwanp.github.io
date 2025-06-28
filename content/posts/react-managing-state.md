---
title: '[React] Managing State'
date: 2025-06-29T00:24:25+09:00
draft: false
tags: ['javascript', 'react']
categories: ['Tech']
featuredImage: /images/tech/React-managing-state.png
---

# React에서 State 다루기

React에서 State는 어떤 UI를 보여줄지 결정하는 핵심 요소입니다. 특히 중복되거나 모호한 State는 디버깅이 어렵고, 버그의 원인이 됩니다.

React 공식 문서에서는 상태 변수 관리, 유지보수 방식, 컴포넌트 간 상태 공유 방식 등에 대한 가이드라인을 제공합니다. 이번 글에서는 공식 문서를 참고하여 핵심 원리를 복습하고, 다시 살펴보면 좋을 정보들을 정리했습니다.

---

## Reacting to Input with State

🔗 https://react.dev/learn/reacting-to-input-with-state

UI를 조작하는 방법에는 크게 두 가지가 있습니다: **Imperative**와 **Declarative**.

- **Imperative(명령형)**: 택시 기사에게 계속 "우회전하세요", "좌회전하세요"와 같이 명령하는 방식.
- **Declarative(선언형)**: 목적지를 말하면 택시 기사가 스스로 판단해 도착하는 방식.

React 없이 DOM을 직접 조작하는 방식은 Imperative이며, React를 사용하여 상태에 따라 UI를 선언하는 방식은 Declarative입니다.

React에서 상태를 다룰 때는 다음과 같은 명령형 사고 순서를 따릅니다:

1. **Identify** 컴포넌트의 다양한 시각적 상태를 정의합니다.
2. **Determine** 상태 변화의 트리거를 파악합니다.
3. **Represent** 상태를 `useState`를 이용해 메모리에 표현합니다.
4. **Remove** 불필요한 상태 변수는 제거합니다.
5. **Connect** 이벤트 핸들러와 상태를 연결합니다.

특히 4번에서는 상태를 리팩토링하는 방법을 다루며, 가능한 상태 변수를 줄이고 하나로 통합하는 것이 좋다고 설명합니다.

예시:

```jsx
// 불필요하게 나뉜 상태
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);

// 하나의 상태로 통합
const [status, setStatus] = useState('typing'); // 'typing' | 'submitting' | 'success'
```

## Choosing the State Structure

- https://react.dev/learn/choosing-the-state-structure

상태변수를 잘 설계하는 것은 버그와 리팩터링 하는것을 훨씬 쉽게해줍니다. 따라서 공식문서에서는 상태변수를 설계하기 위한 5가지 원칙을 제시합니다.

1. **Group related state.** 항상 두개 이상의 상태변수를 함께 수정한다면 그 두개를 합친다.
2. **Avoid contradictions in state.** 몇개의 상태변수들이 서로 모순되는 상태가 있을 수 있다. 따라서 상태변수들간에 서로 모순이 없는지 잘 살펴본다.
3. **Avoid redundant state.** 많은 상태변수를 피해라. 렌더링 중에 이미 가지고 있는 상태변수를 활용해서 계산할 수 있는 변수라면 이를 새로운 상태 변수로 만드는 것을 지양.
4. **Avoid duplication in state.** 같은 정보가 여러 상태변수에 포함되어 있다면, 그 정보에 대해서 일관되게 유지하는 것이 어렵기 때문에 중복된 정보를 줄이자.
5. **Avoid deeply nested state.** 너무 깊게 중첩된 구조는 업데이트하기가 어렵기 때문에 flat한 방식으로 유지.

이 원칙들의 목표는 상태 변수들을 실수 없이 쉽게 업데이트하기 위함입니다.
각 원칙들에 대해서 짧은 예시들은 다음과 같습니다.

```jsx
// 1. Group related state.
const [x, setX] = useState(0);
const [y, setY] = useState(0);
// 위의 두개 변수는 항상 함께 update 됨으로 이렇게 하나로 합칠 수 있습니다.
const [position, setPosition] = useState({ x: 0, y: 0 });


// 2. Avoid contradictions in state.
const [text, setText] = useState('');
const [isSending, setIsSending] = useState(false);
const [isSent, setIsSent] = useState(false);
// isSending이 false 이면 항상 isSent를 true로 해줘야하는데 이를 까먹을 수도 있습니다.
// 이는 서로 모순을 불러옵니다. 따라서 두개를 합쳐서 다음과 같이 선언해야 합니다.
 const [status, setStatus] = useState('typing'); // typing | sending | sent

// 3. Avoid redundant state.
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [fullName, setFullName] = useState('');
// firstName과 lastName은 따로 입력을 받습니다.
// 하지만 fullName은 firstName과 lastName으로 렌더링 중에 계산할 수 있으므로 useState를 쓰지 않습니다.
const fullName = firstName + ' ' + lastName;

// 4. Avoid duplication in state.
const [items, setItems] = useState(initialItems);
const [selectedItem, setSelectedItem] = useState(items[0]);
// 위에서 처럼 하면 items와 selectedItem이 겹칩니다. 따라서 이 두개를 싱크를 계속 맞출때 까먹거나 몰라서 update를 못하는 상황이 대부분입니다. 따라서 그런 경우를 피하기 위해서 다음과 같이 해야합니다.
const [items, setItems] = useState(initialItems);
const [selectedId, setSelectedId] = useState(0);
const selectedItem = items.find(item => item.id === selectedId);

// 5. Avoid deeply nested state.
const [places, setPlaces] = useState({
  id: 0,
  title: '(Root)',
  childPlaces: [{
    id: 1,
    title: 'Earth',
    childPlaces: [{
      id: 2,
      title: 'Africa',
      childPlaces: [{
        id: 3,
        title: 'Botswana',
        childPlaces: []
      }, {
        id: 4,
        title: 'Egypt',
        childPlaces: []
// 다음과 같이 모두 nest된 형태로 두면 너무 복잡해진다. 따라서 다음과 같이 id로 분류해서 childPlaces를 표시한다.
const [places, setPlaces] = ({
  0: {
    id: 0,
    title: '(Root)',
    childIds: [1, 42, 46],
  },
  1: {
    id: 1,
    title: 'Earth',
    childIds: [2, 10, 19, 26, 34]
  },
```

추가적으로 **props를 state로 두면 안됩니다.** Props는 이미 일종의 state처럼 관리 되기 때문에 props가 바뀔때마다 UI가 변하도록 부모에서 관리하는 것이 원칙입니다.

만약 **props가 바뀌더라도 만약에 이를 state를 선언해서 쓰고 있다면, UI가 업데이트 되지 않을 것**임으로 이는 혼란을 초례합니다.

```jsx
function Message({ messageColor }) {
	const [color, setColor] = useState(messageColor); //이렇게 말고
	...
function Message({ messageColor }) {
	const color = messageColor; // 이렇게 써야합니다.

```

## Sharing State Between Components

- https://react.dev/learn/sharing-state-between-components

가끔 두개의 컴포넌트의 선언된 두개의 각각 다른 state가 항상 함께 업데이트되어야 합니다. 이럴때 두개를 가장 가까운 공통 부모 componenet으로 옮긴다음에 합쳐서 써야합니다. 이를 _lifting state up_ 이라고 부릅니다.

## Preserving and Resetting State

- https://react.dev/learn/preserving-and-resetting-state

### State is tied to a position in the render tree

react의 동작원리나 컴포넌트의 state가 언제 초기화 되는지 알려면 React는 [render tree](https://react.dev/learn/understanding-your-ui-as-a-tree#the-render-tree)구조에 의해서 이루어진다는 것을 알아야합니다. 즉 tree구조의 변화에 의해서 state가 초기화 되고 그대로 유지될 수 도 있습니다.

공식문서에는 다양한 UI의 예시를 들면서 render tree와 컴포넌트간의 상호작용의 이해를 돕고 있습니다.

아마 이것은 Counter가 각각 별개로 호출된 컴포넌트이기 때문에 다른 state를 가지고 있다고 생각할 것입니다. 하지만 이렇게 이해하기 보다는 다른 방식으로 이해를 해야합니다.

```jsx
import { useState } from 'react';

export default function App() {
  return (
    <div>
      <Counter /> // 이것의 state가 바뀌어도
      <Counter /> // 이것의 state는 바뀌지 않는다.
    </div>
  );
}
```

두개의 `Counter`는 state를 서로 공유하고 있지 않습니다. 이것을 서로 별개로 호출되서 따로 관리된다고 알기 쉽지만, **render tree에 서로 다른 곳에 위치하고 있기 때문에 state를 공유 하지 않는다고 이해해야 합니다.**

예를 들어서 다음 if문으로 분기한 서로 다른 위치에서 호출된 Counter는 state는 별개로 호출되었기 때문에 state를 공유 하지 않는다고 이해하기 쉽습니다.

```jsx
export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <Counter isFancy={true} />  // 여기에서 count가 2였다면
      ) : (
        <Counter isFancy={false} /> // isFancy === false 일때도 count는 2
      )}
```

하지만 두개의 Counter 컴포넌트는 부모에서 `count` 상태를 공유하지 않아도 `render tree`에서 같은 곳에 위치해 있기 때문에 전의 count 상태를 그대로 가져다가 씁니다.

그렇다면 다음은 어떨까요?
다음 컴포넌트는 tree상 같은 위치에 있고 심지어 한번밖에 호출 되지 않았습니다.

```jsx
export default function App() {
  const [showB, setShowB] = useState(true);
  return (
    <div>
      {showB && <Counter />} // showB가 바뀌었을때 count 상태 변수는 유지될까요?
```

`render tree`는 전에 렌더링 된 트리를 기반으로 컴포넌트를 봅니다. 따라서 만약 전 렌더링에서 `showB`가 `false` 였다면 Counter 위치에는 비어있었습니다. 따라서 해당 컴포넌트는는 사라지고 새로운 `Counter`를 호출하기 때문에 count변수는 초기화 됩니다.

마찬가지로 `render tree`의 같은 위치에서 컴포넌트를 렌더링 해도 **태그가 다르면 서로 다른 sub-tree으로 인식 되서 상태 변수가 초기화 됩니다.**

```jsx
export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <div> // div 태그 이고
          <Counter isFancy={true} />
        </div>
      ) : (
        <section> // section 태그 이기 때문에 서로 다른 트리로 인식된다.
          <Counter isFancy={false} />
        </section>
      )}
```

즉 각각의 tree 구조가 서로 다르다면 모든 상태 변수는 초기화 됨으로, 상태 변수를 유지하고 싶다면 모든 tag를 포함한 tree 구조를 일치 시켜야합니다.

### Resetting state at the same position

같은 위치에 컴포넌트를 렌더링 하면 상태 변수가 공유됩니다. 만약 **컴포넌트가 같은 위치에 렌더링 되지만 서로 다른 사람이라서 상태 변수는 따로 관리하고 싶다면** 어떻게 해야 할까요?

```jsx
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      // Taylor와 Sarah의 상태가 서로 공유 된다.
      {isPlayerA ? <Counter person="Taylor" /> : <Counter person="Sarah" />}
      <button
        onClick={() => {
          setIsPlayerA(!isPlayerA);
        }}>
        Next player!
      </button>
    </div>
  );
}
```

두가지 방법이 있습니다.

1. **두개의 컴포넌트를 서로 다른 위치에서 렌더링 한다.**

```jsx
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      // 서로 다른 위치에서 렌더링
      {isPlayerA && <Counter person="Taylor" />}
      // 분기로 처리하지 않고 위치를 바꾼다.
      {!isPlayerA && <Counter person="Sarah" />}
      <button
        onClick={() => {
          setIsPlayerA(!isPlayerA);
        }}>
        Next player!
      </button>
    </div>
  );
}
```

2. **각각의 State에 key를 부여한다.**

```jsx
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? <Counter key="Taylor" person="Taylor" /> : <Counter key="Sarah" person="Sarah" />}
      <button
        onClick={() => {
          setIsPlayerA(!isPlayerA);
        }}>
        Next player!
      </button>
    </div>
  );
}
```

## Extracting State Logic into a Reducer

- https://react.dev/learn/extracting-state-logic-into-a-reducer

하나의 state에 너무 많은 update 함수들이 있다면 관리하기 힘들고 다른 파일로 뺄 수 도 없어서 번거롭습니다.

React 공식문서에서는 이를 해결하기 위해서 reducer이라는 하나의 함수를 통해서 state update 로직을 관리하라고 제시합니다. 다만 해당 reducer를 사용하면 코드양이 늘어나고 구조가 조금 더 복잡해 집니다. 대신에 확장성이 좋고 state update 함수들을 한곳에서 관리할 수 있어서 디버깅 하기 좋습니다.

State 변경 함수를 reducer로 변경하는 것은 3가지 단계를 거칩니다.

1. **Move** from setting state to dispatching actions.
2. **Write** a reducer function.
3. **Use** the reducer from your component.

**Step 1: Move from setting state to dispatching actions**

```jsx
// 이렇게 하나의 태스크를 더하는 업데이트 함수를
function handleAddTask(text) {
  setTasks([
    ...tasks,
    {
      id: nextId++,
      text: text,
      done: false,
    },
  ]);
}

// 단지 type: 'added'으로 어떤 일이 일어났는지만 기제한다.
function handleAddTask(text) {
  dispatch({
    type: 'added',
    id: nextId++,
    text: text,
  });
}
```

**Step 2: Write a reducer function**

```jsx
// reducer 함수를 작성합니다.
// added 가 일어났을때 무었을 할지 기제 합니다.
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      // 항상 return 문으로 끝나야 합니다. (switch이기 때문에 default도 실행됨)
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

**Step 3: Use the reducer from your component**

```jsx
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import tasksReducer from './tasksReducer.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }
  // ... 그외의 함수들
  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

// tasksReducer.js
export default function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
  }
}
```

## Passing Data Deeply with Context

- https://react.dev/learn/passing-data-deeply-with-context

props를 사용하다보면 몇단계를 거처서 props 전달해야 하는 경우가 있습니다. 특히 프로젝트의 사이즈나 컴포넌트가 복잡하게 얽혀있다면 이를 관리하기 매우 어렵습니다.

이를 Context활용해서 해결 할 수 있습니다. Context를 활용하면 어느 한 컴포넌트의 자식들에서 공통적으로 쓰이는 변수를 한번에 관리 할 수 있습니다.

하지만 Context를 쓰기 전에 먼저

1. props를 작성하는 것부터 시작하세요. 왜냐하면 props를 사용함으로서 데이터 흐름을 더 정확하게 파악 할 수 있고 어떤 컴포넌트가 어떤 데이터를 쓰는지 더 명확히 구분 할 수 있기 때문입니다.
2. 컴포넌트를 추출하고 JSX를 자식 컴포넌트로 전달하는 것을 먼저 고려하세요. 이는 제가 전에 [Passing JSX as children](https://jwanp.github.io/posts/react-describing-the-ui/#passing-jsx-as-childrenhttpsreactdevlearnpassing-props-to-a-componentpassing-jsx-as-children-) 에 정리 해놨습니다.

### 왜 Context를 써야 하나?

다음과 같은 컴포넌트에서는 `Heading`에 `level` props가 필요합니다. 하지만 하나의 `<section>`에 속한 `Heading`은 모두 동일한 `level`을 활용합니다. 이를 Context를 활용하면 Section에서 한번에 관리 할 수 있습니다.

```jsx
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Section>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Section>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Section>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

### Context를 활용하는 방법

1. `createContext`를 활용해서 상태 변수를 만듭니다.

```jsx
// LevelContext.js
import { createContext } from 'react';

export const LevelContext = createContext(1);
```

2. 상태 변수가 필요한 곳에서 context를 가져옵니다.

```jsx
// Heading.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext); // level 변수를 가져옵니다.
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

3. 부모 컴포넌트에서 Context를 제공합니다.

```jsx
// Section.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ level, children }) {
  return (
    <section className="section">
      <LevelContext value={level}> // level context 으로 감쌉니다.
        {children}
      </LevelContext>
    </section>
  );
}

// App.js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section level={1}>
      <Heading>Title</Heading>
      <Section level={2}>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section level={3}>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Section level={4}>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

## Scaling Up with Reducer and Context

- https://react.dev/learn/scaling-up-with-reducer-and-context

앞에서 살펴보았던 reducer와 context를 결합해서 전역상태관리를 구현하는 법을 알아보겠습니다.

다음과 같은 과정을 통해서 reducer를 context와 결합할 수 있습니다.

1. **Create** the context.
2. **Put** state and dispatch into context.
3. **Use** context anywhere in the tree.

---

### Step 1: **Create** the context.

총 2개의 context를생성해야 합니다. Task를 담고 있는 `TasksContent`와 Tasks를 수정할 `TasksDispatchContext`를 생성합니다.

```jsx
// TasksContext.js
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

해당 컨텍스트에는 먼저 null을 할당합니다. 실제 값은 나중에 TaskApp 컴포넌트에서 Context으로 감싸줄때 주입합니다.

### Step 2: Put state and dispatch into context

context를 사용할 가장 상위 컴포넌트(`TasksApp`) 에서 reducer를 호출하여 생성된 상태변수(`tasks`)와 `dispatch`를 Context에 주입하여 `children`을 감싸줍니다.

```jsx
// TasksApp.jsx
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
  // ...
  return (
    <TasksContext value={tasks}>
      <TasksDispatchContext value={dispatch}>
        <AddTask />
        <TaskList />
      </TasksDispatchContext>
    </TasksContext>
  );
}

// reducer 함수
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false },
];
```

### Step 3: Use context anywhere in the tree

이제 `TasksApp` 하위 컴포넌트의 어디서든지 tasks와 dispatch를 불러와서 사용할 수 있습니다.

```jsx
// AddTask.jsx
import { useState, useContext } from 'react';
import { TasksDispatchContext } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useContext(TasksDispatchContext); // context를 불러와서 사용합니다.
  return (
    <>
      <input placeholder="Add task" value={text} onChange={(e) => setText(e.target.value)} />
      <button
        onClick={() => {
          setText('');
          dispatch({
            // reducer를 불러와서 실행
            type: 'added',
            id: nextId++,
            text: text,
          });
        }}>
        Add
      </button>
    </>
  );
}

let nextId = 3;
```

### 감싸는 Context를 모두 한곳에 모으기

만약 Context가 적다면 가장 상위 컴포넌트에서 children을 감싸면 됩니다. 하지만 이것이 점점 많아진다면 관리하기 힘듦으로 하나의 파일에 관리 할 수도 있습니다.

```jsx
// TasksProvider.js

import { createContext, useContext, useReducer } from 'react';
// context 생성
const TasksContext = createContext(null);
const TasksDispatchContext = createContext(null);

// Provider 컴포넌트
export function TasksProvider({ children }) {
  // reducer 생성
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
  // context에 reducer를 전달
  return (
    <TasksContext value={tasks}>
      <TasksDispatchContext value={dispatch}>{children}</TasksDispatchContext>
    </TasksContext>
  );
}
// 상태관리 변수 불러오는 훅
// const tasks = useTasks(); 나중에 이런식으로 사용
export function useTasks() {
  return useContext(TasksContext);
}
// 리듀서 불러오는 훅
// const dispatch = useTasksDispatch(); // 나중에 이런식으로 사용
export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

// App.js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```
