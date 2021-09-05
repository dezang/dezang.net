---
title: Spring Rest Docs & OAS - 시작하기
tags: [api,restdoc,oas]
---

## 개요

지난 글 [Spring Rest Docs & OAS 의 필요성](https://dezang.net/blog/2021/08/22/spring-rest-docs-oas-01) 에서 이 둘의 조합이 왜 필요한지 알아보았습니다. 이제 간단한 샘플 코드를 통해 OAS 를 사용해서 API 문서를 만들어보겠습니다.

본문에 등장하는 코드는 [깃허브](https://github.com/dezang/gs-spring/tree/main/restdoc-openapi)에서 확인할 수 있습니다.

## 시작하기 전에

이 튜토리얼은 **[restdocs-api-spec](https://github.com/ePages-de/restdocs-api-spec) 라이브러리 사용에 중점을 두고 있습니다. 따라서 아래 나열된 방법들은 상세히 설명하지 않습니다.**

- Sprint Rest Docs 를 사용하는 방법
- Spring Boot Start Web 으로 API 를 만드는 방법

restdocs-api-spec 프로젝트는 여러가지 구성 요소로 구성되어 있으며, mockmvc 를 사용한 테스트와 restassured 을 사용한 테스트 둘 다 지원합니다. 두 테스트의 큰 차이점은 MockMvc 는 `@WebMvcTest` 를 사용하고, Rest Assured 는 `@SpringBootTest` 를 사용한다는 점인데 자세한 내용은 [MockMvc VS RestAssured](https://dundung.tistory.com/229) 글을 참고되지만, 이 글이 작성된 시점은 2020년 8월로 1년이 지난 현재는 [io.rest-assured:spring-mock-mvc module](https://search.maven.org/search?q=g:io.rest-assured%20AND%20a:spring-mock-mvc) 이 존재하여 RestAssured 를 사용하더라도 전체 Bean 을 로드할 필요가 없어졌습니다.

테스트 가독성이나, 작성의 편리함을 보았을 때 RestAssured 를 사용하는게 좋아보이나, 이전에 사용 경험이 없어서, 이 글에서는 익숙한 MockMvc 를 사용하여 작성합니다. 추후 RestAssured 를 사용한 샘플도 작성해보겠습니다.

바로 RestAssured 로 시도해보고 싶은 분들은 아래 글들을 참고하면 좋을 것 같습니다.

- [REST-assured Support for Spring MockMvc](https://www.baeldung.com/spring-mock-mvc-rest-assured)
- [RestAssured 공식 홈페이지](https://rest-assured.io/)
- [Testing Spring Boot Applications With REST Assured](https://rieckpil.de/testing-spring-boot-applications-with-rest-assured/)
- [Testing Spring Boot RESTful APIs using MockMvc/Mockito, Test RestTemplate and RestAssured](https://medium.com/swlh/https-medium-com-jet-cabral-testing-spring-boot-restful-apis-b84ea031973d)

## 간단한 샘플 API 구현

먼저 사용자 정보를 조회하는 간단한 API 를 만들어보겠습니다. 그래들 설정을 해주고...

```groovy
plugins {
    id 'org.springframework.boot' version '2.5.4'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
    id 'com.epages.restdocs-api-spec' version '0.11.5'  // (1)
}

group = 'net.dezang'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc' // (2)
    testImplementation 'com.epages:restdocs-api-spec-mockmvc:0.11.5' // (2) For openapi spec
}

openapi3 { // (3)
    server = 'http://localhost:8080'
    title = 'USER-API'
    description = '사용자 리소스 API 입니다.'
    version = '0.1.0'
    format = 'yaml'
}

test {
    useJUnitPlatform()
}
```

- (1) `restdocs-api-spec` 플러그인 추가
- (2) `spring-restdocs-mockmvc` , `restdocs-api-spec-mockmvc` 의존성 추가
- (3) `openapi3` 에 설정 정보 입력

유저 정보를 조회하는 간단한 API 를 만들어줍니다.

```java
package net.dezang.restdocopenapi;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class RestdocOpenapiApplication {

    public static void main(String[] args) {
        SpringApplication.run(RestdocOpenapiApplication.class, args);
    }
}
```

```java
package net.dezang.restdocopenapi;

import lombok.Builder;
import lombok.Getter;

@Getter
@Builder
class User {
    private Long id;
    private String username;
    private String password;
    private Integer age;
    private boolean enabled;
}
```

```java
package net.dezang.restdocopenapi;

import lombok.AccessLevel;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;
import java.util.stream.Collectors;

@RestController
@RequestMapping("users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;

    @GetMapping
    public ResponseEntity<List<UserDto.Response>> list() {
        List<UserDto.Response> responses = userService.list().stream()
            .map(UserDto.Response::of)
            .collect(Collectors.toList());
        return ResponseEntity.ok(responses);
    }

    @NoArgsConstructor(access = AccessLevel.PRIVATE)
    static class UserDto {
        @Data
        @AllArgsConstructor
        static class Response {
            private String username;
            private Integer age;

            public static Response of(User domain) {
                return new Response(domain.getUsername(), domain.getAge());
            }
        }
    }
}
```

```java
package net.dezang.restdocopenapi;

import org.springframework.stereotype.Service;

import java.util.Collections;
import java.util.List;

@Service
class UserService {
    public List<User> list() {
        return Collections.singletonList(User.builder()
            .id(1L)
            .username("dezang")
            .password("!topSecret!")
            .age(20)
            .enabled(true)
            .build());
    }
}
```

우리는 컨트롤러 테스트와 이를 통해 나오는 API 문서에 관심이 있기에 레포지토리 레벨은 의도적으로 추가하지 않았습니다. 간단한 샘플이지만 도메인 객체와 응답 객체는 분리하였습니다.

## OPENAPI SPEC 문서 생성

```java
package net.dezang.restdocopenapi;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.restdocs.AutoConfigureRestDocs;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;

import java.util.Collections;

import static com.epages.restdocs.apispec.MockMvcRestDocumentationWrapper.document; // (1)
import static org.mockito.BDDMockito.given;
import static org.springframework.restdocs.mockmvc.RestDocumentationRequestBuilders.get;
import static org.springframework.restdocs.payload.PayloadDocumentation.fieldWithPath;
import static org.springframework.restdocs.payload.PayloadDocumentation.responseFields;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@AutoConfigureRestDocs
@WebMvcTest(UserController.class)
class UserApiDoc {
    @Autowired
    MockMvc mockMvc;
    @MockBean
    UserService userService;

    @Test
    void list() {
        //given
        given(userService.list())
            .willReturn(Collections.singletonList(
                User.builder()
                    .username("dezang")
                    .age(10)
                    .build()
            ));

        //when
        ResultActions resultActions = mockMvc.perform(get("/users"));

        //then
        resultActions
            .andExpect(status().isOk())
            .andDo(document("list-user",
                responseFields(
                    fieldWithPath("[].username").description("사용자 아이디"),
                    fieldWithPath("[].age").description("사용자 나이")
                )))
            .andDo(print());
    }
}
```

- (1): `MockMvcRestDocumentation` 이 아닌 `MockMvcRestDocumentationWrapper` 내 `document` 메소드를 사용하여 테스트를 작성

코드 작성은 끝났습니다. 이제 openapi3 문서를 만들기 위해 플러그인이 제공하는 테스크를 실행합니다.

```bash
./gradlew openapi3
```

위 명령어를 실행하면, 테스트가 돌고 테스트 결과물이 build 경로에 생성됩니다.

```
build
├── api-spec
│   └── openapi3.yaml
...
├── generated-snippets
│   └── list-user
│       ├── curl-request.adoc
│       ├── http-request.adoc
│       ├── http-response.adoc
│       ├── httpie-request.adoc
│       ├── request-body.adoc
│       ├── resource.json
│       ├── response-body.adoc
│       └── response-fields.adoc
...
```

우리가 원하는 파일이 저기 보이네요. `build/api-spec/openapi3.yaml` 입니다. 아웃풋 경로는 처음 공유한 build.gradle 에 openapi3 에서 설정 가능합니다. 그럼 yaml 파일을 한번 살펴볼까요?

```yaml
openapi: 3.0.1
info:
  title: USER-API
  description: 사용자 리소스 API 입니다.
  version: 0.1.0
servers:
- url: http://localhost:8080
tags: []
paths:
  /users:
    get:
      tags:
      - users
      operationId: list-user
      responses:
        "200":
          description: "200"
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/users450998075'
              examples:
                list-user:
                  value: "[{\"username\":\"dezang\",\"age\":10}]"
components:
  schemas:
    users450998075:
      type: array
      items:
        type: object
        properties:
          age:
            type: number
            description: 사용자 나이
          username:
            type: string
            description: 사용자 아이디
```

생각보다 별거 없어보이지만, 스펙 자체는 강력합니다. 이전 글 [Spring Rest Docs & OAS 의 필요성](https://dezang.net/blog/2021/08/22/spring-rest-docs-oas-01)  에 대한 확인할 수 있습니다.

## 스펙 문서 활용하기

이제 유연한 결과물을 얻었으니, OAS 를 지원하는 도구나 서비스를 활용하면 됩니다. 일단 여기서는 도커를 활용하여 Swagger 를 사용해보겠습니다.

```yaml
docker run --rm -p 80:8080 \
-v __YOUR_BUILD_PATH__/api-spec:/usr/share/nginx/html/docs/ \
-e URL=docs/openapi3.yaml \
swaggerapi/swagger-ui
```

드디어 API 문서를 볼 수 있게 되었습니다!

![Swagger UI 2021-09-05 13-56-49.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b7a62639-22b8-4e3f-88c0-99af60de66bf/Swagger_UI_2021-09-05_13-56-49.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210905%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210905T050900Z&X-Amz-Expires=86400&X-Amz-Signature=6367f793736f396cebc22bd6c50730762450f37f4815a9601f5b6ed9c2d56285&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Swagger%2520UI%25202021-09-05%252013-56-49.png%22)

## 마치며

이제 API 문서를 만들었으니 끝일까요? REST DOCS + OAS 를 시작하게 된 이유가 *MSA 구조에서 분산되어있는 API 문서를 어떻게 하면 통합해서 볼 수 있을까?*  였던 것을 기억하시나요? 각 서비스가 어떤 방법을 사용해서 OAS 라는 스펙을 지킨 문서를 생성했다면, 이 문서를 어떻게 서빙하는 것이 좋을까요? 다음 글에서는 역시 분산되어 있는 OAS 파일을 어떻게 자동으로 한 곳으로 모으고, 사용자들에게 보여줄 것인지 고민의 결과를 공유하겠습니다.