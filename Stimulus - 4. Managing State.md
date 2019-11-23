# 컨트롤러 상태 관리하기

대부분의 최신 프레임워크는 항상 자바스크립트로 상태를 유지하도록 권장합니다. DOM을 서버에서 보내지는 JSON을 소비하는 클라이언트 측 템플릿이 조정하는 쓰기 전용 렌더링 대상으로 취급합니다.



**Stimulus**은 다른 접근법을 취합니다. **Stimulus** 애플리케이션의 상태는 DOM에서 속성으로 존재합니다. 컨트롤러 자체는 대부분 상태를 유지하지 못하도록 되어 있습니다. 이 접근 방식을 사용하면 초기 문서, Ajax 요청, Turbolinks 방문 또는 다른 자바스크립트 라이브러리 등과 같이 어디에서나 HTML로 작업 할 수 있으며 명시적인 초기화 단계없이 연관된 컨트롤러가 자동으로 작동합니다.



## 슬라이드 쇼 만들기

마지막 장에서 우리는 Stimulus 컨트롤러가 HTML 요소에 클래스 이름을 추가하여 문서에서 간단한 상태를 유지하는 방법을 배웠습니다. 그렇다면 단순한 플래그가 아니라 값을 저장해야 할 때는 어떻게 해야 할까.

 

현재 선택된 슬라이드 색인을 속성으로 유지하는 슬라이드 쇼 컨트롤러를 구축하여 이 질문에 대한 답을 조사할 것입니다.



여느 때와 같이 HTML로 시작합니다.



```html
<div data-controller="slideshow">
  <button data-action="slideshow#previous">←</button>
  <button data-action="slideshow#next">→</button>

  <div data-target="slideshow.slide" class="slide">🐵</div>
  <div data-target="slideshow.slide" class="slide">🙈</div>
  <div data-target="slideshow.slide" class="slide">🙉</div>
  <div data-target="slideshow.slide" class="slide">🙊</div>
</div>
```



각 슬라이드 타겟은 슬라이드 쇼에서 단일 슬라이드를 나타냅니다. 컨트롤러는 한 번에 하나의 슬라이드만 표시되도록 해야 합니다.



CSS를 사용하여 기본적으로 모든 슬라이드를 숨길 수 있으며 `slide--current` 클래스가 적용될 때만 표시됩니다.



```css
.slide {
  display: none;
}

.slide.slide--current {
  display: block;
}
```



이제 컨트롤러를 작성해 봅시다. 다음과 같이 새 파일 **src/controllers/slideshow_controller.js**를 작성하십시오.



```javascript
// src/controllers/slideshow_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "slide" ]

  initialize() {
    this.showSlide(0)
  }

  next() {
    this.showSlide(this.index + 1)
  }

  previous() {
    this.showSlide(this.index - 1)
  }

  showSlide(index) {
    this.index = index
    this.slideTargets.forEach((el, i) => {
      el.classList.toggle("slide--current", index == i)
    })
  }
}
```



컨트롤러는 각 슬라이드 타겟을 반복하여 인덱스가 일치하는 경우 `slide--current` 클래스를 토글하는 `showSlide()` 메서드를 정의합니다.



첫 번째 슬라이드를 표시하여 컨트롤러를 초기화하고 `next()` 및 `previous()` 액션 메서드가 현재 슬라이드를 진행하고 되감습니다.





> 생명주기 콜백에 대한 설명 :
>
> ----------------------------------------------------------
>
> 
>
> `initialize()` 메소드는 무슨 일을 할까요? 이전에 사용한 `connect()` 메소드와 다른 점은 무엇일까요?
>
> 
>
> 이는 Stimulus 생명주기 콜백 메소드이며 컨트롤러가 문서로 들어 가거나 떠날 때 관련 상태를 설정하거나 해제하는데 유용합니다.
>
> | 메소드       | Stimulus가 호출하는 시점                    |
> | ------------ | ------------------------------------------- |
> | initialize() | 컨트롤러가 처음 인스턴스화될 때 한번만 호출 |
> | connect()    | 컨트롤러가 DOM에 연결될 때마다 호출         |
> | disconnect() | 컨트롤러가 DOM과 연결이 끊길 때마다 호출    |



페이지를 다시 로드하고 **Next** 버튼이 다음 슬라이드로 이동하는지 확인하십시오.



## DOM에서 초기 상태 읽기

컨트롤러가 `this.index` 속성에서 상태 (현재 선택된 슬라이드)를 어떻게 추적하는지 확인하십시오.



이제 첫 번째 슬라이드 대신 두 번째 슬라이드가 표시된 상태에서 슬라이드 쇼 중 하나를 시작하려고 합니다. 마크업에서 시작 색인을 어떻게 인코딩 할 수 있겠습니까?



한 가지 방법은 HTML 데이터 속성으로 초기 색인을 로드하는 것입니다. 예를 들어 `data-slideshow-index` 속성을 컨트롤러 요소에 추가 할 수 있습니다.



```html
<div data-controller="slideshow" data-slideshow-index="1">
```



