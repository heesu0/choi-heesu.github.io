---
title: "CentOS EOL"
excerpt: ""

categories:
  - Linux

toc: true
toc_sticky: false

date: 2023-09-10
last_modified_at: 2023-09-10
---

## 서론

Red Hat은 사내 표준 운영체제로 사용하던 CentOS의 지원 종료(EOL, End-Of-Life)를 발표했다.

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/bb7117b8-c11f-4216-9d73-eb8b265e0852)

> CentOS 8은 2021년 12월 31일에 지원이 종료되었으며, CentOS 7도 2024년 06월 30일에 지원이 종료 될 예정이다.

이에 대해 지금까지 CentOS를 사용했던 이유와 지원을 종료하는 이유, 그리고 대체로 사용할 수 있는 배포판에 대해 알아보자.

<br>

## CentOS 사용 이유

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/ad81a335-3fb7-4df8-a97f-b1184f5acaf5){: width="50%" height="50%"}

CentOS(Community Enterprise Operating System)는 RHEL(Red Hat Enterprise Linux)에서 파생된 리눅스 배포판이다.

RHEL은 시장 점유율 1위인 리눅스 배포판으로 안정성과 보안이 매우 뛰어나지만 유료라는 치명적인 단점을 가지고 있다.

CentOS는 RHEL 소스 코드를 그대로 가져와서 상표권 문제만 수정한 무료 배포판이다. (다만, REHL처럼 Red Hat으로부터 기술 지원 및 유지 보수를 받을 수는 없다.)

CentOS는 RHEL의 장점을 그대로 누리면서 무료로 제공되기 때문에 기업이나 기관에서 많이 사용하는 리눅스 배포판이 되었다.

<br>

## CentOS 지원 종료

2018년 8월에 Red Hat이 CentOS를 인수한 이후, CentOS는 RHEL의 Downstream으로 존재하게 되었다.

2022년 기준, Red Hat 배포판 생태계는 다음과 같다.

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/10b1fc62-b875-4cf7-831b-5e08236df92b)

- `Fedora` : 실험적인 기능을 테스트하는 리눅스 배포판
- `CentOS Stream` : RHEL에 버그 패치 및 신규 기능이 반영되기 전에 테스트 및 검증하기 위한 리눅스 배포판 (RHEL Upstream)
- `RHEL` : 안전성과 보안성을 우선시하는 상용 리눅스 배포판
- `CentOS` : RHEL에서 상표권 문제를 수정한 리눅스 배포판 (RHEL Downstream)

Red Hat 배포판 생태계를 보면, Fedora에서 다양한 기능들을 개발한 후 CentOS Stream에 먼저 적용하여 테스트와 검증을 거친 후 RHEL에 공식적으로 릴리스한다.

CentOS는 정식으로 릴리즈된 RHEL 기능을 그대로 적용한 Downstream이므로 적용은 비교적 느리지만 안정성과 보안성을 보장받을 수 있다.

하지만 앞서 이야기한 대로 Red Hat은 CentOS의 지원을 종료하고 CentOS Stream에 집중하겠다고 발표했다.

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/71b6edcf-385a-486a-841b-4db24d5f6914)

CentOS 지원이 종료된 2025년 기준, Red Hat 배포판 생태계는 다음과 같다.

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/985aa655-96ee-408a-a353-d87e85e8e9a2)

Red Hat은 공식적으로 지원하는 RHEL Downstream이 없기 때문에 리눅스 배포판을 무료로 사용하려면 CentOS Stream을 사용해야 한다.

그러나 CentOS Stream을 CentOS의 대체제로 사용하기에는 안정성과 보안성을 보장할 수 없다.

따라서 CentOS를 대체할 수 있는 다른 리눅스 배포판이 필요하다.

<br>

## CentOS 대체 배포판

### RHEL

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/3378c1fd-9c9b-41e3-9f93-5407efb838be){: width="50%" height="50%"}

가장 간단한 방법은 Red Hat의 의도대로 RHEL을 사용하는 것이다.

무료로 CentOS를 사용하던 기업 입장에서는 유료인 RHEL을 사용하고 싶지 않겠지만 개인 개발용으로는 꽤 괜찮은 선택지이다.

개인 개발 목적으로는 RHEL을 무료로 사용할 수 있기 때문이다.

Red Hat은 CentOS 지원 종료 발표 이후, 최대 16개의 서버에서 무료로 RHEL을 사용할 수 있는 정책을 발표했다.

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/f06ecd70-16d0-4643-a1e1-e3b8a9b35f57)


<br>

### Rocky Linux

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/757cc106-073d-4bc1-b7da-094b59ea8e25){: width="50%" height="50%"}

Red Hat이 CentOS 지원 종료를 발표한 후 많은 사람들이 이에 반발했다. 그 중 CentOS 프로젝트의 공동설립자 Gregory Kurtzer는 RHEL Downstream 무료 배포판인 Rocky Linux를 만들었다.

Rocky Linux는 기존 CentOS처럼 RHEL 소스 코드를 그대로 가져와 상표권 문제를 수정한 배포판이다.

Rocky Linux는 현재 많은 기업(구글, 아마존, VMware 등)의 지지를 받으며 CentOS의 대체제로 떠오르고 있다.

<br>

### Oracle Linux

![image](https://github.com/heesu0/choi-heesu.github.io/assets/34677157/ac8bb9c3-309f-4523-ae07-044dcba99ac2){: width="50%" height="50%"}

Oracle Linux는 Oracle에서 관리하는 RHEL Downstream 무료 배포판이다.

Oracle은 CentOS에서 Oracle Linux로 마이그레이션하는 스크립트를 제공하고 있으며, Red Hat보다 저렴한 기술 지원 패키지를 판매한다.

하지만 Oracle도 Red Hat처럼 언제 정책을 변경할지 알 수 없다는 우려가 있다.

<br>