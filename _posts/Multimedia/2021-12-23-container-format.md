---
title: "동영상 확장자(Container Format) 종류"
excerpt: "동영상 확장자 종류와 장단점"

categories:
  - Multimedia
tags:
  - [Multimedia]

toc: false
toc_sticky: false

date: 2021-12-23
last_modified_at: 2021-12-23
---

## 서론

인터넷에 동영상 확장자 종류를 검색하든 비디오, 오디오 코덱 종류를 검색하든 이미 수많은 정보가 나와 있다.

디테일한 정보를 나열하는 글들은 인터넷에 널려있으니 간단하게 어떠한 종류들이 있고 어떤 장단점을 가졌는지 정리해 보자.

컨테이너나 코덱에 대한 이해가 부족하면 [이전 포스트](https://choi-heesu.github.io/multimedia/container/)를 참고하자.

<br>

## 비디오 확장자(컨테이너 포맷) 종류


### MP4 (`.mp4` `.m4v`)
![image](https://user-images.githubusercontent.com/34677157/147362253-0f65293b-de39-4234-b9ce-f95fa49ca8d1.png)
- MPEG에서 개발한 멀티미디어 표준 포맷으로 **가장 일반적으로 사용되는 동영상 컨테이너**이다.
- **정식 명칭** : MPEG-4 Part 14
- **장점** : 높은 압축률을 보여주는 H.264 코덱을 주로 사용하며 비교적 적은 용량으로 좋은 품질의 영상을 볼 수 있다. 인터넷을 통한 스트리밍을 지원하여 스마트폰에서도 많이 사용한다.
- **단점** : 편집, 출력, 재생 시 높은 사양이 요구될 수 있다.


### AVI (`.avi`)
![image](https://user-images.githubusercontent.com/34677157/147362303-5b36a47c-4e40-4b84-9a6f-a1c7f73ffbd7.png)
- 마이크로 소프트에서 개발한 윈도우 표준 동영상 파일 포맷
- **정식 명칭** : Audio Video Interleave
- **장점** : 다양한 종류의 코덱을 지원하여 여러 환경에서 재생할 수 있다.
- **단점** : PC에서 재생되는 것이 목적이라 다른 기기에서는 제한이 있을 수 있고 다양한 코덱을 지원하는 만큼 실행 환경에서 지원하지 않는 코덱을 담고 있을 수 있다.


### WMV (`.wmv` `.mwa` `.asf`)
![image](https://user-images.githubusercontent.com/34677157/147362326-459e5ae6-bb49-4b3c-a0b3-b917fbcd2d02.png)
- AVI의 단점을 보완하고자 마이크로소프트에서 개발한 컨테이너. DRM, 메타 정보, 스트리밍과 같은 추가 정보를 제공한다.
- **정식 명칭** : Windows Media Video, Advanced Systems Format
- **장점** : 높은 압축률로 파일 용량을 작게 만들기 때문에 실시간으로 감상하는 스트리밍에 적합하다.
- **단점** : 리눅스나 맥에서 호환성 문제가 자주 생기는 단점이 있다.

### MPEG-PS (`.mpg` `.mpeg`), MPEG-TS (`.ts`)
![image](https://user-images.githubusercontent.com/34677157/147362389-db420e2f-2f45-4ff3-9074-08c83e30e205.png){: width="50%" height="50%"}
- MPEG-1/2 영상을 효율적으로 전송하기 위해 설계된 컨테이너이자 통신 프로토콜이다. 디지털 방송에서 많이 사용되는 포맷으로 TS는 전송, PS는 저장에 목적이 있다.
- **정식 명칭** : MPEG Program stream, MPEG Transport Stream
- **장점** : 가장 원본에 가까운 고화질의 영상을 볼 수 있다.
- **단점** : 패킷 단위로 재생되기 때문에 압축이 되지 않아 용량이 큰 편에 속한다.

### MKV (`.mkv`)
![image](https://user-images.githubusercontent.com/34677157/147362405-12e82a81-30e3-4625-b861-964d3b8ceae6.png)
- 오픈 소스로 개발되었으며 갯수 제한없이 비디오, 오디오, 그림, 자막을 한 파일안에 담을 수 있는 형식. 영화/드라마 등의 멀티미디어 콘텐츠를 담기 위한 포편적인 포맷으로 개발되었다.
- **정식 명칭** : Matroska Multimedia Container
- **장점** : 코덱 종류에 상관없이 모든 스트림을 담을 수 있다. 더 좋은 화질, 음질의 스트림을 넣을 수 있다.
- **단점** : 지원하지 않는 환경이 꽤 있다.

### MOV (`.mov`)
![image](https://user-images.githubusercontent.com/34677157/147362417-95803ed4-5cf8-47bf-98a9-d33e45f91028.png)
- Apple에서 개발한 멀티미디어 컨테이너로 MPEG-4 Part 14(H.264 / AVC) 코덱을 사용한다.
- **정식 명칭** : QuickTime
- **단점** : 윈도우 진영에서는 별도의 플러그인을 설치해야될 수 있다.
- **장점** : 실시간 스트리밍을 지원하며 VR 기능도 지원


### 3GPP, 3GPP2(`.3gp` `.3g2`)
![image](https://user-images.githubusercontent.com/34677157/147362432-14cfb926-4e26-402d-9348-c820c34d74c5.png){: width="30%" height="30%"}
- 3G 모바일 환경에서 사용하도록 만들어진 컨테이너로 MP4의 단순화 버전이다.
- **정식 명칭** : Third Generation Partnership
- **장점** : 3G 모바일 환경(느린 네트워크, 저사양)에서 매우 유용하다.
- **단점** : 최대 파일 크기와 해상도가 제한될 수 있다. 모바일 네트워크 환경이 좋아지면서 필요성이 점차 줄고 있다.


### WebM(`.webm`)
![image](https://user-images.githubusercontent.com/34677157/147362445-e0ef738b-a557-4df7-9e5f-23f4a3adc44c.png)
- 구글이 개발한 오픈소스 미디어 컨테이너 포맷. Web에 최적화된 미디어 포맷으로 HTML5에서 기본으로 지원하여 태그를 이용해 온라인 재생이 가능하다. 비디오 코덱은 VP8, VP9, AV1을 지원하고 오디오 코덱은 Vorbis, Opus를 지원한다.
- **정식 명칭** : WebM
- **장점** : H.264를 사용하는 컨테이너와 비교해 스트리밍 환경에서 더 안정적이다.
- **단점** : H.264를 사용하는 컨테이너와 비교해 압축률과 인코딩 효율이 떨어진다.

<br>