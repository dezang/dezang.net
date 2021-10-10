---
title: 도큐사우르스에서 이미지 첨부하기
tags: [docusaurus]
---

이전에 작성했던 글들에 첨부한 이미지가 깨지는 현상이 일어나는 이유와 이를 해결하기 위한 과정을 공유합니다.

<!--truncate-->

## 이미지 경로가 깨지는 이유
저는 개인 노션에서 일차적으로 글을 작성하고, 퇴고 후 마크다운으로 변환하여 도큐사우루스를 사용하여 글을 발행하고 있습니다. 글에 필요한 이미지 첨부는 노션에서 퇴고하는 과정에서 하게 되는데, 이를 레포로 옮기는 과정에서 이미지를 어디에 올려놔야 하는지 고민하게 되었고, 저는 노션 문서를 인터넷에 공유하고 인터넷에 공유된 노션 문서에서 링크를 따와서 첨부하자! 라는 생각을 하게 됩니다. 그리고 이는 잘 동작하는 것처럼 보였습니다.

하지만 일정 기간이 지난 뒤 발행한 후 확인까지 했던 글에서 이미지가 나오지 않기 시작했습니다. 혹시나하여 과거에 같은 방법으로 이미지를 첨부했던 글들을 살펴보니 모두 이미지가 로딩되지 않고 있었죠. 직접 이미지 링크를 브라우저에서 호출해보니 다음과 같은 응답을 내뱉고 있었습니다.

```xml
<Error>
<Code>AccessDenied</Code>
<Message>Request has expired</Message>
<X-Amz-Expires>86400</X-Amz-Expires>
<Expires>2021-09-13T09:52:47Z</Expires>
<ServerTime>2021-10-10T05:16:06Z</ServerTime>
<RequestId>KCH5PJ872Q747MET</RequestId>
<HostId>L+83AtpATO8aJqVXcO2MB2jwJwNqjWAzE7duQwA6KSMZpI6qvem+EhEr3dvW4x3b4e07ZST9AKE=</HostId>
</Error>
```
제가 사용했던 이미지 링크 주소를 살펴보니 다음과 같은 형태로 되어 있었습니다. _(처음 사용하는 시점에 볼 걸 그랬어요...)_

```text
https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b4dd3281-4152-4c5c-910f-9934ef4219bd/%EA%B2%BD%EC%A0%9C%EC%A0%81_%EC%9E%90%EC%9C%A0_-_Google_Sheets_2021-09-12_12-18-37.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210912%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210912T095247Z&X-Amz-Expires=86400&X-Amz-Signature=c29ee7e21491bd43ce063b4b4f524e43a8a3764bf7224f9ed1e8ba0ed9275ae9&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25EA%25B2%25BD%25EC%25A0%259C%25EC%25A0%2581%2520%25EC%259E%2590%25EC%259C%25A0%2520-%2520Google%2520Sheets%25202021-09-12%252012-18-37.png%22
```

같은 이미지를 노션에서 원본보기를 선택해서 다시 보니 `X-Amz-Date`, `X-Amz-Signature` 부분이 변경되어 있네요.

```text
https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b4dd3281-4152-4c5c-910f-9934ef4219bd/%EA%B2%BD%EC%A0%9C%EC%A0%81_%EC%9E%90%EC%9C%A0_-_Google_Sheets_2021-09-12_12-18-37.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20211010%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211010T053959Z&X-Amz-Expires=86400&X-Amz-Signature=0edea65facd19c1508b6fe138ba0705d6fc247012eb07622d06c097d05ae15b6&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22%25EA%25B2%25BD%25EC%25A0%259C%25EC%25A0%2581%2520%25EC%259E%2590%25EC%259C%25A0%2520-%2520Google%2520Sheets%25202021-09-12%252012-18-37.png%22
```

`X-Amz-Expires=86400` 해당 링크에 대한 유효기간은 86400초 즉 하루입니다. 해당 링크는 하루가 지나면 만료되어 더 이상 사용할 수 없게 되는 것이죠. 왜 발행하고 난 뒤 확인했을 때는 멀쩡하다가 나중에 확인하니 깨져있는지가 명확해지는 순간입니다. OTL... 그렇다면 어떻게 해결해야 할까요?

## 해결 방법
노션에서 첨부한 이미지를 외부에 공개하기 위해서는 노션 문서에 **웹에서 공유**를 활성화해야 합니다. 다만 링크를 가지고 올 때 `https://www.notion.so/myusername` 주소가 아닌 외부에 공개된 주소 즉 웹에서 공유를 활성화하면 생기는 주소로 들어가서 그 웹페이지에 있는 이미지 주소를 가지고 오면 만료기간이 없는 이미지 주소를 가지고 올 수 있습니다. 그 주소는 아래와 같이 생겼습니다.

```text
https://dezangnet.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb4dd3281-4152-4c5c-910f-9934ef4219bd%2F%EA%B2%BD%EC%A0%9C%EC%A0%81_%EC%9E%90%EC%9C%A0_-_Google_Sheets_2021-09-12_12-18-37.png?table=block&id=788dc731-3d2f-4cf3-9d05-0449d3ede78e&spaceId=7104e486-8d51-4b67-825a-091ed96349bf&width=1200&userId=&cache=v2
```

다행히 어디에도 만료기간 비슷한 파라미터를 찾아볼 수 없네요. 또 위 주소에 width 를 변경하면 변경된 넓이로 리사이즈된 이미지를 가지고 옵니다. 적절하게 사용하면 유용하겠네요.

## 마치며
사실 노션말고도 이미지를 서빙해주는 서비스들은 아주 많습니다. 또 서비스형 블로그(네이버 블로그나 티스토리 같은)를 사용하면 이미지 경로에 대해서 고민할 필요도 없죠. 서비스형 블로그가 그리워지는 오늘입니다... 혹시 저처럼 노션에 올린 이미지를 다른 곳에서 쓰길 원하는 분들에게 도움이 되었으면 합니다.