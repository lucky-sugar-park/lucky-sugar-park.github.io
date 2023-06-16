---
title: "Frontend conding convention"
last_modified_at: 2016-03-16T15:28:02-05:00
categories:
  - Blog
tags:
---

> 코드의 품질은 결국 Business Agility에 많은 영향을 미치며, 좋은 코드는 변경 요구사항에 유연하면서도 기민하게 대응할 수 있도록 함으로써 조직의 Business Agility를 향상시킨다.   
> 이렇게 하기 위해서는 신규 개발자라고 하더라도 코드의 디렉토리 구조와 이름, 파일이름 만을 보고도 개략적인 시스템의 구조와 기능을 짐작할 수 있어야 한다.  
> 또한 별도의 주석이 없더라도 코드 자체가 Self description 되어서 이해하기 쉬워야 한다 (클래스 이름, 함수이름, 변수이름, 파일이름, 디렉토리 이름이 중요한 이유임)  
> 이번 글에서는 Frontend 중에서도 React 기술을 적용할 경우, 유용한 Convention을 기술한다.

## Principles
1. Keep components small and function-specific
   - Function-specific components can be standalone, which makes testing and maintenance easier
   - Each small component can be reused across multiple projects
   - Components executing general functions can be made available to the community
   - With smaller components, it's easier to implement performance optimizations
   - It's easier to update smaller components
   - Bigger components have to perform harder and may be difficult to maintain
2. Reusability is important, so keep creation of new components to the mininum required (small, general)
   - By sticking to the rule of on function = one component, you can improve the reusability of components 
