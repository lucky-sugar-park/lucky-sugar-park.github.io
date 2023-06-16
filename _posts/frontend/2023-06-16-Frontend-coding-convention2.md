---
title: "Frontend conding convention"
last_modified_at: 2016-03-16T15:28:02-05:00
categories:
  - Blog
tags:
---

#### Code 
21. 리엑트 훅을 적극적으로 활용하라 ( 물론 Usage를 정확하게 알고 사용해야 함 )
22. Use object destructuring
```
// Bad
return (
    <>
        <div> { user.name } </div>
        <div> { user.age } </div>
        <div> { user.profession } </div>
    </>
)
 
// Good
const { name, age, profession } = user;
 
return (
    <>
        <div> { name } </div>
        <div> { age } </div>
        <div> { profession } </div>
    </>
)
```

23. String props don't need curly brace
```
// Bad
return (
    <Navbar title={ "My special app" } />
)
 
// Good
return (
    <Navbar title="My special app" />
)
```

24. Remove JS code from JSX
```
// Bad
return (
    <ul>
        { posts.map((post) => (
            <li onClick={ event => {
                console.log(event.target, 'clicked!'); // <- This is bad
                }} key={post.id}>{post.title}
            </li>
            ))}
    </ul>
);
 
// Good
const onClickHandler = (event) => {
    console.log(event.target, 'clicked!');
}
 
return (
    <ul>
     {posts.map((post) => (
         <li onClick={onClickHandler} key={post.id}> {post.title} </li>
     ))}
    </ul>
);
```

25. Use Template Literals
```
// Bad
const userDetails = user.name + "'s profession is" + user.proffession
 
return (
    <div> {userDetails} </div>
)
 
// Good
const userDetails = `${user.name}'s profession is ${user.proffession}`
 
return (
    <div> {userDetails} </div>
)
```

26. Import in Order
```
// Bad
import React from 'react';
import ErrorImg from '../../assets/images/error.png';
import styled from 'styled-components/native';
import colors from '../../styles/colors'
import { PropTypes } from 'prop-types';
 
// Good ( Built in -> External -> Internal )
import React from 'react';
 
import { PropTypes } from 'prop-types';
import styled from 'styled-components/native';
 
import ErrorImg from '../../assets/images/error.png';
import colors from '../../styles/colors';
```

27. Reserved Prop Naming
```
// Bad
<MyComponent style="dark" />
 
<MyComponent className="dark" />
 
// Good
<MyComponent variant="fancy" />
```

28. Prop naming => camelCase
29. JSX in Parentheses
```
// Bad
return <MyComponent variant="long">
             <MyChild />
       </MyComponent>;
 
// Good
return (
    <MyComponent variant="long">
        <MyChild />
    </MyComponent>
);
```

30. Self-closing Tags
```
// Bad
<SomeComponent variant="stuff"></SomeComponent>
 
// Good
<SomeComponent variant="stuff" />
```

31. Underscore in method name
```
// Bad
const _onClickHandler = () => {
     // do stuff
}
 
// Good
const onClickHandler = () => {
     // do stuff
}
```

32. Alt prop
    - Always include an alt prop in your <img > tags. And don’t use picture or image in your alt property because the screenreaders already announce img elements as images. No need to include that  
```
// Bad
<img src="hello.jpg" />
 
<img src="hello.jpg" alt="Picture of me rowing a boat" />
 
// Good
<img src="hello.jpg" alt="Me waving hello" />
```

33. Avoid using indexes as a key props
34. Use default props and prop types ( or typescript )
35. Isolate stateful aspects from rendering
    - React components can be stateful or stateless. The stateful components keep track of changing data while the stateless components always render the same data
    - To minimize the component complexity it is always a good pratice to isolate your stateful data-loading logic from the rendering stateless logic  
36. The Rule of 3
```
// Fine ( less than 3 )
const Gallery = (props) => {
    let { image, title } = props;
    ...
}
 
// Hadr to read
const Gallery = (props) => {
    let { image, title, artist, class, thumbnail, breakpoint } = props;
}
 
// Good
const Gallery = (props) => {
    let {
        image,
        title,
        artist,
        class,
        thumbnail,
        breakpoint
    } = props;
}
```

#### Good commenting pratices 
1. Use comments to explain why you did something, not how you did it.
   - If you find yourself expaining how you did something, then it's time to make your code self-explanatory
2. Another point is **not to write comments that are obvious and reduncant**
```
// Prints out the sum of two numbers
console.log(sum);
```

#### ES6 
1. Always destructure you props. Destructuring you props helps make your code cleaner and more maintainable. It also makes assigning object properties to variables feels like much less of a chore
2. Know where to use spread/rest operators. You shall read about it before actually using it.
   -  REST: 프로젝트 내에서 별도의 Rule이 필요함 (정의해야 함) → REST API 호출하는 자바 스크립트 모듈 구조의 표준, naming 표준 등
   -  Spread 연산자 잘 사용하기
```
// Array
var arr1 = [1, 2, 3, 4, 5];
var arr2 = [...arr1, 6, 7, 8, 9];
 
console.log(arr2); // [ 1, 2, 3, 4, 5, 6, 7, 8, 9 ]
 
// String
var str1 = 'paper block';
var str2 = [...str1];
console.log(str2); // [ "p", "a", "p", "e", "r", " ", "b", "l", "o", "c", "k" ]
```
3. Try using optional chaining if possible ( 컴포넌트의 재사용을 향상시킬 수 있음)
4. Use const instead of var or let. const lets you check that a variable should always stary constant. let indicates that the vlaues of the variables can be changed
   - **const, let**만 사용함 
5. Start **preferring arrow function** (=>) for more cleaner code. Arrow functions allow simplifying your code to a single line

#### TESTING/CSS 
1. Write tests for the entire code  
2. Define a quality gate using coveralls  
3. Don't test more than on thing in a test  
4. No log should exist within your test code  
5. test classes only test on class  
6. code that needs to talk to a network, or database is mocked.  
7. Avoid inline CSS  
8. A naming convention is defined and followed  
9. Choose styled-component over other CSS-in-JS libraries  
   - Style-Component is one of the most preferred libraries that provides an easy way yo use CSS in modern JavaScript. It enables developers to write CSS which is nicely isolated per component whild preserving the full power of cascading.  
   - Gives freedom to build a custom component with CSS  
   - Enforce the use of props in the place of classes  
   - Supports server-side rendering with stylesheet rehydration  
   - Can perform unit testing with Jest Styled Component -  A set of utilities to test style components with Jest  

#### Basics 
1. Always ensure that your app is responsive ( 반응형 프로그래밍 )
2. Your app should contain a proper README.md file that clearly details or explains your app. Clearly mention the features, functionalities, contributions guideness, installation process, guide, etc
3. Do check that your app runs without any errors
4. Use EsLint throughout your codebase and other automation tools so that most of the best practices are maintained automatically for you.
5. Consolidate duplicate code throughout the codebase
6. You can use extensions like prettier, EsLint, EditorConfig, PLOP etc to maintain your codebase efficiently.


