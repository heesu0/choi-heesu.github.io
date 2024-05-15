---
title: "Capturing RTMP Packets"
excerpt: ""

categories:
  - Multimedia

toc: false
toc_sticky: false

date: 2024-03-02
last_modified_at: 2024-03-02
---

## 서론

Wireshark와 tcpdump를 이용하여 RTMP 패킷을 캡처하는 방법을 알아보자.

<br>

## Wireshark, tcpdump

RTMP 패킷 캡처 방법을 설명하기 전에, Wireshark와 tcpdump를 함께 사용하는 이유에 대해 간략하게 이야기 해보겠다.

Wireshark와 tcpdump는 모두 네트워크 패킷을 캡처하고 분석하는 도구이다. 그러므로 둘 중 하나만을 사용해도 RTMP 패킷을 캡처할 수 있다.

특히 Wireshark는 뛰어난 GUI 환경을 제공하기 때문에 tcpdump보다 사용하기 쉽고 패킷 분석도 용이하다.

그러나 GUI를 사용할 수 없는 원격 서버에서 패킷을 캡처해야 하는 경우에는 가볍고 CLI로 동작하는 tcpdump가 더 적합할 수 있다.

따라서 원격 서버에서 패킷을 캡처해야 하는 경우, 각 도구의 장점을 활용하여 tcpdump를 통해 네트워크 패킷을 캡처하고 GUI 환경에서 Wireshark로 분석하는 방법을 추천한다.

이때 Wireshark와 tcpdump는 PCAP 파일 포맷을 통해 캡처한 네트워크 패킷은 주고 받는다.

> PCAP : 네트워크 패킷을 캡처하기 위한 API 혹은 캡처 데이터를 저장한 파일 포맷을 의미 (Wireshark와 tcpdump 모두 지원)

<br>

## RTMP 패킷 캡처

먼저 [tcpdump 명령어](https://www.tcpdump.org/manpages/tcpdump.1.html)를 통해 원하는 RTMP 패킷들을 캡처하고 PCAP 파일로 저장한다.

```bash
$ sudo tcpdump -i ${interface} -w capture.pcap ${expression}
```

> Wireshark와 tcpdump의 사용법은 생략하겠다.

그 다음 PCAP 파일을 Wireshark로 열고 rtmpt로 필터링하면 캡처한 RTMP 패킷들을 확인할 수 있다.

<img width="1000" alt="image" src="https://github.com/heesu0/choi-heesu.github.io/assets/34677157/13c171d2-f815-4424-8f03-f8db21bb79f3">

<br>

## RTMP 패킷 정보가 Unknown으로 표시되는 문제

캡처된 RTMP 패킷 정보가 Unknown으로 표시되는 경우가 있다.

<img width="900" alt="image" src="https://github.com/heesu0/choi-heesu.github.io/assets/34677157/4398553f-594b-4dce-b683-3cffc98b1179">

이는 일반적으로 Wireshark에서 설정한 RTMP 패킷의 최대 크기보다 캡처한 RTMP 패킷의 메시지의 크기가 더 클 때 발생하는 문제이다.

Wireshark의 설정(Preferences > Protocols > RTMPT > Maximum packet size)을 변경하면 문제를 해결할 수 있다.

<img width="800" alt="image" src="https://github.com/heesu0/choi-heesu.github.io/assets/34677157/4747d06c-fde4-47f3-8d90-e3f4352e8de9">

> Maximum packet size의 기본값은 32768이다. 여기에 들어갈 적절한 값을 모르겠으면 [RTMP 스펙상](https://rtmp.veriskope.com/docs/spec#541set-chunk-size-1:~:text=no%20message%20is%20larger%20than%2016777215%20bytes.) 메시지의 최대 크기인 16777215(0xFFFFFF)를 입력하면 된다.
