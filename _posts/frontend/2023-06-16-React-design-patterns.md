---
title: "React design patterns"
last_modified_at: 2023-06-16T18:51:02-05:00
categories:
  - Blog
tags:
  - React
  - Design Pattern
---

> React design Principles ( [리엑트 디자인원칙](https://ko.reactjs.org/docs/design-princlples.html)
> "우리는 이 문서를 작성함에 있어서 React는 무엇을 하고 무엇을 하지 않는지, 우리의 개발 철학은 무엇인지에 대해 우리가 어떻게 결정하는지 여러분이 더 잘 알수 있도록 했습니다.
> 우리는 커뮤니티에 기여하는 것을 즐거워하지도 않지만 이 원칙들 중 하나 이상을 위반하는 길은 선택하지 않을 것입니다"

## List of React design patterns 
- Reference site
  - [https://reactpatterns.com/](https://reactpatterns.com/)
  - [https://www.uxpin.com/studio/blog/react-design-patterns/]( https://www.uxpin.com/studio/blog/react-design-patterns/)
  - [https://velog.io/@dnr6054/%EC%9C%A0%EC%9A%A9%ED%95%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%8C%A8%ED%84%B4-5%EA%B0%80%EC%A7%80](https://velog.io/@dnr6054/%EC%9C%A0%EC%9A%A9%ED%95%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%8C%A8%ED%84%B4-5%EA%B0%80%EC%A7%80)
  - [https://velog.io/@holim0/React-Design-Pattern](https://velog.io/@holim0/React-Design-Pattern)
  - [https://blog.logrocket.com/react-component-design-patterns-2022/](https://blog.logrocket.com/react-component-design-patterns-2022/)
  - [https://blog.openreplay.com/3-react-component-design-patterns-you-should-know-about](https://blog.openreplay.com/3-react-component-design-patterns-you-should-know-about)
  - [https://wit.nts-corp.com/2021/08/11/6461](https://wit.nts-corp.com/2021/08/11/6461)
  - [https://medium.com/@konstankino/2019-reactjs-best-practices-design-patterns-516e1c3ca06a](https://medium.com/@konstankino/2019-reactjs-best-practices-design-patterns-516e1c3ca06a)
  - [https://blog.bitsrc.io/new-react-design-pattern-return-component-from-hooks-79215c3eac00](https://blog.bitsrc.io/new-react-design-pattern-return-component-from-hooks-79215c3eac00)
 
  <br/>
 
  |패턴이름|설명|비고|
  |--------|----|----|
  |Stateless Components pattern|Stateless components are called "functional components or presentational components| |
  |Conditional Rendering pattern| | |
  |Render Props pattern| | |
  |Controlled Components pattern| | The control props pattern과 같은 의미|
  |React Hook pattern Custom Hook pattern|훅을 이용하면 개발자들은 Class 바탕의 코드를 작성할 필요 없이 상태 값과 여러 React의 기능을 사용할 수 있음|Hook은 functional component에서만 사용할 수 있음 (클래스 컴포넌트에서는 사용불가함)|
  |High-order component| | |
  |The provider pattern| | |
  |The compound components pattern| |React bootstrap, React materials-ui, Reach UI 등에서 사용됨|
  |The presentational and container component pattern| | |
  |The atomic pattern|컴포넌트를 5가지 레이어로 구분하여 재사용성 향상을 도모함(Atom, Molecules, Organisms, Template, Pages)|Reusability 향상에 중점|
  |Return component from Hooks| |https://blog.bitsrc.io/new-react-design-pattern-return-component-from-hooks-79215c3eac00|

1. Stateless components
   - You cannot reach "this.state" inside it. Stateless components are also called functional components or presentational components.
   - The best practice is to let parent components keep as much state as possible and meke stateless child components (Data can be passed down via props

2. Conditional Rendering
   특정 조건이 충족되면 해당 결과가 나오게 작업을 위해 사용하는 방법 (예, 사용자가 로그인을 하면 "welcome back!" 이라는 문구가 나오게 하고 로그인이 안된 사용자에게는 "Please sign up"이라는 문구가 나오게 할 수 있음)
   
```
  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }
 
    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
 
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}
```

3. Render Props
   - Render props are abailable in React to help us solve the problem of log repetition
   - Technique for sharing code between React components using a prop whose value is a function ( 서로 다른 컴포넌트에서 동일한 state를 공유할 수 있고, 각 컴포넌트 내부의 랜더링 로직을 구현하는 대신 function prop를 사용하여 랜더링 항목을 결정 할 수 있음) → (  https://ko.reactjs.org/docs/render-props.htm )
   - 좋은 예시: React router, Formik, Downshift

```
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}
 
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }
 
  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }
 
  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
 
        {/*
          <Mouse>가 무엇을 렌더링하는지에 대해 명확히 코드로 표기하는 대신,
          `render` prop을 사용하여 무엇을 렌더링할지 동적으로 결정할 수 있습니다.
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}
 
class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

4. Controlled components
   - 제어 컴포넌트(Controlled Components)는 사용자의 입력을 기반으로 자신의 state를 onChange()를 이용해 데이터로 관리하고 업데이트하는 컴포넌트입니다.  
   - 쉽게 말해 데이터와 UI를 완전히 동기화된 상태로 사용자 입력에 즉시 반응할 수 있는 컴포넌트를 뜻합니다  
   - 보통 제어 컴포넌트(Controlled Components)를 사용하면, input의 값은 항상 React state에 의해 결정됩니다. 코드를 조금 더 작성해야 한다는 의미이지만, 다른 UI 엘리먼트에 input의 값을 전달하거나 다른 이벤트 핸들러에서 값을 재설정할 수 있습니다. 기본적으로 React 양식은 제어 및 비 제어 컴포넌트를 모두 지원하지만, 제어 컴포넌트를 사용하는 것을 적극 추천합니다  
     - https://ko.reactjs.org/docs/uncontrolled-components.html  
   - 대부분 경우 폼을 구현하는데 제어 컴포넌트를 사용하는 것이 좋습니다. 제어 컴포넌트에서 폼 데이터는 React 컴포넌트에서 다루어집니다. 대안인 비제어 컴포넌트는 DOM 자체에서 폼 데이터가 다루어집니다  

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef();
  }
 
  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value);
    event.preventDefault();
  }
 
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={this.input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

5. React Hook
   - [사용가능한 전체목록](https://reactjs.org/docs/hooks-reference.html)  
   -  필수 사용을 고려해야 하는 Hook: useState, useEffect, useContext, useCallback, useMemo, useRef
  
6. Higher order component (HOC)
   - The higher order component (or HOC) is an advanced React pattern used for reusing component logic across our application. The HOC pattern is useful for cross-cutting concerning features that require sharing of component logic across our application.(ex: authorization, logging, and data retrieval)  
   - HOC란 리액트 컴포넌트를 아규먼트로 받아서 새로운 리액트 컴포넌트를 리턴하는 함수이다. 코드의 반복을 줄이고, 컨테이너 컴포넌트(비즈니스 로직 담당)와 presentational 컴포넌트(뷰 담당)를 분리하기 위한 목적으로도 쓰인다. (https://developer-alle.tistory.com/301)

```
import React, {Component} from 'react';
 
const higherOrderComponent = (DecoratedComponent) => {
  class HOC extends Component {
    render() {
      return <DecoratedComponent />;
    }
  }
  return HOC;
};
 
// another example
...
import toggle from '../hocs/toggle';
  
const NaviItem = ({to, text, action}) => {
  const onClickAnchor = e => {
    if(action) {
      e.preventDefault();
      e.stopPropagation();
      action();
    }
  }
  return (
    <li className="nav-item">
      <a href={to} onClick={onClickAnchor} className="nav-link">
        {text}
      </a>
    </li>
  )
}
  
export default toggle(NaviItem);
 
function toggle(WrappedComponent) {
  return function ToggleWrapped(props) {
    return props.show ? <WrappedComponent {...props} /> : false;
  }
}
  
export default toggle;
```

7. The provider pattern
   - The provider pattern in React is an advanced pattern used to share global data across multiple components in the React component tree  
   - The provider pattern involves a Provider component that holds global data and shares this data down the component tree in the application using a Consumer component or a custom Hook  
   - The provider pattern is not unique to React; libraries like React-Redux and MobX implement the provider pattern, too

```
// Redux example
import React from 'react'
import ReactDOM from 'react-dom'
 
import { Provider } from 'react-redux'
import store from './store'
 
import App from './App'
 
const rootElement = document.getElementById('root')
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  rootElement
)
 
// material ui ThemeProvider example
import * as React from 'react';
import { createTheme, ThemeProvider } from '@mui/material/styles';
import Checkbox from '@mui/material/Checkbox';
import { green, orange } from '@mui/material/colors';
 
const outerTheme = createTheme({
  palette: {
    secondary: {
      main: orange[500],
    },
  },
});
 
export default function ThemeNestingExtend() {
  return (
    <ThemeProvider theme={outerTheme}>
      <Checkbox defaultChecked color="secondary" />
      <ThemeProvider
        theme={(theme) =>
          createTheme({
            ...theme,
            palette: {
              ...theme.palette,
              primary: {
                main: green[500],
              },
            },
          })
        }
      >
        <Checkbox defaultChecked />
        <Checkbox defaultChecked color="secondary" />
      </ThemeProvider>
    </ThemeProvider>
  );
}
 
import * as React from 'react';
import { useTheme } from '@mui/material/styles';
 
export default function MyComponent() {
  const theme = useTheme();
 
  return <div>{`spacing ${theme.spacing}`}</div>;
}
```

9. The compound components pattern
   - Provides a simple and efficient way for multiple components to share states and handle logic — working together.  
   - 내부 State를 공유하며 서로 상호 작용하는 컴포넌트들을 내부 Static 컴포넌트로 두어 사용자에게 내부 로직은 추상화하고 이외의 로직 작성을 독립적으로 작성할 수 있도록 해 줌  
   - 예를 들면 select 태그는 지 혼자서는 아무 역할도 안하지만 내부 element로 option element들을 작성하여 선택상자라는 역할을 완성할 수 있다  
   - 이 패턴을 사용하면 불필요한 프롭 드릴링(prop drilling) 없이 표현적(expressive)이고 선언적인(declarative) 컴포넌트를 만들 수 있습니다. 커스터마이징이 용이하고 관심사를 분리하여 이해가 쉬운 API를 갖춘 컴포넌트를 원한다면 이 패턴을 고려해봐야 합니다

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/0dbe71c8-a73a-48e9-bd78-b21b3d1de4eb)  

   - 단점
     - 너무 높은 UI의 유연성: 유연성이 높다는 것은 예기치 않은 동작을 유발할 가능성이 크다는 것을 의미합니다. 예를 들어, 필요 없는 자식 컴포넌트가 존재하거나, 자식 컴포넌트의 순서가 잘못되어 있을 수 있고, 꼭 필요한 자식 컴포넌트가 없을 수도 있습니다. 사용자가 컴포넌트를 어떻게 사용하기를 원하는지에 따라, 유연성을 어느 정도 제한하고 싶을 수도 있습니다
     - 너무 무거운 JSX: 이 패턴을 적용하면 JSX 행 수가 증가하며, 특히 EsLint와 같은 린터나 Prettier와 같은 코드 포맷터를 사용하는 경우 더욱 심각해집니다.  단일 컴포넌트 수준에서는 큰 문제가 아니지만, 규모가 커질수록 그 차이가 확연하게 드러납니다  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/75f10540-34e3-4181-acf3-02fe193e5267)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/8766f32d-52ca-49d1-8f95-389b9ba794d9)  

9. The presentational and container component pattern (https://tecoble.techcourse.co.kr/post/2021-04-26-presentational-and-container/)
   - Presentational component: html, css, presentational component만 사용 가능하다.   
     - app에 대해 완전히 몰라야 한다. => 다른 app에서도 이 component를 사용할 수 있을지 스스로에게 물어보자  
     - 작은 레고 블럭처럼 가능한 작게 만들어야 한다  
     - 상태를 가질 수 있지만 UI에 관련된 상태만 가질 수 있다  
   - Container component: 어떠한 동작을 할 것 인가에 대해서 책임진다  
     - 절대로 DOM 마크업 구조나 스타일을 가져서는 안된다
     - 주로 상태를 가지고 있으며, DB CRUD 요청과 같은 side effects를 만들 수 있고 props를 자유롭게 받을 수 있다
     - 대체적으로 presentational 컴포넌트는 stateless한 경향이 있고 container 컴포넌트는 stateful한 경향이 있다

10. Atomic design pattern
    - 디자인 요소들을 나누어 파악하고 이 요소들이 조합되는 과정을 통해서 디자인을 구성하는 방식
    - 5개의 컴포넌트 레이어로 구분함 (Atoms, Molecules, Organisms, Templates, Pages)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/ebb4d746-7ac4-41d4-85d8-2b408990399c)  

    - Atoms  
      하나의 구성 요소. 본인 자체의 스타일만 가지고 있으며 다른 곳에 영향을 미치는 스타일은 포함하지 않아야 함 (form label, input, button과 같은 기본 html element) bootstrap, materials-ui 등과 같은 컴포넌트  
      
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/1e595b3c-df86-4231-9c5c-77bada0411c2)  

    - Molecules (Atoms가 모여서 만들어지는 하나의 구성요소)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/08d547bb-f675-4ae0-94ce-bfd0ce09422e)  

    - Organisms (분자들의 모음)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/80a3d1e3-370b-42e7-a4d7-a2c70d09ca2f)  

    - Template (유기체들을 모아 템플릿으로 생성, 스타일링에 집중한 단위)  
      페이지의 최종 내용 보다는 페이지의 기본 내용 구조에 초점을 맞춤  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/e7042c14-559f-4806-8c55-c7a955838f4e)  

    - Pages (실제 페이지를 구성)  
       - 페이지는 실제 대표적인 콘텐츠가 배치된 UI 모습을 보여주는 템플릿의 특정 인스턴스임. 페이지 단위에서 어플리케이션 상태관리 (리덕스, 모백스 등)가 이루어져야 함  
       - 하지만 분자, 유기체, 템플릿 단위에서 컴포넌트들을 동작시키지 위한 상태를 관리하는 것은 OK ( input과 onChange를 useState로 관라하는 등의 상태관리)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/8998edf3-1811-477a-b95a-1496e42301c0)  

    - Atoms design pattern을 적용한 React directory 구조 예시  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/00da37dd-7b3e-47de-bb2a-ba7b1591ed2a)
    

11. Return component from Hooks
    - "called partial application", Hooks을 활용하는 또 하나의 예시
    - https://blog.bitsrc.io/new-react-design-pattern-return-component-from-hooks-79215c3eac00  ← example과 함께 설명  



  
