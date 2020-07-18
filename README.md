# React에 Scss적용하는 방법

참고: <a href="https://velog.io/@velopert/react-component-styling">@velopert</a>

<br/>

### ✔ Sass 

Sass (Syntactically Awesome Style Sheets: 문법적으로 멋진 스타일 시트)는 CSS pre-processor로서,
복잡한 작업을 쉽게 할 수 있게 해주고, 코드의 재활용성을 높여줄 뿐만 아니라, 코드의 가독성을 높여주어
유지보수를 쉽게 해줍니다.


### ✔ Sass VS Scss

- .sass

    ```sass
    $font-stack: Helvetica, sans-serif
    $primary-color: #333

    body
        font: 100% $font-stack
        color: $primary-color
    ```

- .scss

    ```scss
    $font-stack:    Helvetica, sans-serif;
    $primary-color: #333;

    body {
        font: 100% $font-stack;
        color: $primary-color;
    }
    ```

- #### 🔎 <a href="https://sass-lang.com/guide">더 자세히 알아보기</a>

<br/>
<br/>


## 방법


### ✔ 1. node-sass 라이브러리 설치

node-sass 라이브러리는 Sass를 CSS로 변환해준다

```bash
$ yarn add node-sass
```
<br/>

### ✔ 2. src 디렉토리에 SassComponent.scss파일 생성

- SassComponent.scss

    ```scss
    // 변수 사용하기
    $red: #fa5252;
    $orange: #fd7e14;
    $yellow: #fcc419;
    $green: #40c057;
    $blue: #339af0;
    $indigo: #5c7cfa;
    $violet: #7950f2;

    // mixin 만들기 (재사용되는 스타일 블록을 함수처럼 사용 할 수 있음)
    @mixin square($size) {
    $calculated: 32px * $size;
    width: $calculated;
    height: $calculated;
    }

    .SassComponent {
    display: flex;
    .box {
        background: red; // 일반 CSS 에선 .SassComponent .box 와 마찬가지
        cursor: pointer;
        transition: all 0.3s ease-in;
        &.red {
        // .red 클래스가 .box 와 함께 사용 됐을 때
        background: $red;
        @include square(1);
        }
        &.orange {
        background: $orange;
        @include square(2);
        }
        &.yellow {
        background: $yellow;
        @include square(3);
        }
        &.green {
        background: $green;
        @include square(4);
        }
        &.blue {
        background: $blue;
        @include square(5);
        }
        &.indigo {
        background: $indigo;
        @include square(6);
        }
        &.violet {
        background: $violet;
        @include square(7);
        }
        &:hover {
        // .box 에 마우스 올렸을 때
        background: black;
        }
    }
    }
    ```
- SassComponent.js

    ```js
    import React from 'react';
    import './SassComponent.scss';

    const SassComponent = () => {
        return (
            <div className="SassComponent">
            <div className="box red" />
            <div className="box orange" />
            <div className="box yellow" />
            <div className="box green" />
            <div className="box blue" />
            <div className="box indigo" />
            <div className="box violet" />
            </div>
        );
    };

    export default SassComponent;
    ```
- 컴포넌트 App.js에 렌더링

<br/>


### ✔ 3. Sass변수 및 믹스인 분리

자주 사용 되는 Sass변수 및 믹스인을 따로 파일로 분리해준다.

- src > styles > utils.scss

    ```scss
    // 변수 사용하기
    $red: #fa5252;
    $orange: #fd7e14;
    $yellow: #fcc419;
    $green: #40c057;
    $blue: #339af0;
    $indigo: #5c7cfa;
    $violet: #7950f2;
    // mixin 만들기 (재사용되는 스타일 블록을 함수처럼 사용 할 수 있음)
    @mixin square($size) {
    $calculated: 32px * $size;
    width: $calculated;
    height: $calculated;
    }
    ```
    다른 scss파일에 위의 파일을 **@import** 해서 사용!

- src/SassComponrt.scss

    ```scss
    @import './styles/utils.scss';
    ```

<br/>


### ✔ 4. sass-loader 설정 커스터마이징 (import 경로 설정 간단하게)

import 해줄때 경로설정을 간단하게 하는 방법

깊숙한 구조로 디렉토리를 구성하게 되면 '../../../styles/utils.scss' 이런식으로 한참 거슬러올라가야 
한다는 단점이 있다

=> Webpack에서 Sass를 처리하는 sass-loader의 설정을 커스터마이징

#### 1) yarn eject

create-react-app 으로 만든 프로젝트는 프로젝트 구조의 복잡도를 낮추기 위해 세부 설정들이 모두 숨어있다. 
이를 커스터마이징 하기 위해서는 **yarn eject** 명령어를 통해 세부설정을 다시 밖으로 꺼내줘야한다

create-react-app 으로 만든 프로젝트는 기본적으로 .git 설정도 되어있는데, yarn eject는 아직
git에 커밋되지 않은 변화가 있다면 진행 되지 않는다! 먼저 커밋해줘야 함!

```bash
$ git add .
$ git commit -m 'Commit before yarn eject'
```
```bash
$ yarn eject
yarn run v1.12.0
warning ../package.json: No license field
$ react-scripts eject
? Are you sure you want to eject? This action is permanent. (y/N) y
```

#### 2) webpack.config.js 수정

webpack.config.js 코드안에 sassRegex 를 찾아보면,

```js
// config > webpack.config.js

{
    test: sassRegex,
    exclude: sassModuleRegex,
    use: getStyleLoaders(
    {
        importLoaders: 2,
        sourceMap: isEnvProduction && shouldUseSourceMap,
    },
    'sass-loader'
    ),
    sideEffects: true,
},
```
위의 블록에서 **use:** 부분을 다음과 같이 교체!

```js
{
    test: sassRegex,
    exclude: sassModuleRegex,
    use: getStyleLoaders(
    {
        importLoaders: 3,
        sourceMap: isEnvProduction && shouldUseSourceMap,
    }).concat({
        loader: require.resolve('sass-loader'),
        options: {
        sassOptions: {
            includePaths: [paths.appSrc + '/styles'],
            sourceMap: isEnvProduction && shouldUseSourceMap
        }
        }
    }),
    sideEffects: true,
},

```

#### 3) 서버 껐다가 다시 시작

이제, utils.scss 파일을 불러올 때, 앞부분에 상대경로를 입력 할 필요 없이 style디렉토리 기준 절대경로로
불러 올 수 있다.

```scss
@import 'utils.scss';
```

<br/>

### ✔ 5. sass-loader 설정 커스터마이징 (더 간단하게)

모든 scss파일에 동일한 코드 넣어주기 => prependData 사용 (data는 이제 사용X)

```js
{
    test: sassRegex,
    exclude: sassModuleRegex,
    use: getStyleLoaders(
    {
        importLoaders: 3,
        sourceMap: isEnvProduction && shouldUseSourceMap,
    }).concat({
        loader: require.resolve('sass-loader'),
        options: {
        sassOptions: {
            includePaths: [paths.appSrc + '/styles'],
            sourceMap: isEnvProduction && shouldUseSourceMap,
        },
        prependData: `@import '_variables';`,  // prependData 사용!!

        }
    }),
    sideEffects: true,
},
```
