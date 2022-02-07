---
title: Spring Rest Docs & OAS 의 필요성
tags: [api,restdoc,oas]
---

테스트를 통해 API 문서가 코드와 일치됨을 보장해주는 `SPRING REST DOCS` 과 뛰어난 범용성을 자랑하는 `OPEN API SPEC` (OAS 3) 의 조합으로 개발된 API 활용도를 극대화 시키는 법을 소개합니다. 모든 API 문서를 한 곳으로 모을 수 있는 것은 보너스입니다.

<!--truncate-->

## SPRING REST DOCS 과 OAS 조합이 필요한 이유
개발된 API 가 목적에 맞게 제대로 이용되려면 문서화는 필수적입니다. 저는 예전부터 테스트 코드를 통해 API 문서가 생성되는 SPRING REST DOCS 을 사용해왔습니다. API 문서를 위한 테스트를 만들어야 한다는 점은 분명히 번거로웠지만, API 문서가 코드와 일치되지 않아 발생할 수 많은 이슈들에 비하면 별거 아니라고 생각합니다. SPRINT REST DOCS 가 아니라면 지금까지도 못 들어보지 않았을까 싶은 [AsciiDoc](https://asciidoc.org/) 이 불만이긴 했습니다만, 마크다운과 비슷한 사용법에 러닝커브가 거의 없다시피하여  잘 써왔습니다. 하지만 색다른 방법을 찾아봐야 하는 이유는 도구 자체가 아니라, 도구를 통해 만들어진 문서 접근에서 발생했습니다.

### 계속해서 늘어나는 API 서버와 분산되는 API 문서

서비스가 확장함에 따라 API 엔드포인트가 늘어나는 것은 불가피했고, MSA 환경에서 개발되고 있는 제품인지라, API 추가는 API 서버의 추가로 이어졌습니다. 각 서버마다 REST DOCS 을 통해 만들어지는 API 문서는 각 서버의 특정 엔드포인트로 접근할 수 있었고, 우리 팀은 여기저기 흩어져있는 API 문서를 찾아다니며 개발을 해야하는불편함을 겪어야했습니다.

또한 자바가 아니라, 다른 언어로 개발된 API의 경우 REST DOCS 을 사용할 수 있는 방법이 없었고, 이런 이유로 REST DOCS 을 사용하지 않거나 사용하지 못하는 API 서버는 사내 Notion 에 API 문서를 작성해 공유하도록 하였으나, 역시나 크고 작은 수정 사항들이 빈번하게 동기화되지 않았고, 노션에 있는 API 문서는 신뢰를 잃어가고 있었습니다.

### 어떻게 하면 사내에 있는 모든 API 를 한 곳에서 볼 수 있을까?

어떻게 하면 여기 저기 흩어져있는 API 를 한 곳에서 볼 수 있을까요? 그래서 사내에 있는 모든 기능과 리소스를 최대한 활용할 수 있는 환경을 만들 수 있을까요? 처음에는 각 서버에 있는 SPRING REST DOCS 으로 생성된 html 문서를 모아서 한 웹 서버에서 호스팅을 고려해보았습니다만, 그 각각의 문서 링크를 담고 있는 별도 페이지가 필요했고, 그 곳에는 Notion 링크도 들어가야 했습니다. 이렇게 하면 관리가 잘 될 수 있을까요? 모든 API 서버가 코드를 기반하여 같은 형태(인터페이스)로 API 스펙을 정의한 결과물을 만들어 낼 수는 없을까요?

이 시점에서 REST DOCS 을 잠깐 포기하고 다른 대안을 찾아보기 시작했습니다. [API 블루프린트](https://apiblueprint.org/), [스웨거](https://swagger.io/) 과 같은 오프소스부터, 최근 자주 보이는 [리드미닷컴](https://readme.com/), [Stoplight](https://stoplight.io/) 유료 솔루션도 검토해보았죠. 유료 솔루션을 알아보니  정의된 API 를 미려한 사이트로 호스팅해주는 역할을 주로하고 API Spec 자체는 개발자가 만들어서 넣어야했습니다. (어찌보면 당연한 이야기...) 그런데 문서를 읽다보니 `OAS 3`, `Swagger` Support 라는 문구가 여기저기서 보이더군요. Swagger는 사용해봤고, OAS 3 는 Open Api Specification v3 의 축약어였습니다. 여기에서 해결방법이 어렴풋 보이는 것 같았습니다.

### 그래, 관건은 API Specification.

위에서 언급한 '모든 API 서버가 코드를 기반하여 같은 형태(인터페이스)로 API 스펙을 정의한 결과물을 만들어 낼 수는 없을까요?' 은 저만 고민했던 것은 아니였습니다. 이미 옛날부터 선배들이 고민해왔고, 그에 대한 정의도 만들어 놓았습니다.

> OpenAPI v3.0 was released in July, 2017, by the OpenAPI Initiative, a consortium of member companies who want to standardize how REST APIs are described. There are various other approaches to API description  
>
> OpenAPI v3.0은 REST API 설명 방식을 표준화하려는 회원사 컨소시엄인 OpenAPI Initiative에서 2017년 7월에 출시했습니다. API 설명에 대한 다양한 접근 방식이 있습니다.
> 
> [Stoplight - How to choose your api specification](https://stoplight.io/api-design-guide/basics/#how-to-choose-your-api-specification)

여기서 소개하는 접근 방식은 OpenAPI, JSON Schema, API Blueprint, RAML 이며, 추천하는 녀석은 OpenAPI v3 입니다. 여기 저기서 봤던 `OAS3` 바로 그 녀석이죠. OAS3 에 대해서 자세히 알고 싶다면 [여기](https://swagger.io/specification/) 에서 확인할 수 있습니다.

이제 각 API 서버가 어떤 형식으로 표준화된 결과물을 생산해야하는지는 결정되었습니다. OAS 스펙에 맞는 형태로 yaml 또는 json 파일을 떨궈주면, 그 파일을 이쁘게 보여줄 도구와 서비스는 널려있습니다. 뿐만 아니라 OSA3 는 기계, 도구가 이해할 수 있도록 설계되었다고 하니, 일단 OAS 스펙 파일만 있으면 뭐든지 할 수 있을 것 같네요. 자 그럼 어떻게 이 파일을 API 서버로부터 얻을 수 있을까요? 사람이 하나하나 작성할 형태는 아닌데 말이죠.

### 스웨거를 다시 써야 할까?

OAS 는 API 스펙을 나타내는 가장 대표적인 포맷이라 각종 언어에서 여러가지 프로젝트들이 지원합니다. 여기서는 대표적으로 Spring 쪽에 [SpringDoc Project](https://springdoc.org/) 와 Nodejs 쪽에는 [Swagger-jsdoc](https://www.npmjs.com/package/swagger-jsdoc) 이라는 프로젝트가 있습니다. 특히나 스웨거는 OAS 적극 지원하고 있으며, 이전부터 자바/스프링 진영에서도 많이 쓰이는 프로젝트 입니다. 어찌보면 SPRINT REST DOC 보다 더 많이 쓰이는 녀석일지도 모르겠네요. 스프링에서 어노테이션 몇 개로 기본적인 문서를 뚝딱 만들어주는 도구니까요. 다만 예전에 스웨거를 써 본 입장에서 컨트롤러 코드에 API 설명를 위한 코드가 너무 덕지덕지 붙어있어서 가독성이 떨어지는 단점이 있었습니다. 제품 코드와 동기화도 보장하지 않구요.

### Rest Docs 이 Open API Spec 을 떨궈줄 수만 있다면.

REST DOCS 사용을 포기하고 여기까지 왔지만, 다시금 REST DOCS 이 그리워지는 순간입니다. REST DOCS 이 완성된 HTML 문서를 최종 결과물로 생성하는게 아니라, OPEN API Spec 으로 생성할 수만 있다면, 많은 것들이 해결될텐데 말이죠. 그런데! 그렇게 명확한 방향을 잡고 구글링을 하니, 저와 같은 고민 끝에 [그 해답을 얻어 낸 분](https://taetaetae.github.io/posts/a-combination-of-swagger-and-spring-restdocs/) 이 있었습니다. 그리고 여기서 발견한 이 모든 것을 해결해주는 **[restdocs-api-spec](https://github.com/ePages-de/restdocs-api-spec) 프로젝트!** 기존 클래스들을 랩핑해서 만들어놔서 Import 만 바꿔줘도 기존 코드에 큰 변화없이 적용이 가능했습니다. 이제 이 라이브러리를 사용해서 REST DOCS 테스트 코드를 만들면, OPENAPI 3 스펙으로 파일이 뚝 떨어집니다. 유레카!

## 마치며

분산된 API 문서들, 이 문서를 한 곳으로 모으고자 했던 갈망(?) 과 `OAS` 와`restdocs-api-spec` 이라는 라이브러리를 만나게 되기까지에 여정을 소개해드렸습니다. 다음 글에서는 본격적으로 `restdoc-api-spec` 을 사용해서 OAS 파일을 생성하고, 스웨거에서 파일을 보기 좋은 형태로 서빙하는 부분을 적어볼께요. 긴 글 읽어주셔서 감사합니다.