---
title: "Frontend conding convention"
last_modified_at: 2023-06-17T18:17:02-05:00
categories:
  - Blog
tags:
  - React
---

> React를 적용해서 프론트엔드를 개발할 때에, 참조할 수 있는 모범사례와 개발 팁을 기술한다.
> 아래의 글을 참조하였다.  
> [참조](https://www.freecodecamp.org/news/best-practices-for-react/)

#### Three Major Challenges React Deveopers Face 
1. Maintainabilty
   - I've often seen a component that has many different cases, each representing a different outcome.
     The JSX is flooded with conditional renderings (tenary operators and simple && operators), classnames are applied conditionally.
     or the component uses a huge switch statement. There are many possible prop and state values, each responsible for a different outcome.
   - But I think everyone should develop a feeling for  
     when a component starts to become less maintainable and
     when these techniques become overused
   - The more complexity and different outcomes a component has (polymorphism),
     the more difficult it becomes to maintain
     -> most root cause: laziness, not enough experience or time pressure to refactor a component properly in odrder to make it more maintainable and cleaner and no or little testing.

2. Solid understanding of react
3. Scalability
   - Crafting excellent software is not only about UX, clean code pattern or clever architecture but also its ability to scale
  
#### React에 대해서 기본적으로 알고 있어야 하는 기술적인 내용들  
1. useState, useEffect, useMemo, useCallback, useRef => 필수, 정확하게 알고 적절하게 사용해야 함
2. [Advanced guide](https://reactjs.org/docs/accessibility.html)
3. [React 세부기술체계적 설명](https://react.vlpt.us/basic/17-useMemo.html)
4. 기타
   - What is "state"?
   - ups and downs of class and functional components
   - how to trigger re-renderings
   - different component lifecycles and how to interact with them
   - Virtual DOM
   - Benefits of CSR (Client Side Rendering) and SSR (Server Side Renendering) in general and in Reat
   - Controlled vs uncontrolled components
   - at least one global state management technology (Context API, Redux/Toolkit, Recoil)
   - Component Patterns (expecially how to choose the right pattern)

#### Learn how to build clean, performant and maintainable React components 
- Good programmer vs Great programmer
- Use theme
- Use functional components, arrow function
- don't use inline-style
- maintain a proper inport structure (third party import first -> internal import below)
- format your code before commiting

1. Create a good folder structure
   - 좋은 구조의 정의는 어플리케이션 크기에 따라서 다를 수 있다 [참조](https://robinwieruch.de/react-folder-structure/) 에서는 잘 되어 있는 파일 구조를 소개하고 있음
2. Maintain a structured import order
   - Built-in (like 'react') -> External (third party node modules) -> internal

```
// Bad
import React, { useState, useEffect, useCallback } from "react";
import Typography from "@material-ui/core/Typography";
import Divider from "@material-ui/core/Divider";
import Title from "../components/Title";
import Navigation from "../components/Navigation";
imrpot Grid from "@material-ui/core/Grid";
import { getSErviceURL } from "../../utils/getServiceURL";
import { Formik } from "formik";

// Good
import React, { useState, useEffect, useCallback } from "react";
import { useSelector, useDispatch } from "react-redux";
import Typography from "@material-ui/core/Typography";
import Divider from "@material-ui/core/Divider";
imrpot Grid from "@material-ui/core/Grid";
import { Formik } from "formik";

import Navigation from "../components/Navigation";
import Title from "../components/Title";
import { getSErviceURL } from "../../utils/getServiceURL";
```
3. Learn different component pattern
   - [https://blog.logrocket.com/react-component-design-pattern-2022/](https://blog.logrocket.com/react-component-design-pattern-2022/)
   - ex: compound component pattern (to avoid uncecessary prop-drilling of many component levels)
   - [https://www.udemy.com/course/the-complete-guide-to-advanced-react-patterns](https://www.udemy.com/course/the-complete-guide-to-advanced-react-patterns)

4. Use a linter and follow its rule
5. Test your code
   - 테스트 코드를 작성하면서 기존 코드를 다시 한번 생각해 볼 수 있ㄱ, 테스트 코드에 의해서 다른 개발자들이 당신의 코드를 좀 더 쉽게 이해할 수 있다.
   - [Testing chapter inside of React docs](https://reactjs.org/docs/testing.html)

6. Integrate Typescript (or at least use default props and prop types)
   - [How to code React apps in type scrpt](https://www.freecodecamp.org/news/how-to-code-your-ract-app-with-typescript/)
   - [5 reasons to use Typescript with React](https://blog.bitsrc.io/5-strong-reasons-to-use-typescript-with-react-bc987da5d907)

7. Use lazy-loading/code spliting (bundling issue)
   - 어플리케이션 규모가 커지면서 번들크기가 커지는 문제발생 (특히 three.js와 같은 Big thrid party 라이브러리가 포함되어 있으면 더 심각함) -> 극히 일부 기능만 사용하더라도 전체 라이브러리를 모두 로딩 해야 함)
   - Code spliting 기법으로 해결할 수 있음 (번들을 필요에 따라서 여러 개로 쪼갤 수 있음 = webpack, Rollup, Borwserify 모두 지원함)
   - Multiple bundle 파일로 컴파일 할 수 있고 필요 시에 동적으로 로딩할 수 있음
   - [React code spliting](https://reactjs.org/docs/code-spliting.html) 

```
// Before
import { add } from './math';

console.log(add(16,26));

// After
import("./math").then(math=>{
    console.log(math.add(16,16));
});
```

```
// Before
import OtherComponent from "./OtherComponent";

// After
const OtherComponent = React.lazy(()=>import("./OtherComponent"));
```

8. Extract reusable logic into custom hooks
   - Hooks allow us to reuse stateful logic without changing our component hierarchy
   - Whenever you find yoursefl in a situation where you have to reuse the same stateful logic that is already used in another functional component, that's a great time to create a custom hook
   - DRY (don't repeat yourself) principle
   - example: PC와 모바일 등의 디바이스가 있을 경우 화면 사이즈와 관련된 로직이 중복되게 들어가게 됨 -> 훅을 사용하라

```
// 훅을 사용하지 않는 경우
const ScreenDimensions = () => {
  const [windowSize, setWindowSize] = useState({
      width: undefined,
      height: undefined
  });

  useEffect(()=> {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: widow.innerHeight
      });
    }
    window.addEventListener('resize', handleResize);
    handleResize();
      return () => window.removeEventListener('resize', handleResize);
  }, []);

  return (
    <>
      <p>Current screen width: { windowSize.width }</p>
      <p>Current screen height: { windowSize.height }</p>
    </>
  )
}

// useWindowSize라는 훅을 만들어서 사용하는 경우
// Hook을 만든다
const useWindowSize = () => {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    }
    window.addEventListener('resize', handleResize);
    handlerResize();
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return windowSize;
}

// 훅을 각기 다른 디바이스에 적용할 수 있다
const ScreenDimensions = () => {
  const windowSize = useWindowSize();

  return (
    <>
      <p>Current screen width: { windowSize.width }</p>
      <p>Current screen height: { windowSize.height }</p>
    </>
  )
}

const ReponsiveView = () => {
  const windowSize = useWindowSize();

  return (
    <>
      { windowSize.width <= 960 ? (
         <SmartphoneView />
       ) : (
         <DesktopView />
       )}
    </>
  )
}
```

9. Handle errors effectively
   - What exactly does error handling mean in the React world? There are some different parts that play a role. One is to cath errors, another one to handle the UI accordingly and the last one to log them properly.
   - React error boundary

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true }
  }

  componentDidCatch(error, errorInfo) {
    //log the error to an error reporting service
    errorService.log({ error, errorInfo});
  }

  render() {
    if(this.state.hasError) {
      return <h1>Oops, something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

   - Use try-catch to handle errors beyond boundaries

```
const UserProfile = ({ userId}) => {
  const [isLoading, setIsLoading] = useState(true);
  const [profileData, setProfileData] = useState({});

  useEffect(() => {
    // Separate function to make of use of async
    const getUserDataAsync = async () => {
      try {
        // Fetch user data from API
        const userData = await axios.get("/users/${userId}");
        // Throw error if user data is falsy (will be caught by catch)
        if(!userData) {
          throw new Error("No user data found");
        }
        // If user data is truthy update state
        setProfileData(userData.profile);
      } catch (error) {
        // Log any caught error in the logging service
        errorService.log({ error });
        // Update state
        setProfileData(null);
      } finally {
        // Reset loading state in any case
        setIsLoading(false);
      }
    }
    getUserDataAsunc();
  }, []);
}
```

   - Use the react-error-boundary library

```
import { ErrorBoundary } from 'react-error-boundary';

const ErrorComponent = ({ error, resetErrorBoundary }) => {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre>{ error.message }</pre>
    </div>
  )
}

const App = () => {
  const logError = (error, errorInfo) => {
    errorService.log({ error, errorInfo });
  }

  return (
    <ErrorBoundary
      FallbackComponent={ ErrorComponent }
      onError={ logError }
    >
      <MyErrorProneComponent />
    </ErrorBoundary>
  );
}
```

   - Logging errors  
     development mode: console.log
     production mode: **you** as the developer want to see the errors in one dedicated place in order to fix them. For that reason we need a logging service create by our own or a third party one. When using third party logging services my personal recommandations is definitely **Sentry**. So I hightly encourage you to check it out.

10. Keep your key prop unique across your whole app

```
// original
const SeasonScores = ({ seasonScoresData }) => {
  return (
    <>
      <h3>Our scores in this season:</h3>
      { seasonScoreData.map((score, index) => (
        <div key={ index }>
          <p>{score.oponent}</p>
          <p>{score.value}</p>
        </div>
      ))}
    </>
  );
}

// refactoring
const SeasonScores = ({ seasonScoresData, currentRoster }) => {
  return (
    <>
      <h3>Our scores in this season:</h3>
      { seasonScoreData.map((score, index) => (
        <div key={ score.id }>
          <p>{score.oponent}</p>
          <p>{score.value}</p>
        </div>
      ))}
      <br/>
      <h3>Our current roster:</h3>
      {currentRoster.map((player, index) => (
        <div key={ player.id }>
          <p>{player.name}</p>
          <p>{palyer.position}</p>
          <p>{player.jerseyNumber}</p>
          <p>{palyer.totalGoals}</p>
        </div>
      ))}
    </>
  );
}
```

11. Tips to help you write better react code - two part
    - first part : Learn the Building Blocks of React, is the solid foundation you build your application on
    - second part: How to Build Clean, Performant and Maintainable React Components is for building the walls
    - Implement the useReducer hook eailer
      **useReducer** is ususally perferable to **useState** when you have complex state logic that involves multiple sub-values or when the next state depends on the previous one. useReducer also let you optimize performance for components that trigger deep updates because you can pass dispatch down instead of callbacks

```
// Initial state
const initialState = {
  isDataLoading: false,
  customerData: [],
  hasError: false,
  isHovered: false,
  mapData: {},
  formData: {},
  isBtnDisabled: false
}

// Reducer
const reducer = (state, action) => {
  switch (action.type) {
    case 'POPULATE_CUSTOMER_DATA':
      return {
        ...state,
        customerData: action.payload
      }
    case 'LOAD_MAP':
      return {
        ...state,
        hasMapLoaded: true
      }
    ...
    ...
    ...
    default: {
      return state
    }
  }
}

// Component
const CustomerMap = () => {
  const [state, dispatch] = useReducer(reducer, initialState);
  ...

  return (...);
}
```

   - Use shorthand for boolean props

```
// Not Good
<RegistrationForm hasPadding={true} withError={true} />

// Good
<RegistrationForm hasPadding withError />
```

   - Avoid curly brace for string props

```
// Bad
<Paragraph variant={"h5"} heading={"A new book"} />

// Good
<Paragraph variant="h5" heading="A new book" />
```

   - Erase non-html attributes when spreading props

```
// Before
// Page component
const IndexPage = () => {
  return (
    <>
      <MainTitle isBold hasPadding>
        Welcome to our new site!

      </MainTitle>
    </>
  )
}

// MainTitle component
const MainTitle = ({ isBold, children, ...restProps }) => {
  return (
    <h1
      style={{
        fontWeight: isBold ? 600 : 400,
        padding: restProps.hadPadding ? 16 : 0
      }}
      {...restProps}
    >
      { children }
    </h1>
  )
}

// After
// Page component
const IndexPage = () => {
  return (
    <>
      <MainTitle isBold hasPadding>
        Welcome to our new site!

      </MainTitle>
    </>
  )
}

// MainTitle component
const MainTitle = ({ isBold, children, hasPadding, ...restProps }) => {
  return (
    <h1
      style={{
        fontWeight: isBold ? 600 : 400,
        padding: hadPadding ? 16 : 0
      }}
      {...restProps}
    >
      { children }
    </h1>
  )
}
```

   - Use snippet extensions for vscode (기본 코드를 자동으로 생성해 줌)

```
import React from 'react';

const GoogleMap = () => {
};

export default GoogleMap;
```

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/9dffd6cb-9cb0-4ee6-aa6d-91dbe8d67371)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/8d863221-77b7-4111-b352-e17ec7fa382d)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/103801fc-a992-4888-9b0a-d17d4b4de9f8)  

   - Write a fragment when a div is not needed (불필요한 div 태그를 사용하지 말아라)

```
// Error
const InfoText = () => {
  // Will throw an error
  return (
    <h1>Welcome!</h1>
    <p>This is our new page, we're glad you're able here~</p>
  )
}

// Fixed with fragment
const InfoText = () => {
  return (
    <>
      <h1>Welcome!</h1>
      <p>This is our new page, we're glad you're able here~</p>
    </>
  )
}
```

   - Integrate self closing tags when no children are needed  

```
<NavigationBar />
```

   - Follow common naming conventions
     - Use PascalCase in components, interfaces, or type alias

```
// React component
const LeftGridPanal = () => {
  ...
}

// Typescript interface
interface AdminUser {
  name: string;
  id: number;
  email: string;
}

// Typescript type alias
type TodoList = {
  todos: string[];
  id: number;
  name: string;
}
```

     - Use camelCase for javascript data type like variables, arrays, objects, functions and so on...  

   - Sanitize your code to prevent **XSS attacks**
     Maybe you've found yourself in a scenario where you have to use the property dangerouslySetInnerHTML on an element in React. Basically it's React's equivalent to innerHTML you might know from javascropt.
     [dompurify tool](https://www.npmjs.com/package/dompurify) that can help 
```
const Markup = () => {
  const htmlString = "<p>This is set via dangerousSetInnerHTML</p>";
  return (
    <div dangerouslySetInnerHTML={{ __html: htmlString }} />
  )
}
```
