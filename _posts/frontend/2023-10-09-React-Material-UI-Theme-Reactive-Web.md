---
title: "Reactive web example with react, material-ui and theme"  
last_modified_at: 2023-10-09T18:40:10-05:00
categories:
  - Blog
tags:
  - Reactive web
  - React.js
  - Material-ui
  - Theme
---

아래의 글에서  Reactive Web에 대한 통찰을 얻을 수 있었다   
https://ko.wix.com/blog/post/responsive-vs-adaptive-design   

또한 아래의 비디오를 참조하였다.   
- [Material-UI 버전을 적용한 Reactive Web-old](https://www.youtube.com/watch?v=lKZiXQWnlUw)
- [Material-UI 버전을 적용한 Reactive Web-newer](https://www.youtube.com/watch?v=fzxEECHnsvU)
- [Material-UI에서 Theme를 Customization 하는 방법](https://www.youtube.com/watch?v=xIIJfmDnvPE)

> 현재 대부분의 사용자들은 데스크톱보다는 모바일 기기를 통해서 웹사이트를 방문할 가능성이 더 높으며,  
> 이러한 이유 때문에 대부분의 웹 어플리케이션들은 반응형으로 개발되도록 요구된다.  
> 반응형 웹이란 디바이스 화면의 크기에 따라 웹 어플리케이션의 컨텐츠가 자동으로 최적화 되게 배치되게 해 주는 방식으로 디자인 된 것을 의미한다.  
>
> 참고로 반응형 웹 디자인 방식과 적응형 웹 디자인을 비교해 보면
> 반응형 웹 디자인은 감지된 화면 크기에 따라 자동으로 페이지가 재배열되는 유동적인 접근 방식이다.
> 반면, 적응형 웹 디자인은 브라우저가 주어진 플랫폼에 맞춰 특별히 생성된 레이아웃을 불러오는 웹 디자인 방식이다
>
> 반응형 웹 디자인 기술은  CSS 미디어 쿼리 (맞춤형 스타일 시트)를 사용하여 사용자가 사용하는 기기의 특성을 검사한 후에
> 검사된 내용을 바탕으로 자체적으로 렌더링 된다  
>
> 반응형 웹 구현을 위해서는 적지 않은 CSS 지식과 구현 기술을 알고 있어야 하며,
> 구현도 구현이지만 메인 화면부터 팝업화면까지 설계시점부터 많은 부분들이 고려되어야 한다. 결코 쉬운 작업은 아니다
>
> 한편 (메트리얼 디자인 가이드를 구현한) 유명한 오픈소스인 Material-ui의 Theme 기능을 활용하면 훨씬 쉽게 반응형 웹을 구현할 수 있다
> (반응형 웹 화면 설계는 여전히 미리아픈 작업)
>
> 다른 Widget용 라이브러리를 사용하지 않고 Material-ui 만을 사용해서도 훌륭한 웹사이트를 구현할 수 있으므로,
> 결론적으로 Material-ui만을 사용하고도, 만족스러운 반응형 웹사이트를 구축할 수 있다.
> 
> 이번 글에서는 React + Material-ui 을 적용해서 (간단하게) 반응형 웹 화면을 구축하는 방법을 (실제 예제와 함께) 알아본다.

#### Material-UI에 대해서
Material-ui는 자주 사용하는 60여 개의 React용 widget을 제공하는 라이브러리이다.   
이 60여 개의 Widget만을 사용해도 웹 화면을 개발하는 데에 부족함이 없으며,   
제공하는 Widget을 활용해서 (자신만의 업그레이드된) 새로운 Widget을 만들 수도 있다.   

특히 2,126개의 자주 사용하는 아이콘들을 SVG와 PNG형태로 제공하고 있으며,   
동일한 아이콘을 속성 값에 의해서 다양한 색상과 크기, 형태로 표현할 수 있도록 해 준다   
(아이콘들의 품질은 상업용 수준에 버금간다)   

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/2ecc223f-a5f0-4882-91c7-f4664a4bf1e8)

Material-ui는 Default Theme(https://mui.com/material-ui/customization/default-theme/ 참조) 라는 것을 제공하고 있으며,   
이 Default theme 정보에는 미리 정의된 breakpoints와 미디어 쿼리 관련 기능들이 포함되어 있다.   
Material-ui에서 제공하는 모든 widget들은 (개발자가 특별하게 지정하지 않는 이상) 이 Default theme에 정의된 정보에 영향을 받는다    
(색상, 글꼴, 마진, 패딩 등)

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/4a0faed9-59c1-4801-905a-7245e4bc0e55)  

#### Material-UI의 Widget들에서 Theme를 적용하는 방법
Material-ui에서는 기존에  Default Theme(https://mui.com/material-ui/customization/default-theme/ 참조) 라는 것을 제공하고 있으며, 이 Default theme 정보에는 미리 정의된 breakpoints와 미디어 쿼리 관련 기능들이 포함되어 있다. Material-ui에서 제공하는 모든 widget들은 (개발자가 특별하게 지정하지 않는 이상) 이 Default theme에 정의된 정보에 영향을 받는다 (색상, 글꼴, 마진, 패딩 등)  

##### ThemeProvider
Custom theme를 적용하고자 할 때에 사용해야 하는 컴포넌트이다.   
App의 최 상단 컴포넌트에 정의하면 그 하부의 모든 컴포넌트에 적용된다   
(Materi8al-ui에서 제공하는 Default theme를 적용할 경우에는 사용할 필요는 없다  

```
// createTheme API를 통해서 Materual-ui에서 제공하는 default theme 대비 overriding 하고자 하는 항목 
// 또는 신규 추가하고자 하는 항목을 정의한다 (default theme: https://mui.com/material-ui/customization/default-theme 참조)
// 신규 추가항목은 makeStyle, styled, useTheme 등의 API를 통해서 적용하지 않으면 반영되지 않는다
const mytheme = createTheme({
    palette: {
        mode: “dark”,    // 여기에서 정의한 overriding 항목 이외의 항목들은 default theme에 설정된 내용들이 적용된다
        primary: {
            main: “…”,
            …
        },
        …
    }
})

export default function App() {
    …
    return (
        <ThemeProvider theme={mytheme}>
            … // Child component
        </ThemeProvider>
    )
}
```  

아래의 코드는 Custom Theme를 정의하는 예시를 보여준다   

```
const GreenTheme = createTheme({
    palette: {
        mode: "light",            // light 모드의 기본 값들을 사용한다 (Material-ui는 light 모드와 dark 모드가 있으며 각각 default 값을 가지고 있음)
        primary: green,           // primary color를 green으로 변경했음
        divider: green[200],      // ...
        background: {
            default: green[900],  // backgroundColor를 green[900]으로 변경했음 ( 각 Color마다 100~900까지 정의되어 있음 )
            paper: green[100],    // Container 들의 기본 바탕색 (Paper component)의 Color를 변경했음
        },
        text: {
            primary: '#fff',      // Primary Text Color 변경
            secondary: green[500],// Secondary Text Color 변경
        },
    },
    typography: {                 // Typography component의 Font와 Font weight 변경 (Quicksand 라는 폰트는 사전에 설치되어 있어야 함)
        fontFamily: 'Quicksand',
        fontWeightLight: 400,
        fontWeightRegular: 500,
        fontWeightMedium: 600,
        fontWeightBold: 700
    }
});
```  

아래 코드는 Custom Font 설치 방법을 보여준다 (index.html)   
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/56a02732-0250-42ad-8b73-5786049d6cd6)   

여러 개의 Theme 중에서 사용자가 (dynamic하게) 자신이 원하는 Theme를 설정하도록 할 수도 있다   

```   
import GreenTheme from './themes/GreenTheme';
import GreyTheme from './themes/GreyTheme';
import StandardDarkTheme from './themes/StandardDarkTheme';
import StandardLightTheme from './themes/StandardLightTheme';

const THEME_NAMES = {
  STANDARD_LIGHT: "light-standard",
  STANDARD_DARK : "dark-standard",
  GREY : "grey",
  GREEN: "green"
}

export default function App () {
    const [theme, setTheme] = React.useState ( StandardLightTheme );
    const handleThemeSelected = (selectedThemeName) => {
        if(selectedThemeName===THEME_NAMES.STANDARD_LIGHT) {
            setTheme(StandardLightTheme);
        } else if(selectedThemeName===THEME_NAMES.STANDARD_DARK) {
            setTheme(StandardDarkTheme);
        } else if(selectedThemeName===THEME_NAMES.GREY) {
            setTheme(GreyTheme);
        } else if(selectedThemeName===THEME_NAMES.GREEN) {
            setTheme(GreenTheme);
        } else {
            setTheme(StandardLightTheme);
        }
     }

    return (
        <ThemeProvider theme={theme}>
            <Box bgcolor={"background.default"} color={"text.primary"}>
                <Navbar />
                <Stack direction=“row” spacing={2} justifyContent=“space-between”>
                    <Sidebar onThemeChanged={handleThemeSelected} />  // 사용자가 UI에서 원하는 Theme를 선택하면 해당 Theme로 변경될 수 있다
                    <Feed />
                    <Rightbar />
                </Stack>
            </Box>
        </ThemeProvider>
    )
}
```

##### Theme 적용을 위한 Hook 목록  
- Material-UI에서는 Theme에서 정의된 스타일을 자신의 Component (Widget)에 적용할 수 있다
- 만약 Material-UI에서 제공하는 widget을 사용할 경우에는 별도로 정의하지 않아도 Theme에서 정의된 스타일이 적용된다.

| Hook | 설명 | 비고 |   
|------|------|------|
| makeStyle | 인자에서 정의한 스타일대로 스타일 객체를 생성해서 반환한다. <br> 인자에는 Function이 들어가며 이 Function의 인자에 ThemeProvider에서 넘겨준 Theme가 전달된다. <br>넘겨 받은 Theme를 적용한 자신 만의 스타일을 생성할 수 있다. | ThemeProvider를 정의하지 않았다면 Material-UI에서 제공하는 default Theme가 전달된다 |
| withStyles | 스타일이 적용된 컴포넌트 (Widget)을 반환한다. <br> 스타일 생성에 필요한 Function과 Function에 의해 생성된 스타일 적용의 컴포넌트를 생성하기 위한 Function을 인자로 넘겨준다 | 상동 |
| styled| 스타일 생성함수에 의해 생성된 스타일이 적용된 (인자로 넘겨진 유형의 컴포넌트와 동일한 타입) Widget을 반환한다 <br> withStyles과 유사한 기능을 제공하지만 withStyle이 리턴할 컴포넌트를 함수 형태의 인자로 받는 대신에 styled는 Component 유형을 인자로 직접 넘겨준다 | 상동 |
| useTheme| ThemeProvider에서 넘겨준 Theme 객체를 반환한다 | 상동 |

##### makeStyle
인자의 함수에서 정의한 스타일 객체를 생성해서 반환하는 Hook 이다.  

```  
import { makeStyle } from “@mui/styles”

const useStyle = makeStyle((theme)=>({
    button: {
        …theme.myButton    // ThemeProvider로 넘겨진 theme 안에 myButton이라는 이름의 스타일이 정의되어 있어야 함
    },
    checkbox: {
        …theme.myCheckbox  // ThemeProvider로 념겨진 theme 안에 myCheckbox라는 이름의 스타일이 정의되어 있어야 함
    }
}));

export default function MyWidget () {
    const classes = useStyle();  // 스타일 그룹 객체를 생성한다
   
    return (
        <>
            <Button style={ classes.button } >버튼</Button>  // 스타일 객체를 사용한다
            <Checkbox style={ classes.checkbox } > 체크박스 </checkbox>  // 스타일 객체를 사용한다
        </>
    )
}
```  

##### withStyles
인자의 함수에서 정의한 스타일이 적용된 컴포넌트 (함수)를 생성해서 반환하는 Hook 이다   

```
import { withStyles } from “@mui/styles”;
import { createTheme, ThemeProvider } from “@mui/material”;
Import clsx from “clsx”;

const defaultTheme = createTheme();  // createTheme에 별도의 인자가 없으므로 Material-ui에서 정의한 default theme가 적용됨

const MyComponent = withStyles((props) => {  // myComponent의 body를 반환함
    const { classes, className, …other } = props;
    return  (
        <div className={ clsx(className, classes.root) } { …other } /> // classes 객체에 root가 있으면 root+className 스타일을 적용하고 없으면 className만 적용함
    )
})(({ theme }) => ({  
    root: {   // root style을 정의함 ( 이 때에 ThemeProvider에서 넘겨진 theme를 사용할 수 있음 )
        background: theme.palette.primary.main
    }
}));


export default function MyWidget () {
   
    return (
        <ThemeProvider theme={ defaultTheme } > // 생성된 theme를 넘겨줌
            <MyComponent />                     // withStyles에 의해서 생성된 객체 (widget)을 사용함
        </ThemeProvider>
    )
}
```   

##### styled
스타일 생성함수에 의해 생성된 스타일이 적용된 (인자로 넘겨진 유형의 컴포넌트와 동일한 타입의) Widget을 반환한다   

```   
import styled from “@emotion/styled”;

const StyledToolbar = styled(Toolbar)(({ theme }) => ({  // 인자의 함수로 정의된 스타일을 가지는 Toolbar 객체를 반환함 (함수 안에서 theme 적용함)
    display: “flex”,
    justifyContent: “space-between”
}));

const StyledSearch = styled(“div”)(({ theme }) => ({     // 인자의 함수로 정의된 스타일을 가지는 div 객체를 반환함 (인자의 함수에서 theme 적용함)
    backgroundColor: “white”,
    padding: “0, 10px”,
    borderRadius: theme.shape.borderRadius,   // 함수 내에서 인자로 넘어온 theme를 적용할 수 있음
    width: “40%”
}));

const Icons = styled(Box)(({ theme }) => ({   // 인자의 함수로 정의된 스타일을 가지는 Box 객체를 반환함 (인자의 함수 내에서 Theme 적용함)
    display: “none”,
    gap: “15px”,
    alignItems: “center”,
    [theme.breakpoints.up(“sm”)]: {
        display: “flex”   // 함수 내에서 인자로 넘어온 theme를 적용할 수 있음 (스크린 크기가 sm보다 클 경우 flex 방식의 display)
    }
}));

export default function MyWidget () {
   
    return (
        <AppBar position: “sticky”>
            <StyledToolbar>  // styled로 정의된 컴포넌트를 사용함
                …
                <StyledSearch><InputBase placeholder=“Search…” sx={{ paddingLeft: 1 }} /></StyledSearch>
                <Icons>…</Icons>
            </StyledToolbar>
        </AppBar>
    )
}
```   

##### useTheme
ThemeProvider에서 넘겨준 theme 객체를 반환함  

```   
import { useTheme } from “@mui/styled”;

export default function MyComponent {
    const theme = useTheme();  // useTheme를 사용하여 theme를 가져옴

    return (
        <div>{`spacing ${themespacing}`}</div>  // 가져온 theme를 사용함
    )
}
```  

#### Material-ui의 Theme를 활용한 반응형 화면 예시  
아래의 그림은 동일한 소스코드로 PC 화면일 경우와 Mobile 화면일 경우 다르게 표현하도록 구성한 예시이다.

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/70d9490d-3fdb-42e1-becc-5945168dc7f8)   

#### Material-ui의 Theme를 활용한 반응형 화면 구성
- Material-UI에서 정의한 기본화면 및 크기
  - 화면을 디바이스 종류에 따라서 총 5가지 유형으로 정의 하였으며, 화면 크게에 따른 조건을 정의하기 위한 5개의 함수를 제공함

| 화면크기 | XS | SM | MD | LG | XL |
|---------|---|---|---|---|---|
| 해상도|0|600|900|1200|1536|   

아래는 함수에 대한 설명이다
|함수명|설명|
|------|----|
|up| 예) [theme.breakpoints.up("sm")]: { display: "none" } <br> -> 디바이스 화면의 가로 해상도가 600 픽셀 이상이면 화면에서 보이지 않게 함 |
|down| 예) [theme.breakpoints.down("sm")]: { display: "flex" } <br> -> 디바이스 화면의 가로 해상도가 600 픽셀 미만이면 flex 방식으로 보여줌 |
|between| 예) [theme.breakpoints.between("sm", "lg")]: { display: "flex" } <br> -> 디바이스 화면 가로 해상도가 600 ~ 1200 이면 flex 방식으로 보여줌 |
|only| 예) [theme.breakpoints.only("sm")]: { display: "flex" } <br> -> 디바이스 화면 가로 해상도가 정확하게 600 픽셀일 경우에만 flex 방식으로 보여줌 |
|not| 예) [theme.breakpoints.not("sm")]: { display: "flex" } <br> -> 디바이스 화면 가로 해상도가 600 픽셀이 아닌 경우에 flex 방식으로 보여줌 |

