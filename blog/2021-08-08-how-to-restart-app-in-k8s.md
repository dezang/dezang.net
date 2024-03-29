---
title: 쿠버네티스에서 앱을 재시작 하는 방법
tags: [k8s]
---

쿠버네티스를 처음 접하고 사용해온지 2년이 넘어가는 것 같습니다. 처음에 쿠버네티스를 다루게 될 때 난감했던 부분 중에 하나가 애플리케이션을 어떻게 내렸다가 올리는지였죠. 그 방법이 시간이 지나면서 조금씩 나아졌다고 생각하는데, 그 방법들을 한번 쭉 소개해볼까 합니다. (본문에서 말하는 "서비스"는 쿠버네티스에 서비스가 아니라 "애플리케이션" or "서버"를 말합니다.)

<!--truncate-->

### 파드 삭제
```shell
kubectl delete pod <pod-name> 
```
가장 처음으로 사용했던 방법입니다. 쿠버네티스는 디플로이먼트에 선언한 상태로 현재 상태를 맞추기 위해 파드가 삭제되면 자동으로 새로 띄워줍니다. 즉 삭제만 하면 알아서 쿠버가 다시 띄워주기 때문에 재시작처럼 동작합니다. 아, 이 방법은 deployment 매니페스트로 생성된 파드만 해당됩니다. 이 방법을 쭉 써오다가 디플로이먼트 파드가 복수 개인 서비스를 재시작 할 경우에 파드를 하나씩 종료시켜야하는 문제점을 만나게 되었습니다. 물론 추후 라벨로 같은 라벨인 파드를 모두 삭제 할 수 있다는 것을 알게되었지만 그 때는 이미 더 좋은 방법을 알고 있어서... 서비스가 성장하면서 특정 서비스에 복수 개에 파드가 생성되는 경우가 많아지면서 이 방법은 더 이상 쓰지 않게 되었습니다.

### 디플로이먼트 삭제 후 재생성
```shell
kubectl delete -f <deployment-manifest>
kubectl apply -f <deployment-manifest>
```
결론부터 말하면 비추천하는 방법입니다. 디플로이먼트를 삭제하면서 잃어버리는 정보들이 많거든요. (`rollout history` 같은 정보들) 어찌 됐든 해당 방법을 사용하면 복수 개에 파드를 가진 서비스도 모두 재시작할 수 있습니다. 디플로이먼트를 지우고 다시 만드는데 안될 수가 없죠. 파드를 삭제하는 방법에 비해 장점이 또 하나 있습니다. 바로 종료 후 시작하는 시점을 사용자가 컨트롤 할 수 있다는 것이죠. 즉 위에서 소개한 파드 삭제가 종료 후 바로 시작하는 "restart" 이였다면, 디플로이먼트 삭제 후 생성은 "stop", "start" 로 이벤트 시점이 나눠지게 됩니다. 운영하다보면 잠깐 내렸다가 일정 시간 후 띄워야 하는 경우가 생기기 마련이죠. 그럼 그 때 이렇게 해야하는데 왜 비추천이냐구요? 바로 다음에 소개 시켜드릴 방법이 더 좋기 때문입니다.

### 디플로이먼트의 Scale 을 변경
```shell
kubectl scale deployment <deployment-name> --replicas 0
kubectl scale deployment <deployment-name> --replicas <n>
```
서비스 재시작을 위해서 상당히 오랫동안 써왔고 현재도 쓰고 있는 방법입니다. 다만 지금은 "stop", "start" 방식으로 종료 후 시작 시점을 제가 컨트롤 하고 싶을 때만 사용합니다. 즉시 재시작일 경우에는 마지막으로 소개할 방법을 사용하죠.

### Rollout restart 명령어 사용
```shell
kubectl rollout restart deployment <deployment-name>
```
저의 최종 종착점입니다. 사실 `rollout` 명령어를 쿠버네티스를 꽤 오랫동안 쓰고나서 알게 되서... 역시 지식이 부족하면 몸이 고생하는 법이죠. 저 명령어를 사용하면 파드를 재시작해줍니다. 다만 위에 방법처럼 단순히 파드를 삭제하고 시작해주는 것으로 끝나는게 아닙니다. 여기서 자세히 다루지는 않겠지만 서비스에 replica 수가 늘어나게 되면 배포 전략에 대해서 고민을 하게 되고 디플로이먼트에 설정하는 아래 설정을 하게 될텐데요.
```yaml
spec:
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  type: RollingUpdate
```
이 전략에 맞춰서 재시작을 해줍니다. 간단하게 설명하면 이 방법 위에서 언급한 방법들은 순간적으로 모든 파드를 재시작하게 되지만, `rollout restart` 는 사용자가 설정한대로 순차적으로 파드를 재시작하게 되는 것이죠. 무중단 재시작을 위해서라면 필수적이라고 볼 수 있습니다. 


## 마치며
지금까지 서비스를 재시작하는 방법들을 알아보았습니다. 사실 `rollout restart` 을 초기부터 알았으면 좋았겠다 싶어서 다른 분들에게 소개하고자 쓴 글이지만, 제가 지금까지 사용했던 방법들을 되새겨보면서 각 방법들에 장단점도 정리하고 싶었습니다. 혹시 또 다른 방법이 있을까요? 여러분들은 어떤 방법을 사용하시나요? 현재 댓글 기능은 없지만... 어떤 방법으로든 의견주세요 😋 


