---
title: '[React] Describing the UI'
date: 2025-05-09T09:54:02+09:00
draft: false
tags: ['javascript', 'react']
categories: ['Tech']
featuredImage: /images/tech/React-describing-the-ui.png
---

{{< line_break >}}

해당 내용은 React 공식문서 내용을 복습한 것입니다. 읽어보면서 기초적인 내용도 있지만 간과하고 넘어갔던 부분들도 있었습니다. 무엇을 하든지 항상 기초 위에서 하면 좋습니다. 기초가 탄탄하면 복잡한 개념도 더 쉽게 이해할 수 있고, 문제를 해결할 때도 본질에 집중할 수 있기 때문입니다. 특히 React처럼 컴포넌트 중심의 UI 프레임워크에서는 작은 개념 하나하나가 전체 구조와 흐름에 큰 영향을 미칠 수 있으므로, 문서에 나와 있는 기본 개념들을 꼼꼼히 짚고 넘어가는 것이 중요하다고 느꼈습니다.

{{< line_break >}}

제가 정리한 내용들은 **Describing the UI** 섹션에서 다른 분들이 까먹고 지나갔을 법한 내용들, 혹은 다시 상기하면 좋겠다 싶은 내용들을 중심으로 구성했습니다.

{{< line_break >}}

## Describing the UI

### [What browser sees](https://react.dev/learn/your-first-component#what-the-browser-sees)

컴포넌트 이름은 무조건 Capital letter 이어야 한다. 왜냐하면 React가 컴포넌트를 일반 브라우저 태그와 구분하여 알게 하기 위함이다.

- `<section>` is lowercase, so React knows we refer to an HTML tag.
- `<Profile />` starts with a capital `P`, so React knows that we want to use our component called `Profile`.

{{< line_break >}}

### [Nesting and organizing components](https://react.dev/learn/your-first-component#nesting-and-organizing-components)

컴포넌트안에 다른 컴포넌트를 넣을 수 있지만 컴포넌트의 정의는 있으면 안된다.

```jsx
export default function Gallery() {
  // 🔴 Never define a component inside another component!
  function Profile() {
    // ...
  }
  // ...
}
```

{{< line_break >}}

### [Why do multiple JSX tags need to be wrapped?](https://react.dev/learn/writing-markup-with-jsx#why-do-multiple-jsx-tags-need-to-be-wrapped)

- JSX 함수는 리턴할때 무조건 root 태그 하나로 감싸줘야한다. 이렇게 하는 이유는 JSX 함수는 하나의 javascript object를 반환하도록 되어 있기 때문이다. 만약 여러개의 태그를 나란히 반환하면 여러개의 javascript object를 반환하는 것이 된다.

{{< line_break >}}

### camelCase ~~all~~ most of the things!