##### 반응형 화면 구성 예시
- 화면 크기에 따라서 컴포넌트를 다르게 보이도록 구성할 수 있음
- 아래의 코드는 컴포넌트의 스타일 (sx) 내에서 처리할 경우를 보여주고 있음

```   
…
const Sidebar = ({onThemechanged}) => {
    const handleThemeChanged = (e, theme) => {
        onThemeChanged(theme===null ? “light” : theme.name);
    }

    return (
        <Box flex={1} p={2} sx={{ display: { xs: “none”, sm: “block” }}}>  // 모바일 디바이스면 안 보이게 하고 PC면 Block 방식으로 보여줌
            …                                                              // theme를 명시적으로 사용하지 않고서도 현재 디바이스의 화면 사이즈에 따라서 달리 표현할 수 있다
        </Box>
    )
}
```

- 아래의 코드는 styled에 의한 조건문 처리 방식을 보여주고 있음
- 디스플레이 크기가 sm보다 크면 Icons가 보이도록 하고 sm보다 작으면 UserBox가 보이도록 정의한 예제임

```   
…
const Icons = styled(Box)(({ theme }) => ({
    display: "none",
    gap: "15px",
    alignItems: "center",
    [theme.breakpoints.up("sm")]: {  // 디스플레이 크기가 sm보다 크거나 같으면 flex 타입으로 하고 작으면 보이지 않도록 정의함
        display: "flex"
    }
}));
const UserBox = styled(Box)(({ theme }) => ({
    display: "flex",
    gap: "10px",
    alignItems: "center",
    [theme.breakpoints.up("sm")]: {   // 디스플레이 크기가 sm보다 크거나 같으면 안 보이게 하고 작으면 flex 타입으로 보이도록 정의함
        display: "none"
    }
}));

const Navbar = () => {
    return (
        <AppBar position=“sticky”>
            <StyledToolbar>
                <Typography variant=‘h6’ sx={{ display: { xs: “none”, sm: “block” }}}>Reactive Web</Typography>
                <PetsIcon sx={{ display: {{ xs: “block”, sm: “none” }}} />
                …
            </StyledToolbar>
        </AppBar>
    )
}
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/9aa165da-df90-4356-b4c0-bf11b6b075cc)
   
```

#### Outro
- 본 글에서 참조한 소스코드는 [여기](https://www.github.com)를 통해서 git으로 clone 할 수 있다   
- Material-UI만을 사용하고서도, 거의 왠만한 웹 화면을 구성하는 데에 부족함이 없었으며, 특히 Theme와 Reactive web 방식의 UI를 아주 손쉽게 구현할 수 있었다.
- Material-UI에서 제공하지 않는 Widget이 있다면 styled web hook을 통해서 Custom Component를 확보할 수 있고,
- 일관된 theme 적용을 통해서 기존 Material-UI에서 제공하는 Widget과 같은 수준의 손쉬움을 확보할 수 있었다.
- Material-UI에서 제공하는 각종 Widget 사용법과 스타일 적용법에 익숙해 진다면,
- 웹 디자이너의 도움 없이도 Production 수준의 웹 UI를 확보할 수 있다는 점은 아주 매력적인 것 같다.
