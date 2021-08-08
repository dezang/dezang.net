---
title: Java version 업그레이드 주기는?
tags: [java]
---
자바 8로 개발을 시작하고 지금까지 쭈욱 8로 개발을 하고 있습니다. 개인 토이 프로젝트는 예전부터 11로 작성해왔고 , 실무에서도 새롭게 시작하는 프로젝트들은 11을 사용하고 있지만, 자바 8로 이미 개발된 프로젝트들은 벌집을 건드리는 꼴이 될까봐 업그레이드 하지 못하고 있죠. 뭐 조만간은 반드시 해야 할 일이긴 합니다.

최근 프로젝트를 생성하다보면 14버전이나 그 상위 버전을 선택할 수 있게 되는 경우가 있습니다. 잠깐 이 버전들은 얼마나 지원되는 것일까요? 최신 버전이라고 마냥 올리는게 정답일까요? LTS 가 되는 11 다음 버전은 몇 일까요? 함께 알아봅시다.

> Just before the release of JDK 9, Oracle made a number of announcements about how the development, distribution and updating of Java would work moving forward. Rather than the multi-year, feature-driven release cycle, there are now two releases of Java each year. The dates of these are fixed (March and September) and include only the features that are ready at that time. - [What Does Long-Term Support Mean for OpenJDK?](https://www.javacodegeeks.com/2019/07/long-term-support-mean-openjdk.html)

> This faster release cadence would rapidly make providing extended support impractical, so Oracle said they would designate specific releases of the Oracle JDK binary as having long-term support (LTS). JDK 8 was designated with LTS to get things started, with JDK 11 being the first LTS release under the new model. LTS releases would then occur every three years (so every sixth JDK release).

위 내용은 간단하게 정리하면 다음과 같습니다.

- 매년 두 번 릴리즈, 날짜는 3월이랑 9월
- 11 버전이 자바 8 이후로 첫번째 LTS
- LTS 릴리즈는 3년마다 발생, 6개월 마다 업데이트 되는 버전이 6번 올라갈 때마다 임
- 따라서 다음 LTS 릴리즈는 17

조금 더 생각해보면...

- LTS 가 아닌 버전은 고작 6개월 지원... 6개월 뒤에는 버그 나오면 알아서 해결해야 함 😳
- 2021년 9월에 다음 LTS 릴리즈인 17 이 나올 예정
- 한달 남았다? (좀 더 참고 있다가 17 쓸까...)
- 앞으로 대충 21년 더 자바를 쓴다 치면... 7 번에 LTS...
- 그럼, 11, 17, 23, 29, 35, 41, 47 가 LTS
- 21년 뒤면 아이폰 32 가 나올려나... 갤노트는 31
- 자바 17 이후 버전 쯤이면 Golang 쓰고 있지 않을까?

일단 다음 `New Project` 버튼 누를 때는 17 나오기 전엔 무조건 11 로 만들다가, 곧 17 이 나오면 17 로 만들어야겠네요. 그리고 8 → 11 → 17 보다는 8 → 17 로 한번에 마이그레이션 하는게 더 좋을 것 같습니다. 물론 무조건 버전을 올리기 보다는 각 버전에서 개선된 점은 무엇인지, 무슨 이유로 그렇게 개선하게 된 것인지를 아는 것이 더 중요하겠지요. 조만간 다음 버전에서 추가된 기능을 정리해봐야겠어요.

## 참고

- [Java 11로 전환해야 하는 이유 by MS](https://docs.microsoft.com/ko-kr/azure/developer/java/fundamentals/reasons-to-move-to-java-11)
- [Java 8에서 Java 11로 전환](https://docs.microsoft.com/ko-kr/azure/developer/java/fundamentals/transition-from-java-8-to-java-11)