3. Consolidate duplicate code -DRY (Don't replicate yourself)
4. Airbnb javascript style guide
   - [Airbnb style guide](https://airbnb.io/javascript/react/)
5. Google typescript style guide
   - [Google style guide](https://google.github.io/styleguide/tsguide.html)
  
## React coding conventions 
#### 디렉토리 구조  
1. The react documentation mentions that there are in general two main ways to organize your React application.  
- Grouping by features or routes
     
```
src/
App.js
App.css
App.test.js
index.js
global/ ⇐ items that are in common with entire application
  AppContext.js
  ThemeContext.js
  UserContext.js
  Button.js
cards/
  index.js
  Cards.css
  Cards.js
  Card.css
  Card.js
  Card.test.js
detailed-product/
    DetailedProduct.css
  DetailedProduct.js
  DetailedProduct.test.js
checkout/
  ReviewOrder.css
  ReviewOrder.js
  ReviewOrder.test.js
  ShoppingCart.css
  ShoppingCart.js
  ShoppingCart.test.js 
user/
  index.js
  User.css
  User.js
  User.test.js
```  

- Grouping by file type
     
```
src/
App.js
index.js
components/
  App.css
  Card.js
  Cards.js
  ConfirmationPage.js
  DetailedProduct.js
  Footer.js
  Navbar.js
  ReviewOrder.js
  Settings.js
  ShoppingCart.js
  User.js
context/
  AppContext.js
  ThemeContext.js
  UserContext.js
css/
  Card.css
  Cards.css
  ConfirmationPage.css
  DetailedProduct.css
  Footer.css
  Navbar.css
  ReviewOrder.css
  Settings.css
  ShoppingCart.css
  User.css
hooks/
  useAuth.js
  useAxios.js
  ...other custom hooks
tests/
  App.test.js
  Card.test.js
  Cards.test.js
  ConfirmationPage.test.js
  DetailedProduct.test.js
  Footer.test.js
  Navbar.test.js
  ReviewOrder.test.js
  Settings.test.js
  ShoppingCart.test.js
  User.test.js
```  

2. Grouping by Level of componnet

   ![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/9dd00cc1-7195-4505-9979-ebd2d1d5fb4f)

3. Other examples
   ![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/b408c508-6680-4a29-8c40-056456c0cfc7)
   ![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/552d3dde-8dbc-4754-b004-d149f925fa37)


#### 코드구조 
1. **import statement order**: React import <> 3 party library import <> import * as <> import ./<some file>
   - 각각의 Priority 내에서 알파벳 순으로 오름차순으로 정렬
   - Make sure all your imports statements are on new lines, as this will make your imports clean and easy to understand for all the components, third-party libraries, etc
2. **Use index.js** for each folder to export so that all the import are referenced on the index.js file, and you don't have to write  import statements again for every file

   ![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/b4169ddb-b95d-4a87-bd21-b9529cb06384)

3. It's easy to get confused about whether to use double quotes(" ") or single quotes(' ') while working with React. The simple answre is : **maintain consistency** whatever you're using. Also, consider that you use double quotes(" ") for JSX attributes and single quotes(' ') for the JS code (Follow the standard way or maintain consistency)  
4. Most of the time, beginners merge two-three components inside a single file and play around with that. However, that's not a good practice to maintain clean code
   - Single component in a file
   - Dividing the whole app into components an then working on it on a separate file is a good practice to maintain clean code
5. Do you sometimes use CSS styles with JSX? But it's a bad practice. Always make className for each element and put all the styling under respective CSS file only.

#### Naming conventions 
1. Your filenames should always have consistent casing and extension. Either use .js or .jsx as explained in code structure.
   - **React Component** 파일: **PascalCase** 적용하며 **.jsx or .tsx** 확장자 사용 ( 예, LoginPresenter.jsx )
   - 일반 java script Class 파일 (No react component): **PascalCase** 적용하며 .js 확장자 사용 ( 예, MessageHandler.js )
   - 함수 제공 파일: **camelCase** 적용 (예, useReducer..js )

2. In React, name your file the same as the React component inside that file without a hyphen in the filename (ex, RegistrationForm.jsx )
3. variables/function/modules → **camelCase** 사용
4. Use well-descriptive names for variable/functions/modules/. Keep in mind that it is application-specific so that even a third party or new developer can easily understand your code
5. It's bad to use the underscore prefix ( _ ) for a React component's internal methods because underscore prefixes seem to used as a convention in other languages to denote private objects or variables. But everything in JavaScript is public. And there is no native support for privacy. So even if you add underscore prefixes to your properties, It'll not make them private.  
6. When making reducer functions, write **Action** types as **domain/eventName**. For example: **ADD_TODO** and **INCREMENT** (in CAPITALS) as this matches the typical conventions in most programming languages for declaring the constant values.
7. Talking about cases in a React component, use **PascalCase** for the same - and for their instances use camelCase
   - const loginForm = <LoginForm />
   -  import LoginForm from './loginForm';
8. For the folder name,. use **snake-case**
9. CSS files should be named the same as the component (CookieBanner.jsx, CookieBanner.css, Header.jsx, Header.css)  

#### Files and Folders 
1. Your app's directory structure shall be as follows

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/7ec10506-7f40-4147-8dca-9bfacd35d494)  

2. Each component folder should have its respective files grouped under the same. It maintains the hierarchy throughout the codebase. The necessary files include: Comonent.jsx, Component.css, Component.test.js  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/0c375cde-db4a-4e7e-92f7-48297b954464)  

3. If a component requires multiple file (css, test) **locate all files within component a folder**

#### Code 
1. Play the game of HOOKS by following all its rules. You can read more about the rules [here](https://www.smashingmagazine.com/2020/04/react-hooks-best-practices/)  
   - Either manually follow these rules or use an ESLint plugin called **eslint-plugin-react-hooks** which enforces these rules. So, add these rules while working on any project
   - ```npm install --savedev eslint-plugin-react-hooks```
  
2. Remove **console.logs** - unless you have strong motivation why you would like it iin case of **production mode**
3. **Avoid multiple if-else blocks**. Instead, **use ternary** - best for clean code practice
4. **Remove all commented-out codes**. The biggest motivation for writig comments is the bad code that you write. It would be good to spend more time writing descriptive functions, methods, and filenbames that are self-explanatory.
5. Write Tests for each components. It's a good practice to write test cases for each component developed as it reduces the chances of getting errors when deployed. You can check all the possible scenarios through until testing - and for that, some of the most commonly used React test frameworks you can use are JEST and ENZYME ([JEXT vs Mocha](https://www.ponicode.com/blog/jest-versus-mocha-which-testing-framework-for-you))
6. Use Optional chaining if things can be null
```
const adventurer = {
  name: 'Alice',
  cat: {
    name: 'Dinah'
  }
};
 
const dogName = adventurer.dog?.name;
console.log(dogName);
// expected output: undefined
 
console.log(adventurer.someNonExistentMethod?.());
// expected output: undefined
```

7. Use the guard pattern/prop types/typescript to ensure your passed in parameters re valid
8. Create PURE functions and avoid side-effects
   - PURE function: Sampe input, Same output
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/a718b424-98fd-4223-ac7f-6d1fc43c87c9)

```
// No so bad
 
const impureDouble = (x) => {
  console.log('doubling', x);
 
  return x * 2;
};
 
const result = impureDouble(4);
console.log({ result }); // side effects here
 
...
// “Impurely” Changing an Object
const impureAssoc = (key, value, object) => {
  object[key] = value;
};
 
const person = {
  name: 'Bobo'
};
 
const result = impureAssoc('shoeSize', 400, person);
 
console.log({
  person,
  result
});
 
// Purifying it
const pureAssoc = (key, value, object) => ({
  ...object,
  [key]: value
});
 
const person = {
  name: 'Bobo'
};
 
const result = pureAssoc('shoeSize', 400, person);
 
console.log({
  person,
  result
});
 
 
// another way
onst pureAssoc = (key, value, object) => {
  const newObject = { ...object };
 
  newObject[key] = value;
 
  return newObject;
};
 
const person = {
  name: 'Bobo'
};
 
const result = pureAssoc('shoeSize', 400, person);
 
console.log({
  person,
  result
});
```  

9. **Avoid mutating** state when working with arrays
10. Treat **props as read-only**. Do not try modify them
11. **No DRY** (Don't repeat yourself) violations. Create utility files to avoid duplicate code
12. Use **Higher Order Components** where appropriate
13. Favour functionless components
14. **Do not use mixins** (다중 상속이 가능하도록 하는 라이브러리 - 종속 충돌 문제도 있지만 다중상속으로 인한 부작용이 더 큰 문제임)
    -  Mixins introduce implicit dependencies, cause name clashes, and cause snowballing complexity. Most use cases for mixins can be accomplished in better ways via components, higher-order components, or utility modules
15. Methods that are longer that the screen should be refactored into smaller units
16. Use JSX ShortHand
```
// Bad
return (
    <navbar showTitle={ true } />
)
 
// Good
return (
    <navbar showTitle />
)
```
17. Use Ternary operators

```
// Bad
const { role } = user;
 
if(role === ADMIN) {
    return <AdminUser />
} else {
    return <NormalUser />
}
 
// Good
const { role } = user;
 
return role === ADMIN ? <AdminUser /> : <NormalUser />
```

18. Take Advantage of Object Literals
```
// Bad
cost { role } = user;
 
switch(role) {
    case ADMIN:
        return <AdminUser />
    case EMPLOYEE:
        return <EmployeeUser />
    case USER:
        return <NormalUser />
}
 
// Good
cost { role } = user;
 
const components = {
    ADMIN: AdminUser,
    EMPLOYEE: EmployeeUser,
    USER: NormalUser
};
 
const Component = components[role];
 
return <Component />;
```

19. Use Fragments
```
// Bad
return (
    <div>
        <Component1 />
        <Component2 />
        <Component2 />
    </div>
)
 
// Good
return (
    <>
        <Component1 />
        <Component2 />
        <Component2 />
    </>
)
```

20. Don't define a function inside rende
```
// Bad
return (
    <button onClick={ () => dispaatch(ACTION_TO_SEND_DATA) }
        This is a bad example
    </button>
)
 
// Good
const submitData = () => dispatch(ACTION_TO_SEND_DATA);
 
return (
    <button onClick={ submitData }>
        This is
    </button>
)
```  
