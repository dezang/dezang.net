---
title: Java 예외 처리
tags:[java]
draft: true
---


## 오류(Error)와 예외(Exception)
- 오류
    - 시스템 레벨에서 발생
    - 개발자가 미리 예측해서 처리할 수 없음, 예측할 수 있다면 예외로 봐야 함
- 예외
    - 개발자가 구현한 로직에서 발생
    - 발생 상황을 예측해서 처리 가능
## boolean return 에 대해서
## checked vs unchecked
- `RuntimeException` 은 `CheckedException`과 `UnceckedException` 을 구분하는 기준
- `RuntimeException` 과 그 자식 클래스들은`UnceckedException`

# 참고
- [Java 예외(Exception) 처리에 대한 작은 생각](https://www.nextree.co.kr/p3239/)


---
title: Spring @Transactional
tags: [spring]
---

알아두어야 할 사항들
- private method 는 `@Transactional` 이 적용되지 않는다.
- `@Transactional` 이 적용되지 않은 public method 내부에서 `@Transactional`이 적용된 public method를 호출하는 경우, `@Transactional`이 동작하지 않는다.
- readonly transaction 이 적용된 public method 내부에서 not-readonly transaction이 적용된 public method를 호출하는 경우, 모든 method는 readonly transaction으로 동작하게 된다.
- (3)의 경우는 반대로도 적용된다.

스프링은 트랜잭션을 처리하기 위해 Bean 의 프록시 객체를 생성하는데, 스프링 프록시는 타겟 클래스를 상속해서 프록시를 만들기 떄문에 상속이 불가능한 private method 는 적용 불가능합니다. final 적용된 메서드도 동작하지 않음



## 참고
- [Spring @Transactional에 대하여.](https://netframework.tistory.com/entry/Spring-Transactional%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC)
- [private와 inner method에서의 spring trasaction 동작](https://handr95.tistory.com/3)
- [Proxy형태로 동작하는 JPA @Transactional](https://cobbybb.tistory.com/17)