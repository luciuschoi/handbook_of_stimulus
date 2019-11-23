# Hello, Stimulus



**Stimulus**의 작동 방식을 배우는 가장 좋은 방법은 간단한 컨트롤러를 만드는 것입니다. 이 장에서는 그 방법을 소개할 것입니다.



## 전제조건

여기서 소개하는 내용을 따라 가려면 **Stimulus**를 살펴 보기 위해 사전 구성된 빈 슬레이트인 [stimulus-starter](https://github.com/stimulusjs/stimulus-starter) 프로젝트의 실행 사본이 필요합니다.



[Glitch에서 stimulus-starter를 리믹스하는 것](https://glitch.com/edit/#!/import/git?url=https://github.com/stimulusjs/stimulus-starter.git)을 추천하는데 아무것도 설치하지 않아도 브라우저에서 전적으로 작업 할 수 있게 됩니다.



![](/Users/lucius/Documents/assets/remix_on_glitch.png)



또는 자신 만의 텍스트 편집기로 편하게 작업하려면 **stimulus-starter**를 복제하고 설정해야합니다.



```shell
$ git clone https://github.com/stimulusjs/stimulus-starter.git
$ cd stimulus-starter
$ yarn install
$ yarn start
```



그런 다음 브라우저에서 http://localhost:9000/ 을 방문하십시오.



(**stimulus-starter** 프로젝트는 의존성 관리를 위해 [Yarn 패키지 관리자](https://yarnpkg.com/)를 사용하므로 먼저 설치해야합니다.)



## 이 모든 것은 HTML로부터 시작한다

간단한 연습(버튼이 있는 텍스트 필드)으로 시작하겠습니다. 버튼을 클릭하면 콘솔에 텍스트 필드의 값이 표시됩니다.



모든 **Stimulus** 프로젝트는 HTML로 시작하며 이 프로젝트도 예외는 아닙니다. **public/index.html**을 열고 `<body>` 태그 직후에 다음의 마크업을 추가하십시오.



```html
<div>
  <input type="text">
  <button>Greet</button>
</div>
```



브라우저에서 페이지를 다시로드하면 텍스트 필드와 버튼이 나타납니다.



## 컨트롤러가 HTML에 생명을 불어 넣는다

기본적으로 **Stimulus**의 목적은 DOM 요소를 자바스크립트 객체에 자동으로 연결하는 것입니다. 이러한 객체를 *컨트롤러*라고합니다.



프레임워크에 내장된 `Controller` 클래스를 확장하여 첫 번째 컨트롤러를 만들어 봅시다. **src/controllers/** 폴더에 **hello_controller.js**라는 새 파일을 만듭니다. 그런 다음 아래의 코드를 안에 추가합니다.



```javascript
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
}
```



## 식별자가 컨트롤러를 DOM과 연결시킨다

다음으로, **Stimulus**에게 이 컨트롤러를 HTML에 연결하는 방법을 알려줘야 합니다. `<div>`의 **data-controller** 속성에 식별자를 지정하여 수행하게 됩니다.



```html
<div data-controller="hello">
  <input type="text">
  <button>Greet</button>
</div>
```



식별자는 HTML 요소와 컨트롤러 사이를 연결하는 역할을 합니다. 이 경우 식별자 **hello**는 **Stimulus**에게 **hello_controller.js** 내에 있는 컨트롤러 클래스에게 인스턴스 하나를 작성하도록 지시합니다. [설치 안내서](https://stimulusjs.org/handbook/installing)에서 자동 컨트롤러 로딩 방식에 대해 자세히 알아볼 수 있습니다.



## Is This Thing On?

브라우저에서 페이지를 새로 고침하면 아무것도 변경되지 않은 것을 볼 수 있습니다. 컨트롤러가 작동하는지 여부를 어떻게 알 수 있을까요?



한 가지 방법은 `connect()` 메소드에 로그 문을 넣는 것인데, 컨트롤러가 문서에 연결될 때마다 **Stimulus**가 호출합니다.



**hello_controller.js**에서 `connect()` 메소드를 다음과 같이 구현하십시오.



```javascript
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  connect() {
    console.log("Hello, Stimulus!", this.element)
  }
}
```



페이지를 다시로드하고 개발자 콘솔을 여십시오. **Hello, Stimulus!** 가 보이고 이어서 <div> 내용이 보이게 됩니다.



## 액션은 DOM 이벤트에 응답한다

이제 "Greet" 버튼을 클릭 할 때 로그 메시지가 표시되도록 코드를 변경하는 방법을 살펴 보겠습니다.



`connect()` 이름을 `greet()` 로 바꾸어 시작하십시오.



```javascript
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  greet() {
    console.log("Hello, Stimulus!", this.element)
  }
}
```



버튼의 클릭 이벤트가 트리거되면 `greet()` 메소드를 호출하려고 합니다. **Stimulus**에서는 이벤트를 처리하는 컨트롤러 메소드를 *액션 메소드* 라고합니다.



액션 메소드를 버튼의 **click** 이벤트에 연결하려면 **public/index.html**을 열고 마술같은 효과를 내는 **data-action** 속성을 버튼에 추가하십시오.



```html
<div data-controller="hello">
  <input type="text">
  <button data-action="click->hello#greet">Greet</button>
</div>
```



> **액션 기술자(Action Descriptior)**에 대한 설명 :
>
> ----------------------------------------------------------
>
> 
>
> **data-action** 값인 **click->hello#greet**을 "**액션 기술자**"라고 합니다. 각 부분에 대한 설명은 다음과 같습니다. 
>
> 
>
> - `click` 은 이벤트 이름입니다.
> - `hello` 는 컨트롤러 식별자입니다.
> - `greet` 는 호출할 액션 메소드 이름입니다. 



브라우저에 페이지를 로드하고 개발자 콘솔을 여십시오. "Greet" 버튼을 클릭하면 로그 메시지가 나타납니다.



## 타겟(Target)은 주요 HTML 요소를 컨트롤러 속성으로 매핑한다

텍스트 필드에 입력한 이름에 대해서 hello라고 인사를 하도록 액션을 변경한 후 연습을 마치겠습니다.



그러기 위해서는 먼저 컨트롤러 내부에서 입력 요소에 대한 참조가 필요합니다. 그런 다음 **value** 속성을 읽어 내용을 가져올 수 있습니다.



**Stimulus**를 사용하면 중요한 HTML 요소를 **타겟**으로 표시할 수 있으므로 해당 속성을 통해 컨트롤러에서 쉽게 참조 할 수 있습니다. **public/index.html**을 열고 입력 요소에 마술같은 효과를 보이는 **data-target** 속성을 추가하십시오.



```html
<div data-controller="hello">
  <input data-target="hello.name" type="text">
  <button data-action="click->hello#greet">Greet</button>
</div>
```



> **타겟 기술자(Target Descriptior)**에 대한 설명 :
>
> ----------------------------------------------------------
>
> 
>
> **data-target** 값인 **hello.name**을 "**타겟 기술자**"라고 합니다. 각 부분에 대한 설명은 다음과 같습니다. 
>
> 
>
> - `hello` 은 컨트롤러 식별자입니다.
> - `name` 는 타겟 이름입니다. 



컨트롤러의 타겟 정의 목록에 **name**을 추가하면 **Stimulus**가 자동으로 **this.nameTarget** 속성을 만들어 첫 번째로 매치되는 타겟 요소를 반환합니다. 이 속성을 사용하여 요소의 값을 읽고 인사말 문자열을 만들 수 있습니다.



함께 해 봅시다. **hello_controller.js**를 열고 다음과 같이 업데이트 합니다.



```javascript
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "name" ]

  greet() {
    const element = this.nameTarget
    const name = element.value
    console.log(`Hello, ${name}!`)
  }
}
```



그런 다음 브라우저에서 페이지를 다시 로드하고 개발자 콘솔을 엽니다. 입력 필드에 이름을 입력하고 "Greet" 버튼을 클릭하십시오. Hello, world!



## 컨트롤러는 리팩토링 작업을 간소화 한다

Stimulus 컨트롤러는 자바스크립트 클래스의 인스턴스이므로 자신의 메소드는 이벤트 핸들러 역할을 수행할 수 있습니다. 이는 우리가 원하는 대로 표준 리팩토링 기술을 보유할 수 있음을 의미합니다. 예를 들어, **name** 이라는 getter를 추출하여 `greet()` 메소드를 정리할 수 있습니다.



```javascript
// src/controllers/hello_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "name" ]

  greet() {
    console.log(`Hello, ${this.name}!`)
  }

  get name() {
    return this.nameTarget.value
  }
}
```



## 요약 및 다음 단계

축하합니다. 방금 첫 **Stimulus** 컨트롤러를 작성했습니다!



지금까지 프레임워크의 핵심 개념인 컨트롤러, 식별자, 액션 및 타겟을 다루었습니다. 다음 장에서는 **Basecamp**에서 가져온 실제 컨트롤러를 구축하기 위해 이들을 통합하는 방법을 살펴 보겠습니다.



다음: [Building Something Real](https://stimulusjs.org/handbook/building-something-real)