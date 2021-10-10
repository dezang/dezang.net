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
- 