그런 다음 `initialize()` 메소드에서 해당 속성을 읽고 이를 정수로 변환 한 후 `showSlide()` 에 전달할 수 있습니다.



```javascript
initialize() {
  const index = parseInt(this.element.getAttribute("data-slideshow-index"))
  this.showSlide(index)
}
```



컨트롤러 요소에 대한 데이터 속성 작업은 **Stimulus**가 이에 대한 API를 제공할 만큼 매우 흔히 발생한다. 속성 값을 직접 읽는 대신 보다 편리한 `this.data.get()` 메소드를 사용할 수 있습니다.



```javascript
initialize() {
  const index = parseInt(this.data.get("index"))
  this.showSlide(index)
}
```



> 데이터 API에 대한 설명 :
>
> ----------------------------------------------------------
>
> 
>
> 각 **Stimulus** 컨트롤러는 `has()`, `get()` 및 `set()` 메소드를 가지는 `this.data` 객체를 가지고 있습니다. 이러한 방법을 사용하면 컨트롤러 식별자로 범위가 지정된 컨트롤러 요소의 데이터 속성에 편리하게 액세스할 수 있습니다.
>
> 
>
> 예를 들어 위의 컨트롤러에서,
>
> 
>
> - `this.data.has("index")` 는 컨트롤러 요소에 `data-slideshow-index` 속성이 있으면 true를 반환합니다.
> - `this.data.get("index")` 는 컨트롤러 요소의 `data-slideshow-index` 속성의 문자열 값을 반환합니다.
> - `this.data.set("index", index)` 는 컨트롤러 요소의 `data-slideshow-index` 속성을 index의 문자열 값으로 설정합니다.
> 
>
>
>속성 이름이 둘 이상의 단어로 구성된 경우 자바스크립트에서는 camelCase로, HTML에서는 attribute-case로 참조하십시오. 예를 들어 `this.data.get("currentClassName")` 을 사용하여 `data-slideshow-current-class-name` 속성을 읽을 수 있습니다.



`data-slideshow-index` 속성을 컨트롤러 요소에 추가 한 다음 페이지를 다시 로드하여 지정된 슬라이드에서 슬라이드 쇼가 시작되는지 확인하십시오.



## Persisting State in the DOM

데이터 속성에서 슬라이드 쇼 컨트롤러의 초기 슬라이드 인덱스를 읽어서 부트 스트랩하는 방법을 살펴 보았습니다.



그러나 슬라이드 쇼를 탐색 할 때 해당 속성은 컨트롤러의 `index` 속성과 동기화되지 않습니다. 문서에서 컨트롤러 요소를 복제하는 경우 복제 컨트롤러는 초기 상태로 되돌아갑니다.



데이터 API에 위임하는 `index` 속성에 대한 **getter** 및 **setter**를 정의하여 컨트롤러를 개선 할 수 있습니다.



```javascript
// src/controllers/slideshow_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  static targets = [ "slide" ]

  initialize() {
    this.showCurrentSlide()
  }

  next() {
    this.index++
  }

  previous() {
    this.index--
  }

  showCurrentSlide() {
    this.slideTargets.forEach((el, i) => {
      el.classList.toggle("slide--current", this.index == i)
    })
  }

  get index() {
    return parseInt(this.data.get("index"))
  }

  set index(value) {
    this.data.set("index", value)
    this.showCurrentSlide()
  }
}
```

여기에서 `showSlide()`의 이름을 `showCurrentSlide()`로 바꾸고 `this.index` 에서 읽도록 변경했습니다. `get index()` 메서드는 컨트롤러 요소의 `data-slideshow-index` 속성을 정수로 반환합니다. `set index()` 메서드는 해당 속성을 설정한 다음 현재 슬라이드를 새로 고칩니다.



이제 컨트롤러 상태는 전적으로 DOM에 있습니다.



## 요약 및 다음 단계

이 장에서는 Stimulus Data API를 사용하여 슬라이드 쇼 컨트롤러의 현재 색인을로드하고 유지하는 방법을 살펴 보았습니다.



사용성 측면에서 컨트롤러는 불완전합니다. 다음 문제를 해결하기 위해 컨트롤러를 수정하는 방법을 고려하십시오.



- 이전 슬라이드는 첫 번째 슬라이드를 볼 때 아무 것도 수행하지 않는 것 같습니다. 내부적으로 인덱스 값은 0에서 -1로 감소합니다. 대신 마지막 슬라이드 인덱스로 값을 감쌀 수 있습니까? (**Next** 버튼과 비슷한 문제가 있습니다.)
- `data-slideshow-index` 속성을 지정하지 않으면 `get index()` 메서드의 `parseInt()` 호출이 **NaN**을 반환합니다. 이 경우 기본값 0으로 되돌릴 수 있습니까?



다음으로 **Stimulus** 컨트롤러에서 타이머 및 HTTP 요청과 같은 외부 리소스를 추적하는 방법을 살펴 보겠습니다.



다음: [Working With External Resources](https://stimulusjs.org/handbook/working-with-external-resources)






