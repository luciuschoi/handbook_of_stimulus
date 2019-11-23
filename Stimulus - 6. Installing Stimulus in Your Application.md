# 애플리케이션에 Stimulus 설치하기

애플리케이션에 **Stimulus**를 설치하려면 [stimulus npm package](https://www.npmjs.com/package/stimulus)를 자바스크립트 번들에 추가하십시오. 또는 `<script>` 태그에 **stimulus.umd.js**를 로드하십시오.



## webpack 이용하기

**Stimulus**는 [webpack](https://webpack.js.org/) 애셋 패키지와 통합되어 앱의 폴더에서 컨트롤러 파일을 자동으로 로드합니다.



**Stimulus** 컨트롤러가 포함된 폴더 경로로 webpack의 `require.context` 헬퍼를 호출하십시오. 그런 다음 `definitionsFromContext` 헬퍼를 사용하여 결과 컨텍스트를 `Application#load` 메소드에 전달하십시오.



```javascript
// src/application.js
import { Application } from "stimulus"
import { definitionsFromContext } from "stimulus/webpack-helpers"

const application = Application.start()
const context = require.context("./controllers", true, /\.js$/)
application.load(definitionsFromContext(context))
```



## 컨트롤러 파일 이름을 식별자로 매핑하기

컨트롤러 파일 이름을 **[식별자]_controller.js** 로 지정합니다. 여기서 식별자는 HTML에서 각 컨트롤러의 `data-controller` 식별자에 해당합니다. 



**Stimulus**은 일반적으로 밑줄을 사용하여 파일 이름에서 여러 단어를 분리합니다. 컨트롤러 파일 이름의 각 밑줄은 식별자에서 대시로 변환됩니다.



하위 폴더를 사용하여 컨트롤러의 네임 스페이스를 지정할 수도 있습니다. 네임 스페이스가 있는 컨트롤러 파일 경로의 각 슬래시는 식별자에서 두 개의 대시가 됩니다.



원하는 경우 컨트롤러의 파일 이름에서 밑줄 대신 대시를 사용할 수 있습니다. **Stimulus**는 그것들을 동일하게 취급합니다.



| 컨트롤러 파일 이름            | 식별자           |
| ----------------------------- | ---------------- |
| clipboard_controller.js       | clipboard        |
| date_picker_controller.js     | date-picker      |
| users/list_item_controller.js | users--list-item |
| local-time-controller.js      | local-time       |



## 다른 빌드 시스템 사용

**Stimulus**은 다른 빌드 시스템과도 작동하지만 컨트롤러 자동 로딩을 지원하지 않습니다. 대신 애플리케이션 인스턴스에 컨트롤러 파일을 명시적으로 로드하고 등록해야 합니다.



```javascript
// src/application.js
import { Application } from "stimulus"

import HelloController from "./controllers/hello_controller"
import ClipboardController from "./controllers/clipboard_controller"

const application = Application.start()
application.register("hello", HelloController)
application.register("clipboard", ClipboardController)
```



## Babel 사용하기

빌드 시스템과 함께 **Babel**을 사용하는 경우 [@babel/plugin-proposal-class-properties](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties)를 설치하고 구성 파일에 추가해야 합니다.



```json
// .babelrc
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```



## 빌드 시스템없이 사용하기

빌드 시스템을 사용하지 않으려면 `<script>` 태그에 **Stimulus**를 로드하면 **window.Stimulus** 객체를 통해 전역적으로 사용할 수 있습니다.



아직 기본적으로 지원되지 않는 `static targets = […]` 클래스 속성 대신 `static get targets()` 메소드를 사용하여 대상을 정의하십시오.



```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <script src="https://unpkg.com/stimulus/dist/stimulus.umd.js"></script>
  <script>
    (() => {
      const application = Stimulus.Application.start()

      application.register("hello", class extends Stimulus.Controller {
        static get targets() {
          return [ "name" ]
        }

        // …
      })
    })()
  </script>
</head>
<body>
  <div data-controller="hello">
    <input data-target="hello.name" type="text">
    …
  </div>
</body>
</html>
```



## 브라우저 지원

**Stimulus**는 기본적으로 항상 업데이트되는 자체 업데이트 데스크탑 및 모바일 브라우저를 모두 지원합니다.



애플리케이션이 Internet Explorer 11과 같은 이전 브라우저를 지원해야하는 경우 **Stimulus**를 로드하기 전에 [`@stimulus/polyfills`](https://www.npmjs.com/package/@stimulus/polyfills) 패키지를 포함하십시오.



```javascript
import "@stimulus/polyfills"
import { Application } from "stimulus"

const application = Application.start()
// …
```

