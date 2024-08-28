---
title: "Micro Frontend using angularjs 1.x and reactjs with examples"  
last_modified_at: 2024-08-27T14:54:10-05:00
categories:
  - Blog
tags:
  - Microfrontend
  - React.js
  - Angularjs 1.x
  - Microfrontend integration bewteen reactjs and angularjs
---

> 2013년도 ~ 2017년도 사이에 Angularjs 1.x버전으로 개발된 SPA들이 제법 있으며,  
> 불행하게도 시간이 지날 수록 개발자 수급이 어려워지며, 복잡해져서 변경 요구에 대응하기가 점점더 어려워진다.  
> 이런 이유 때문에 유지보수가 용이한 React/Vue/Angular 기술 + Micro frontend 아키텍처로 전환하고자 하는 요구가 많이 있다  
> 하지만 이 작업은 매우 어려운 일이다. 특히 SPA의 규모가 커질 수록 전환작업은 극한으로 어려워질 수 있다.  
> 특히 Angularjs 1.x는 너무 오래된 기술이기 때문에, 몇 년 전부터 더 이상 기술 업데이트가 없으며 최신 기술인 webpack과 관련된 사례나 글을 찾기가 쉽지 않은 관계로 전환작업은 더욱 더 어려워 진다.  
> 기존 Angularjs 1.x 코드를 버리고, 모두 신규 React 컴포넌트로 개발한 후에 Big bang 방식으로 일괄적용 할 수도 있지만  
> 규모가 크고 Lagacy frontend를 "계속 서비스 + 기능 유지보수"하면서 점증적인 전환을 해야 할 경우 (사실상 대부분의 경우가 여기에 해당될 수 있음)  
> 전환 작업은 시간도 오래 걸리고 매우 어려워질 수 있다.
>
> 이 글에서는 전환시에 고려해야 할 사항들을 짚어보고, 점증적 전환을 위한 절차와 예제를 통해서 두 개의 자바 스크립트 프레임워크간 어떻게 통합할지를 고찰한다.  

## 목차
1. 전환시 고려사항  
2. 아키텍처  
3. 전환절차  
4. Angularjs 1.x와 React간 Microfrontend 통합 예제

## 전환시 고려사항  
Legacy Frontend를 유지보수가 용이한 최신 기술의 Frontend로 전환하기 위해서는 다음과 같은 사항들을 고려해야 한다.  
1. 기존 운영중인 Legacy Frontend에 대해서 (사용자들 서비스 향상을 위해) 지속적인 기능 향상을 제공할 필요가 있는가?
2. 동일한 기능을 제공하는 Frontend에 대해서 One Source 정책을 유지할 필요가 있는가?
3. Big bang 방식의 전환 vs 점증적 전환
4. Legacy Frontend에 대한 업데이트 유지보수 인력이 충분한가?

## 아키텍처 

## 전환방법  

