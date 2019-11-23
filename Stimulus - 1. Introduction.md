# 소개

**Stimulus**는 겸손한 야망을 가진 JavaScript 프레임 워크입니다. 다른 프레임 워크와 달리 **Stimulus**는 애플리케이션의 전체 프런트 엔드를 넘겨 받지 않습니다. 대신 HTML 각 요소를 JavaScript 객체에 자동으로 연결하여 ***<u>HTML을 보강하도록</u>*** 설계되었습니다.



## JavaScript를 HTML에 연결하기

**Stimulus**은 페이지를 지속적으로 모니터링하여 마술과 같은 능력을 가지는 `data-controller` 속성이 나타날 때까지 기다립니다. `class` 속성과 같이 둘 이상의 값을 넣을 수 있습니다. 그러나 `data-controller` 값은 CSS 클래스 이름을 적용하거나 제거하는 대신 **Stimulus** 컨트롤러를 연결 및 연결 해제합니다.



`class`가 HTML을 CSS에 연결하는 브리지와 같은 방식으로, `data-controller`는 HTML에서 JavaScript로 연결하는 브리지와 같은 방식으로 생각하십시오.



이 토대 위에 **Stimulus**는 `data-action` 속성을 추가하여 페이지 상의 이벤트가 발생할 때 어떤 컨트롤러 메소드를 트리거하는지를 지정하고, 마술같은 효과를 보여주는 `data-target` 속성을 추가하여 컨트롤러 영역 내에서 HTML 요소를 찾을 수 있는 단초를 제공해 줍니다.



## 컨텐츠와 동작의 분리

**Stimulus**의 매직 속성들을 사용하면 CSS를 사용하여 프리젠테이션과 컨텐츠를 분리하는 것과 동일한 방식으로 컨텐츠와 동작을 명확하게 분리 할 수 있습니다. 또한 **Stimulus**의 규칙에 따라 일반적으로 연관성을 가지는 코드들은 이름별로 그룹화하는 것이 좋습니다.



이와 같이 하는 분리 작업은 특수한 기능을 가지는 재사용 가능한 컨트롤러를 빌드하는 데 도움이되므로 코드가 "자바스크립트 죽(soup)"이 되지 않도록 충분한 구조를 제공합니다.



## 가독성 있는 문서

자바스크립트 동작이 마법 속성들로 연결되면 HTML 코드의 일부를 읽고 무슨 일이 일어나고 있는지 알 수 있습니다. 6개월 후에 이 HTML 템플릿을 다시 보게 될 때 이들이 서로 어떻게 유기적으로 연결되는지를 기억해 내지 않아도 걱정할 필요가 없습니다.



또한 가독성이 좋은 HTML 마크업은 팀의 다른 사람들이 HTML 템플릿(또는 심지어 프로덕션 페이지의 개발자 콘솔)을 쉽게 읽을 수 있어서 동작을 신속하게 추적하거나 문제를 진단 할 수 있음을 의미합니다.



##  The Water’s Warm

이제 본격적으로 들어가기 전에 **Stimulus**가 어떻게 작용하는지 알아보도록 하겠습니다. 첫 번째 컨트롤러를 구축하는 방법을 배우려면 다음으로 진행하면 됩니다.



다음: [Hello, Stimulus](https://stimulusjs.org/handbook/hello-stimulus)