---
title: "멀티미디어 용어 정리"
excerpt: ""

categories:
  - Multimedia

toc: true
toc_sticky: false

date: 2021-12-21
last_modified_at: 2021-12-21
---

## Container

![image](https://user-images.githubusercontent.com/34677157/147255966-62c131fe-6356-44aa-9f1f-436045ba4944.png)

### 컨테이너(Container) 정의
- 비디오, 오디오 데이터를 담고 있는 보관함
- 동영상 확장자, 래퍼 포맷(Rapper Format) 이라 부름
- 코덱에 의해 압축된 오디오, 비디오 데이터인 스트림(Stream)을 하나 이상 가지고 있음

### Container의 역할
- 스트림을 제어할 수 있는 다양한 정보(메타데이터)를 가지고 있음
스트림(Stream) 정의
- 데이터, 패킷, 비트 등 일련의 연속성을 갖는 흐름/데이터를 의미
- 비디오, 오디오 처럼 시간에 따라 변하는 일련의 데이터를 의미
- 여기서는 코덱(Codec)을 통해 인코딩(Encoding)된 영상, 음성, 데이터 출력물을 말함

<br>

## Codec

### 코덱(Codec) 정의
- 음성 또는 영상의 신호를 디지털 신호로 변환하는 **Co**der와 그 반대로 변환하는 **Dec**oder를 합쳐서 부르는 용어
- 용량이 큰 영상을 이동하거나 보관하기 쉽게 압축하고 재생할 때 다시 복원하는 역할

### Codec이 필요한 이유
- 만약 이런 스펙의 동영상이 존재할 경우
  - Resolution : 1920*1080
  - Pixel Format : RGB24 (3Byte Per Pixel)
  - FPS(Frame Per Second) : 24
  - Play Time : 30minutes
- 1920 * 1080 * 3 * 24 * 30 * 60 = **250.28GB** 의 저장 공간 필요

### Codec 종류
- 품질이 저하되더라도 압축률을 높이는 **손실 압축 코덱**과 원본 데이터를 유지하면서 압축하는 **무손실 압축 코덱**으로 나눌 수 있음
- 압축 알고리즘에 따라 다양한 종류의 코덱이 존재

### 대표적인 Codec 종류
- Video Codec
  - H264, H265, VP8, VP9, AV1...
- Audio Codec
  - MP3, AAC, Vorbis, AC3, FLAC...

<br>

## Video Operation

![image](https://user-images.githubusercontent.com/34677157/147256102-8bf47830-35e5-46f3-ba2d-9066ebdc9a18.png)

### 인코딩(Encoding) 정의
- 아날로그 신호나 스트림 데이터로 이루어진 비디오와 오디오를 압축된 부호로 변환하는 과정

### 디코딩(Decoding) 정의
- 압축된 데이터를 본래의 아날로그 혹은 스트림 데이터로 복원하는 과정

### 트랜스코딩(Transcoding) 정의
- 코덱을 다른 종류의 코덱으로 변환하는 과정
- 디코딩 후 다른 코덱으로 다시 인코딩하는 과정이 필요

### 먹싱(Muxing = Multiplexing) 정의
- 하나 이상의 코덱 스트림을 컨테이너에 추가하는 과정
- 디먹싱(Demuxing = Demultiplexing) 정의
- 컨테이너에서 코덱 스트림을 추출하는 과정

### 트랜스먹싱(Transmuxing) 정의
- 컨테이너를 변환하는 과정으로 확장자, 포맷을 변경한다고 표현함
- 컨테이너에서 코덱 스트림을 추출하여 다른 컨테이너에 추가하는 과정
- 디먹싱 후 다시 먹싱하는 과정이 필요

<br>

## Pixel

### 픽셀(Pixel) 정의
- 화면을 표현하기 위해 사용되는 가장 작은 요소
- 픽셀 종횡비(PAR, Pixel Aspect Ratio) : 하나의 픽셀에 대한 가로 세로 비율

### 프레임(Frame) 정의
- 한개의 정지된 이미지

### 프레임률(Framerate) 정의
- 초당 보여줄 수 있는 프레임의 개수
- 단위는 fps(frame per second)

### 화면 주사 방식
- 순차주사(Progressive) : 주사선을 1초에 60씩 한꺼번에 순차적으로 출력하는 방식
  - 장점 : 고화질의 선명한 화면을 볼 수 있음
  - 단점 : 데이터량이 많아짐
- 비월주사(Interlaced) : 짝수 홀수 주사선을 번갈아가며 출력하는 방식
  - 장점 : 적은 데이터량으로도 영상을 표현할 수 있음
  - 단점 : 급격한 움직임을 보이면 잔상이 남음(Interlaced Noise), 화면이 너무 커지면 선이 보임, 깜박임이 심해 눈이 피로함, 선명하지 못함
- 현재 특별한 경우를 제외하고는 순차주사(Progressive) 방식을 사용

### 해상도(Resolution) 정의
- 이미지(프레임)을 구성하는 픽셀의 수
- (width) x (height) 형식으로 표현
- 표준 해상도 그룹을 줄여서 480p, 720p, 1080p 등으로 표현하는데 숫자는 해상도의 세로 픽셀 수를 의미하고 알파벳은 화면 주사 방식을 의미 (p는 progressive, i는 interlaced)
- 화면 종횡비(DAR, Display Aspect Ratio) : 해상도를 표현하는 가로 세로 비율
 
<br>

## Sampling

![image](https://user-images.githubusercontent.com/34677157/147256202-cb03d43d-7bd9-4616-95cb-1dbda34c1415.png)

### 표본화(Sampling) 정의
- 연속적인 시간에 대해 생성되는 데이터를 이산적인 시간에 대한 데이터로 변환하는 과정
- 이미지 관점에서는 “몇 개의 픽셀에 이 이미지를 담을 것인가”를 의미
- sampling rate : 초당 샘플링 횟수 (단위는 Hz, kHz)

<br>

## Quantization

![image](https://user-images.githubusercontent.com/34677157/147256256-ce8d1c23-619b-4ee0-ada0-087878390bbd.png)

### 양자화(Quantization) 정의
- 연속적인 데이터 표현 값을 이산적인 분포를 갖는 값으로 재구성하는 과정
- 이미지 관점에서는 “이미지 픽셀에 몇 개의 값을 사용할 것인가”를 의미
- bit depth : pixel(or sample)당 값을 표현하는데 사용하는 비트 수

<br>

## Bitrate

### 비트율(Bitrate) 정의
- 아날로그 데이터에 대해 샘플링 및 양자화를 수행할 때 생성되는 단위 시간당 비트
- bit rate = sampling rate * quantization per sample
- 2초마다 아날로그 데이터를 읽고 읽은 값마다 4bit를 할당하면 비트율은 20 bit/s

### 비디오에서 의미하는 Bitrate
- 영상이 1초당 처리하는 데이터 크기 (인코딩 과정에서 1초당 영상에 담는 bit의 수)
- 단위는 bps(bit per second)
- bit rate = width * height * bit depth * fps(frame per second)
- 동영상이 30fps, 24bit pixel depth, 480x240 해상도를 가질 경우, 초당 82,944,000bit(82.944Mbps)가 필요함

### 인코딩 시 Bitrate를 할당하는 방식
- 가변 비트레이트(VBR, Variable Bitrate)
  - 영상의 복잡도에 따라 할당하는 비트레이트 값이 결정
  - 각 프레임마다 필요한 만큼의 데이터를 사용하여 품질이 좋지만 상대적으로 인코딩 시간이 오래 걸리며 영상의 용량을 예측하기 힘듬
  - 즉, 품질이 좋지만 많은 시간, 연산, 하드웨어 점유률을 필요로 함 (스트리밍 환경에 부적합)
- 고정 비트레이트(CBR, Constant Bitrate)
  - 영상의 복잡도와 관계없이 항상 같은 양의 비트레이트를 할당
  - 영상의 품질은 VBR보다 떨어지지만 데이터 크기가 고정되어 동영상 용량을 예측할 수 있음
- 평균 비트레이트(ABR, Average Bitrate)
  - 영상의 복잡도에 따라 비트레이트를 할당하지만 평균으로 지정된 비트레이트를 유지하려함
  - VBR과 CBR의 특징을 섞어 높은 방식으로 스트리밍에도 무리가 없으며 CBR에 비해 높은 품질을 보여줌

<br>

## Chroma Subsampling

![image](https://user-images.githubusercontent.com/34677157/147256376-a69d38d9-3eb9-476d-bb2c-5205862a3916.png)

### Chroma Subsampling 정의
- 사람은 눈의 밝기(Luma, Luminance) 차이에는 민감하지만 색상(Chroma, Chrominance) 차이에는 상대적으로 둔감
- 크로마 서브샘플링은 사람의 눈에 둔감한 색차 정보를 줄이고 민감한 명도를 기반으로 영상을 압축하는 방식

### RGB 정의
- 빛의 3원색인 Red, Green, Blue를 조합하여 색을 표현하는 방식
- 하나의 픽셀을 표현할 때, 24bit(한 색당 8bit) 사용
- 일반적으로 32bit 단위가 24bit 단위보다 처리 속도가 빠르기 때문에 투명도(Alpha)를 추가하여 RGBA(32bit)도 많이 사용함 (CPU가 메모리에서 데이터를 가져올 때 32bit, 64bit 단위로 가져오기 때문에 24bit는 메모리 정렬 비용이 발생함)

### YUV 정의
- 빛의 양을 정하는 휘도(Y)와 색차(U, V)를 사용하는 방식
- 디지털에서는 U, V 대신 RGB의 파랑(Cb)과 빨강(Cr)을 사용
- 대표적으로 YUV444, YUV422, YUV420이 있음

<br>

## GOP

![image](https://user-images.githubusercontent.com/34677157/147257251-1ac16efa-94e6-4b7f-ac5b-00b025190aaf.png)

### GOP(Group of Pictures) 정의
- 몇 장의 전후 화면 데이터를 한 묶음으로 하는 영상 데이터 단위
인코딩된 이미지 프레임 그룹

### GOP의 필요성
- 비디오를 압축할 때 프레임을 개별적으로 압축하는 것보다 하나의 프레임과 주변 프레임 간의 상관관계를 이용하여 압축을 하는 것이 효율적임
- 이때 GOP 단위로 프레임을 묶어서 압축을 진행함

### GOP 특징
- 임의 시점으로 접근(Random Access)이 GOP 단위로 가능
GOP가 가지고 있는 I-frame, P-frame, B-frame 의 비율에 따라 압축비가 달라짐
- 한 장 이상의 I-frame을 가지고 있어야 함
- 하나의 GOP로 묶인 프레임의 수를 GOP 수(GOP number, GOP N)이라고 하며 GOP 수가 클수록 압축률은 올라가지만 화질은 상대적으로 떨어짐

### GOP의 구성 요소
<br>
![image](https://user-images.githubusercontent.com/34677157/147257383-b49ced28-a846-4f4d-8e52-c419c7efb329.png){: .align-center}

- I-frame(Intra Frame)
  - 하나의 온전한 이미지를 저장하고 있는 프레임으로 디코딩 시 다른 프레임의 도움이 필요 없음
  - 다른 프레임을 디코딩할 때 키 역할을 하여 키 프레임(Key Frame)이라 부름
- P-frame(Predicted Frame)
  - 순 방향 예측 프레임
  - 바로 이전에 위치한 I-Frame을 기준으로 바뀐 부분의 내용을 담고 있는 프레임
  - I-frame 용량의 33% 크기
- B-frame(Bidirectional Frame)
  - 양 방향 예측 프레임
  - I-frame과 P-frame 사이에서 양쪽 프레임을 모두 참조하여 바뀐 부분의 내용을 담고 있는 프레임
  - P frame의 용량의 33% 크기

<br>