## Angularjs 1.x와 React간 Microfrontend 통합   
전체 소스코드는 [여기](https://github.com/micro-frontend-pilot) 를 참조한다.   
모두 4개의 Repository가 있으며 (pokemon-poc는 무시해도 됨), 각 Repository에 대한 설명은 아래의 표와 같다.   
<table>
  <thead>
    <tr>
      <td>이름</td>
      <td>설명</td>
      <td>비고</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>angularjs-app</td>
      <td>
        - Angularjs 1.6.9로 개발된 프로젝트이다.<br/>
        - 기존의 Angularjs router에서 React로 만들어진 컴포넌트를 import 하고, 특정 url로 요청이 오면 React 컴포넌트로 routing 한다<br/>
        - React로 만들어진 Container에서 Angularjs 컴포넌트를 가져다 재사용할 수 있도록 Microfrontend를 expose 한다 
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
    <tr>
      <td>container</td>
      <td>
        - React로 만들어진 프로젝트이다 <br/>
        - React 또는 Nextjs, Angularjs 1.x로 expose된 Micro frontend들을 동적으로 재사용한다.  
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
    <tr>
      <td>common</td>
      <td>
        - Copyright, Header 등의 공통기능 재사용을 위해 Micro frontend로 expose 하기 위한 프로젝트이다 <br/>
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
    <tr>
      <td>biz-next</td>
      <td>
        - 도메인 업무에 대한 Micro Frontend를 제공하기 위한 프로젝트이다. <br/>
        - SSR 및 CSR용 Micro Frontend 적용 예시를 위해서 Nextjs로 만들어져 있다 <br/>
      </td>
      <td>Webpack 5, Module Federation 적용</td>
    </tr>
  </tbody>
</table>

### Webpack 5 적용  
Micro frontend 아키텍처를 적용하는 가장 쉽고, 일반적인 방법은 Webpack 5의 Module Federation을 적용하는 것이다.  
Angularjs 1.x에서 webpack 5 적용을 위한 설정은 아래 코드를 참조한다.  

```
// webpack.config.js
onst path = require("path");
const webpack = require('webpack');
const HtmlWebPackPlugin = require("html-webpack-plugin");
const ModuleFederationPlugin = require("webpack/lib/container/ModuleFederationPlugin");

const deps = require("./package.json").dependencies;

module.exports = {
    // angularjs 1.x 프로젝트에서의 entry 
    entry: './src/app.module.js',
    output: {
        // 이 프로젝트인 Angularjs 1.x로 만들어진 Microfrontend를 다른 프로젝트에서 참조할 때에 사용할 Path를 기입
        // 일반적으로는 CDN 또는 minio와 같은 Bucket에 Upload 하고, 그 주소를 기입한다  
        // publicPath: "http://localhost:3000/",
        path: __dirname + "/dist",
        filename: 'app.bundle.js',
    },
    resolve: {
      extensions: [".tsx", ".ts", ".jsx", ".js", ".json"],
    },  
    devServer: {
      port: 3000,
      // webpack 개발서버에서 라우팅 경로를 처리할 때의 옵션
      // 이 옵션을 true로 하지 않으면, 웹 브라우저에서 직접 URL을 입력하면 404 에러가 난다  
      historyApiFallback: true,
    },
    module: {
      rules: [
        {
          test: /\.m?js/,
          type: "javascript/auto",
          resolve: {
            fullySpecified: false,
          },
        },
        {
          test: /\.(css|s[ac]ss)$/i,
          use: ["style-loader", "css-loader", "postcss-loader"],
        },
        {
          test: /\.(ts|tsx|js|jsx)$/,
          exclude: /node_modules/,
          use: {
            loader: "babel-loader",
          },
        },
        {
          test: /\.(png|jpg|jpeg|gif|svg|woff|woff2|ttf|eot)$/,
          use: {
            loader: "file-loader"
          }
        },
        {
          test: /\.(htm|html)$/,
          exclude: [/node_modules/, require.resolve('./index.html')],
          use: {
            loader: "html-loader",
            options: {
              esModule: false,
            },
          }
        }
      ],
    },
    plugins: [
      new ModuleFederationPlugin({
        // 다른 프로젝트에서 이 마이크로 프론트앤드를 참조할 때의 microfrontend 모듈이름
        name: "ng_mfe_expose",
        filename: "remoteEntry.js",
        // 다른 곳에 배포된 마이크로 프론트앤드를 재사용하기 위해서 정의해 주어야 함
        remotes: {
          // http://127.0.0.1:9000/pilot/common/remoteEntry.js에서 common이라는 이름의 모듈을 common 이라는 이름으로 정의함
          "common": "common@http://127.0.0.1:9000/pilot/common/remoteEntry.js",
        },
        // 외부에 특정 마이크로 프론트앤드를 노출하고 싶을 때 기술함
        // ng_mfe_expose라는 모듈이름으로 "./src/modules/mfe-expose/MfeExposeComponent.js"를 ./MfeAngularComponent이라는 이름으로 노출함 (접속하는 URL은 output.publicPath의 값임)  
        // exposes: {
        //   "./MfeAngularComponent": "./src/modules/mfe-expose/MfeExposeComponent.js",
        // },
        // 모듈을 expose할 때에 포함시킬 라이브러리들 
        // shared: {
        //   "angular" : { singleton: true, requiredVersion: deps.angular },
        //   "angular-route" : { singleton: true, requiredVersion: deps["angular-route"] },
        //   "angular2react" : { singleton: true, requiredVersion: deps["angular2react"] },
        //   "ajv": { singleton: true, requiredVersion: deps.ajv },
        //   "autoprefixer": { singleton: true },
        //   "@uirouter/angularjs": { singleton: true, requiredVersion: deps["@uirouter/angularjs"]}
        // },
      }),
      new HtmlWebPackPlugin({
        template: "./index.html",
        title: "Webpack and Angularjs 1.x application example"
      }),
    ],
}
```
### Reactjs와 Angularjs 1.x 간 통합방법

### Reactjs 에서  Remote Micro Frontend 사용하기

### Angularjs 1.x에서 Remote Micro Frontend 사용하기
