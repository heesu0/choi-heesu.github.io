---
title: "MP4/MOV to GIF"
excerpt: ""

categories:
  - Multimedia

toc: false
toc_sticky: false

date: 2024-03-01
last_modified_at: 2024-03-01
---

## 서론

영상의 용량을 줄이거나 호환성 문제로 인해 MP4/MOV 포맷을 GIF 포맷으로 변환해야 할 상황이 있을 수 있다.

이때 사용하는 명령어를 알아보자.

<br>

## 필요 커맨드 도구 설치

- `ffmpeg` : 멀티미디어 처리를 지원하는 도구
- `gifsicle` : GIF 이미지/애니메이션을 생성, 조작, 최적화하는 도구

```bash
# MacOS
$ brew install ffmpeg gifsicle

# Debian-based Linux
$ sudo apt install ffmpeg gifsicle
```

<br>

## MP4/MOV to GIF

먼저 `ffmpeg` 도구를 통해 MP4/MOV 포맷을 GIF 포맷로 변환한다. 이때 `-pix_fmt`, `-r` 옵션을 통해 GIF 포맷의 픽셀 포맷과 FPS를 설정할 수 있다.

이후 GIF 파일의 용량을 줄이기 위해 `gifsicle` 도구의 `-03` 옵션을 통해 최적화 작업을 진행한다.

```bash
$ ffmpeg -i ${MP4_or_MOV} -pix_fmt rgb8 -r 10 output.gif && gifsicle -O3 output.gif -o output.gif
```

<br>

## Reference

- https://www.youtube.com/watch?v=QKtRMFvvDL0&ab_channel=iOSeTutorials
