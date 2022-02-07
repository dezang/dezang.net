일반 리눅스 사용자 입장에서는 최상단의 systemd utillities 인 systemctl, journalctl 등 유틸리티 사용법을 위주로 익히면 되며 이 문서에서는 systemctl 사용 방법에 대해서 설명

https://www.lesstif.com/system-admin/systemd-system-daemon-systemctl-24445064.html


https://www.kernelpanic.kr/20


systemctl status  

systemctl list-units --type=service --state=running

간편하게 말하면 service는 systemctl의 wrapper script다

출처: https://cyeongy.tistory.com/entry/linux-service와-systemctl의-차이 [기술없는 기술 블로그]