- [camelCase ~~all~~ most of the things!](https://react.dev/learn/writing-markup-with-jsx#3-camelcase-salls-most-of-the-things)

JSX에서는 기존에 사용하던 태그 속성들의 이름을 camelCase으로 해줘야한다. 우리는 JSX에서 태그들의 속성들을 javascript object으로 활용한다. 하지만 javascript에서는 변수에 관련해서 dashes를 포함하면 안된다던지, `class`이름을 사용못하게 하는 naming-rule들이 있다.

예를 들어서 `stroke-width` 대신에 `strokeWidth`를 써야하고 `class`대신에 `className`을 써야한다.
추가 적인 정보는 [list of dom component props](https://react.dev/reference/react-dom/components/common)에서 확인 할수 있다.

{{< line_break >}}

### [Where to Use curly braces](https://react.dev/learn/javascript-in-jsx-with-curly-braces#where-to-use-curly-braces)

JSX에서의 `{}`는 특별한 의미를 갖고 있다. 왜냐하면 HTML태그 안에서도 javascript를 쓸 수 있기 때문이다. 그렇다면 어디서 `{}`를 써야할까?

2. JSX 태그 안에서 javascript 값을 문자형태로 변환하기 위해서 쓸 수 있다. `<h1>{name}'s To Do List</h1>`
3. JSX 태그의 속성에 값을 부여하기 위해서 `=` 사인 바로뒤에 쓸 수 있다.`src={avatar}`는 avatar 변수를 불러오지만 `src="{avatar}"`는 `=` 바로뒤에 `{}`가 나오지 않기 때문에 `"{avatar}"`를 반환한다.

{{< line_break >}}

### [Using “double curlies”: CSS and other objects in JSX](https://react.dev/learn/javascript-in-jsx-with-curly-braces#using-double-curlies-css-and-other-objects-in-jsx)

가끔 `{{}}`이 보이면 당황할때가 있다. `{}`만 쓰면 될것 같은데 왜 두개가 필요할까?
첫번째는 javascript 문법임을 알리는 것이고, 두번째는 object를 표현하기 위함이다.
예를 들어서 style 태그에 javascript object를 넣기 위해서는 다음과 같이 해야한다.

```jsx
<ul style={{
      backgroundColor: 'black',
      color: 'pink'
}}>
```

{{< line_break >}}

### [Specifying a default value for a prop](https://react.dev/learn/passing-props-to-a-component#specifying-a-default-value-for-a-prop)

JSX 컴포넌트에서 props를 받아올때 default value를 설정할 수 있다.

```jsx
function Avatar({ person, size = 100 }) {
  // ...
}
```

이때는 `size={null}`이거나 `size={false}`라고 100이 할당되는 것이 아니라 `size={undefined}`일때만 `size={100}` 이 할당된다.

{{< line_break >}}

### [Passing JSX as children](https://react.dev/learn/passing-props-to-a-component#passing-jsx-as-children) ⭐️

props를 변수 전달 용도로만 사용하는 것이 아니라 코드를 더 간결하게 만들고 최상위 컴포넌트에서 대부분의 변수들을 관리 하기 위한 용도로 `{children}`를 활용할 수 도 있다.

대부분의 사람들은 최상위 컴포넌트에서 다른 컴포넌트를 관리하는게 아니라 자녀 컴포넌트에서 또 다른 컴포넌트를 import하는 방식으로 하곤 한다. 하지만 이렇게 하게 되면 어떠한 컴포넌트가 쓰였는지 계속 타고들어가서 확인해야 하거나 props를 통째로 넘겨줘야하는 일이 생긴다.

```jsx
// Profile.js
import Avatar from './Avatar.js'
function Card(props){ // 부모 컴포넌트
  return(
    <div className="card">
      <Avatar {...props}/>
    </div>
  )
}

export default function Profile(){ // 최상위 컴포넌트
  return(
    <div className="profile">
      <Card person={person} size={size}/>
    </div>
  )
}

// Avatar.js
import { getImageUrl } from './utils.js';
export default function Avatar({ person, size }) { // 자식 컴포넌트
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}
```

하지만 이렇게 하게 되면 Card에서 사용되지 않은 props를 가지고 있어야 하고 Profile에서 받은 props가 어디에서 쓰였는지 확인하기 위해서 Card를 거쳐야 한다. 따라서 우리는 다음과 같이 할 수 있다.

```jsx
// 위의 컴포넌트 처럼 하고 싶을때 이렇게 Card안에서 children 컴포넌트를 props로 받아서 전달해 주면된다.
import Avatar from './Avatar.js';

function Card({ children }) {
  // 부모 컴포넌트
  return <div className="card">{children}</div>;
}

export default function Profile() {
  // 최상위 컴포넌트
  return (
    <Card>
      <Avatar
        size={100}
        person={{
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2',
        }}
      />
    </Card>
  );
}
```

{{< line_break >}}

### **Don’t put numbers on the left side of `&&`**

우리는 jsx 안에 `&&`를 활용하여 다음과 같은 코드를 넣곤 한다. `messageCount && <p>New messages</p>`. 이는 `messageCount가` `true`라면 뒤로 넘어가고 `false`라면 앞에서 멈춘다.

예를 들어서 `messageCount`가 `undefined`이거나 `null`이라면 JSX는 해당 공간에 아무것도 보여 주지 않는다.

하지만 `messageCount`가 0일때는 우리의 예상과는 다른 방식으로 동작한다.

일단 0은 false이므로 뒤로 넘어가지 않는 것은 같다. 하지만 그뒤에 아무것도 보여주지 않아야 하는데 0은 숫자이므로 0을 보여준다. 즉 JSX는 `undefined` 이거나 `null`일때는 해당 값을 보여주지 않지만 0은 보여준다.
**falsy인 값들 JSX동작 방식**

- `undefined`: 안보여줌
- `null` : 안보여줌
- 0 : 보여줌

### [Rendering data from arrays](https://react.dev/learn/rendering-lists#rendering-data-from-arrays)

javascript 문법을 써서 리스트를 filter 혹은 map을 써서 렌더링 할 수 있다. 여기에서 조금 특이했던 부분은, 나는 원래 return문 안에서 이러한 함수들을 써줬는데 여기에서는 변수에 담아서 보여줘서 가독성 측면에서는 어떤게 더 좋을지 생각해보는 계기가 되었다.

```jsx
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const chemists = people.filter((person) => person.profession === 'chemist');
  const listItems = chemists.map((person) => (
    <li>
      <img src={getImageUrl(person)} alt={person.name} />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        known for {person.accomplishment}
      </p>
    </li>
  ));
  // 결과 보여주기
  return <ul>{listItems}</ul>;
}
```

{{< line_break >}}

### [Purity: Components as formulas](https://react.dev/learn/keeping-components-pure#purity-components-as-formulas)

purity는 어떠한 input이 있을때 항상 같은 output만 나와야 하는 것을 의미한다. React의 컴포넌트들은 이러한 purity를 고수해야한다.

특별하게 봤던 것은 컴포넌트들이 어떠한 순서로 렌더링 되는지 예측하면 안된다는 것이다. 즉 컴포넌트 자체에 대해서만 생각해야지 다양한 컴포넌트들간의 렌더링 순서혹은 상호작용에 대해서 생각 하면 안된다. 또한 컴포넌트가 존재하기 전에 있는 변수를 조작하면 안된다.

```jsx
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

이렇게 할 수 있는 이유는 props를 input으로 받고 JSX를 return하기 때문이다.

이러한 purity는 production환경에서는 영향이 없는 `<React.StrictMode>`를 사용해서 확인 할 수 있는데, 이는 같은 React컴포넌트를 두번씩 렌더링 해서 두개의 결과가 같은지 확인한다.

**하지만,** 렌더링하는 도중에 생성된 변수를 바꾸는 것은 가능하다. 이것을 **“local mutation”** 이라고 부른다.

```jsx
// 가능
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = []; // 렌더링 하는 도중에 생성된 변수
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />); // local mutation
  }
  return cups; // 렌더링 하는 도중에 바뀐다.
}
```

{{< line_break >}}
**그럼에도 불구하고,** 컴포넌트가 렌더링이 된뒤에 애니메이션, 화면 변화, 데이터 변화 등이 일어날 수도 있다. 이것을 **side effects**라고 부르는데, 이는 대부분 **event handler**에 의해서 일어난다.

우리는 렌더링 도중에 event handler를 등록함으로 purity를 유지하는데에 큰 문제는 없지만, 적당한 event handler를 찾지 못하는 경우에는 **마지막 옵션으로 useEffect**를 쓸것을 제시한다.
