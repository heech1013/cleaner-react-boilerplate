# Minimal React Boilerplate

React 컴포넌트를 렌더링할 수 있는 최소한의 설정으로 구성된 보일러플레이트.

## 튜토리얼

해당 보일러플레이트를 설정하는 과정을 따라가봅니다.

### 의존성 초기화

```bash
$ npm init -y
```

- `-y`: 모든 설정을 default 값으로 설정(means "yes")

### 바벨 설정

> - 바벨은 그 자체만으로는 아무것도 하지 않는다. preset과 plugin을 통해 바벨이 무언가 동작하도록 만든다.
> - 각각의 preset과 plugin들은 npm 라이브러리를 가지고 있다.
> - preset은 plugin들의 집합(콜렉션)이다. preset은 수많은 plugin들을 일일이 추가하고 관리하는 불편함을 해결해준다.

--

```bash
$ npm i -D @babel/core @babel/preset-env @babel/preset-react
```

- 바벨은 개발 단계에서만 사용하기 때문에 devDependencies에 추가한다.
- `@babel/core`: babel compiler core
- `@babel/preset-env`: ES6+의 최신 문법을 별도의 변환 작업 없이도 편하게 사용할 수 있도록 해준다. (ES6+ 문법을 ES5로 컴파일해준다.)
- `@babel/preset-react`: 리액트 어플리케이션을 만들 때 필요한 plugin들의 모음(JSX 코드를 createElement 함수 코드로 변환해주는 plugin 등).

--

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

- 바벨 설정 파일 `.babelrc`에 preset을 추가

### 웹팩 설정

```bash
$ npm i -D webpack webpack-cli webpack-dev-server
```

- `webpack`: 웹팩 라이브러리
- `webpack-cli`: 웹팩의 커맨드라인 인터페이스
- `webpack-dev-server`: 웹팩 빌드 결과물을 확인할 수 있는 개발 서버를 제공한다.

--

```jsx
// webpack.config.js
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "bundle.[fullhash].js",
  },
};
```

- 웹팩 설정 파일: `webpack.config.js`
- `mode`: 모드 설정. 웹팩이 설정한 모드에 적합한 최적화를 진행한다.  
  (`string = 'production: 'none' | 'development' | 'production'`)
- `entry`: 어플리케이션 진입점을 설정.
- `output`: 번들된 파일을 저장할 경로 설정.
  - `filename`: 번들된 파일의 이름 설정. `[fullhash]`는 웹팩에서 생성한 해시를 사용한다.

--

### 웹팩 loader 설정

```jsx
module: {
  rules: [
    {
      test: '빌드할 파일 확장자 정규식'
      exclude: '제외할 파일 정규식'
      use: {
        loader: '사용할 로더 이름'
        option: '로더 옵션'
      }
    }
  ]
}
```

- loader는 `module` - `rules` 키워드로 추가할 수 있다.

--

```bash
npm i -D babel-loader html-loader
```

- `babel-loader`: 바벨을 웹팩에서 사용할 수 있도록 해준다.
- `html-loader`: 웹팩이 HTML을 읽을 수 있도록 해준다.

--

```jsx
// webpack.config.js
module.exports = {
// ...
	module: {
	    rules: [
	      {
	        test: /\.(js|jsx)$/,
	        exclude: /node_modules/,
	        use: {
	          loader: 'babel-loader',
	        },
	      },
	      {
	        test: /\.html$/,
	        use: [
	          {
	            loader: 'html-loader',
	            options: {
	              minimize: true,
	            },
	          },
	        ],
	      },
	    ],
	  },
// ...
```

- 설치한 loader들을 위와 같이 `webpack.config.js` 파일에 추가해준다.

--

### 웹팩 plugin 설정

```bash
$ npm i -D html-webpack-plugin
```

- `html-webpack-plugin`: `script` 태그를 사용하여 `body`에 모든 webpack 번들을 포함하는 HTML5 파일을 생성한다.

--

```jsx
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
// ...
  plugins: [
    new HtmlWebpackPlugin({
      template: 'public/index.html',
    })
  ],
// ...
```

- plugin에 `new HtmlWebpackPlugin()`을 추가하는 것만으로 기본 세팅이 완료된다. (아래와 같이 번들을 포함하는 HTML 파일이 생성된다)
  ```jsx
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="UTF-8" />
      <title>webpack App</title>
    </head>
    <body>
      <script src="index_bundle.js"></script>
    </body>
  </html>
  ```
- 위 HTML 파일은 `HtmlWebpackPlugin`의 default HTML 파일이다. 만일 직접 생성한 HTML 파일을 템플릿으로 사용하고 싶다면, `template` 옵션을 지정한다. (`id`가 `root`인 `div`에 `App` 리액트 컴포넌트를 렌더링해야 하므로, 템플릿 옵션 설정은 필수에 가까울 것이다.)

### 웹팩 개발 서버 설정

> `webpack dev server`의 결과물은 `webpack` 명령어(dist 내부에 번들 파일 생성)와는 다르게 메모리에 저장된다. 개발 서버를 종료하면 결과물도 사라진다.

--

```jsx
// package.json
// ...
"scripts": {
  "start": "webpack-dev-server",
},
// ...
```

- `script` - `start`에 `webpack-dev-server` 명령어 추가

--

```jsx
// webpack.config.js
const port = process.env.PORT || 3000;

module.exports = {
  // ...
  devServer: {
    host: "localhost",
    port: port,
    open: true,
  },
};
```

- `host`: 개발 서버의 url 설정
- `port`: 개발 서버의 port 설정
- `open`: 서버가 실행될 때 자동으로 브라우저를 열어줄지 여부 설정

--

### 리액트 설정

```bash
$ npm i react react-dom
```

--

```jsx
// src/index.js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root"));

// src/App.js
import React from "react";

const App = () => <div>Hello, Webpack!</div>;

export default App;
```

```html
<!-- public/index.html -->
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <title>웹팩 with 리액트 보일러 플레이트</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

- 정상적으로 결과물이 빌드되는지 확인할 수 있도록 파일을 간단히 작성한다.
- script 명령어 `$ npm start`로 테스트할 수 있다.
