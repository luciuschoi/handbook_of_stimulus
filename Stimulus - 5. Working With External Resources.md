# 외부 리소스에 대한 작업

마지막 장에서는 데이터 API를 사용하여 컨트롤러의 내부 상태를 로드하고 유지하는 방법을 배웠습니다.



때로는 컨트롤러가 외부 리소스의 상태를 추적해야 할 수도 있습니다. 외부 리소스라는 것은 DOM 또는 **Stimulus**의 일부가 아닌 것을 의미합니다. 예를 들어, HTTP 요청 후 요청 상태가 변경되면 응답해야 할 수도 있습니다. 또는 타이머를 시작한 다음컨트롤러와 더 이상 연결이 유지되지 않으면  즉시 타이머를 중지 할 수 있습니다. 이 장에서는 두 가지 방법을 모두 설명합니다.



## HTML을 비동기적으로 로딩하기

원격 HTML 조각을 로드하고 삽입하여 페이지의 일부를 비동기적으로 채우는 방법에 대해 알아 보겠습니다. 우리는 Basecamp에서 이 기술을 사용하여 초기 페이지 로드 속도를 높이고 뷰를 보다 효율적으로 캐싱 할 수 있도록 뷰에 사용자 별 콘텐츠를 제공하지 않습니다.



서버에서 가져온 HTML로 요소를 채우는 범용 콘텐츠 로더 컨트롤러를 구축합니다. 그런 다음 이메일 받은 편지함에 표시되는 읽지 않은 메시지 목록을 로드하는 데 사용됩니다.



**public/index.html** 에서 받은 편지함을 스케치하여 시작하십시오.



```html
<div data-controller="content-loader"
     data-content-loader-url="/messages.html"></div>
```



그런 다음 메시지 목록에 HTML을 사용하여 새 **public/messages.html** 파일을 만듭니다.



```html
<ol>
  <li>New Message: Stimulus Launch Party</li>
  <li>Overdue: Finish Stimulus 1.0</li>
</ol>
```



(실제 애플리케이션에서는 서버에서 이 HTML을 동적으로 생성하지만 데모 목적으로 정적 파일만 사용합니다.)



이제 컨트롤러를 다음과 같이 구현할 수 있습니다.



```javascript
// src/controllers/content_loader_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  connect() {
    this.load()
  }

  load() {
    fetch(this.data.get("url"))
      .then(response => response.text())
      .then(html => {
        this.element.innerHTML = html
      })
  }
}
```



컨트롤러가 연결되면 HTML 요소의 `data-content-loader-url` 속성에 지정된 URL로 가져 오기 요청을 시작합니다. 그런 다음 반환 된 HTML을 요소의 `innerHTML` 속성에 할당하여 로드합니다.



브라우저의 개발자 콘솔에서 네트워크 탭을 열고 페이지를 다시 로드하십시오. **index.html**에 대한 최초의 전체 페이지 요청과 컨트롤러의 메시지 `.messages` 에 대한 후속 요청이 표시됩니다.



## 타이머를 이용한 자동 새로 고침

받은 편지함을 정기적으로 업데이트하여 항상 최신 상태로 유지하도록 컨트롤러를 변경/개선해 보겠습니다.



`data-content-loader-refresh-interval` 속성을 사용하여 컨트롤러가 콘텐츠를 다시 로드하는 빈도를 밀리 초 단위로 지정합니다.



```html
<div data-controller="content-loader"
     data-content-loader-url="/messages.html"
     data-content-loader-refresh-interval="5000"></div>
```



이제 컨트롤러를 업데이트하여 시간 간격을 확인하고 존재하는 경우 새로 고침 타이머를 시작할 수 있습니다.



```javascript
connect() {
    this.load()

    if (this.data.has("refreshInterval")) {
      this.startRefreshing()
    }
  }

  startRefreshing() {
    setInterval(() => {
      this.load()
    }, this.data.get("refreshInterval"))
  }
}
```



개발자 콘솔에서 페이지를 다시 로드하고 5 초마다 한 번씩 새 요청을 관찰하십시오. 그런 다음 **public/messages.html** 을 변경하고 받은 편지함에 나타날 때까지 기다립니다.



## 추적 중인 리소스 해제

컨트롤러가 연결되면 타이머를 시작하지만 절대 멈추지 않습니다. 즉, 컨트롤러의 요소가 사라지면 컨트롤러는 백그라운드에서 계속 HTTP 요청을 합니다.



타이머에 대한 참조를 유지하도록 `startRefreshing()` 메서드를 수정하여 이 문제를 해결할 수 있습니다. 그런 다음 `disconnect()` 메서드에서 취소 할 수 있습니다.



```javascript
disconnect() {
    this.stopRefreshing()
  }

  startRefreshing() {
    this.refreshTimer = setInterval(() => {
      this.load()
    }, this.data.get("refreshInterval"))
  }

  stopRefreshing() {
    if (this.refreshTimer) {
      clearInterval(this.refreshTimer)
    }
  }
}
```



이제 컨텐츠 로더 컨트롤러가 DOM에 연결된 경우에만 요청을 발행하도록 할 수 있습니다.



최종 컨트롤러 클래스는 다음과 같습니다. 



```javascript
// src/controllers/content_loader_controller.js
import { Controller } from "stimulus"

export default class extends Controller {
  connect() {
    this.load()

    if (this.data.has("refreshInterval")) {
      this.startRefreshing()
    }
  }

  disconnect() {
    this.stopRefreshing()
  }

  load() {
    fetch(this.data.get("url"))
      .then(response => response.text())
      .then(html => {
        this.element.innerHTML = html
      })
  }

  startRefreshing() {
    this.refreshTimer = setInterval(() => {
      this.load()
    }, this.data.get("refreshInterval"))
  }

  stopRefreshing() {
    if (this.refreshTimer) {
      clearInterval(this.refreshTimer)
    }
  }
}
```



## 요약 및 다음 단계

이 장에서는 **Stimulus** 생명주기 콜백을 사용하여 외부 리소스를 획득 및 해제하는 방법을 살펴 보았습니다.



다음으로 자신의 애플리케이션에서 **Stimulus**를 설치하고 구성하는 방법을 살펴 보겠습니다.



다음: [Installing Stimulus in Your Application](https://stimulusjs.org/handbook/installing)

