---
title: "FFmpeg 명령어 모음"
excerpt: ""

categories:
  - Multimedia_Framework

toc: true
toc_sticky: false

date: 2024-02-06
last_modified_at: 2024-02-06
---

## 서론

내가 평소에 자주 사용하는 FFmpeg 명령어들을 정리해 보았다.

<br>

## FFmpeg 명령어 구조

FFmpeg 명령어는 입력 파일을 읽어서 옵션에 맞게 처리한 후, 출력 파일에 쓰는 구조로 되어 있다.

```bash
$ ffmpeg [global_options] {[input_options] -i ${input_file}} ... {[output_options] ${output_file}} ...
```

다음은 대표적인 명령어 옵션들이다.

- `-i ${input_file}` : 입력 파일 지정
- `-re` : 기본 프레임 속도(x1)로 입력 읽기 (라이브 스트리밍 용도로 적합)
- `-stream_loop ${number}` : 입력 스트림 반복 횟수 지정 (-1이면 무한 루프)
- `-vcodec ${video_codec}` or `-c:v ${video_codec}` : 비디오 코덱 지정 (copy이면 기존 스트림 복사)
- `-acodec ${audio_codec}` or `-c:a ${audio_codec}` : 오디오 코덱 지정 (copy이면 기존 스트림 복사)
- `-vf ${video_filter}`, `-af ${audio_filter}` : 비디오/오디오 필터 지정
- `-b:v ${video_bitrate}`, `-b:a ${audio_bitrate}` : 비디오/오디오 비트레이트 지정
- `-map %{}` : 입출력 스트림을 수동으로 매핑
- `-f ${container_format}` : 입출력 파일 포맷 강제 적용 (없으면 자동으로 입력 포맷을 감지하고 확장자명을 기반으로 출력 포맷을 지정)


자세한 옵션 정보는 [FFmpeg 공식 문서](https://www.ffmpeg.org/ffmpeg-all.html)에서 확인할 수 있다.

또한, [FFmpeg 위키](https://trac.ffmpeg.org/wiki)에 있는 상황별 가이드도 유용하다.

- [H.264 Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/H.264)
- [H.265 Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/H.265)
- [Streaming Guide](https://trac.ffmpeg.org/wiki/StreamingGuide)
- [Encoding for Streaming Sites](https://trac.ffmpeg.org/wiki/EncodingForStreamingSites)

<br>

## FFmpeg 명령어 백그라운드 실행

- FFmpeg 명령어를 백그라운드로 실행하기 위해 nohup, & 조합을 많이 사용한다.
- 사실 nohup만으로도 세션 종료와 상관없이 명령어를 백그라운드에서 실행할 수 있지만, 안전하게 &와 같이 사용한다.

```bash
# 출력 로그를 저장하지 않는 백그라운드 실행
$ nohup ffmpeg ... > /dev/null &

# 표준 출력/에러 로그를 파일에 저장하는 백그라운드 실행
$ nohup ffmpeg ... > output.log 2>&1 &
```

<br>

## 동영상 구간 자르기

```bash
# -ss는 시작 시점을 의미하지만 -to는 종료 시점이 아닌 영상 길이를 의미한다.
$ ffmpeg -i ${input_file} -ss ${hh:mm:ss} -to ${hh:mm:ss} -c:v copy -c:a copy ${output_file}

# hh:mm:ss 대신 초 단위로도 표현 가능하다.
$ ffmpeg -i ${input_file} -ss ${seconds} -to ${seconds} -c:v copy -c:a copy ${output_file}
```

<br>

## RTSP 스트리밍

```bash
# RTP over TCP
$ ffmpeg -re -stream_loop -1 -i ${input_file} -c copy -f rtsp -rtsp_transport tcp ${rtsp_url}

# RTP over UDP
$ ffmpeg -re -stream_loop -1 -i ${input_file} -c copy -f rtsp -rtsp_transport udp ${rtsp_url}

# 키프레임마다 SPS, PPS NALU 추가
$ ffmpeg -re -stream_loop -1 -i ${input_file} -c copy -f rtsp -bsf:v dump_extra ${rtsp_url}
```

<br>

## RTMP 스트리밍

```bash
$ ffmpeg -re -stream_loop -1 -i ${input_file} -c copy -f flv ${rtmp_url}
```

<br>

## H.264 트랜스코딩

```bash
# H.264 30 fps 트랜스코딩
$ ffmpeg -i ${input_file} \
        -vcodec libx264 \
        -tune zerolatency \ # 저지연 스트리밍에 적합한 설정
        -g 30 \ # GOP size 30
        -x264-params cabac=1:bframes=0:keyint=30:min-keyint=30:no-scenecut \ # CABAC 활성화, B-frame 비활성화, 30 fps
        -profile:v main \ # H.264 main profile
        -acodec copy \
        -f mp4 ${output_file}

# 프레임에 실시간 타임스탬프를 추가하도록 H.264 트랜스코딩 후 RTMP 스트리밍
$ ffmpeg -re -stream_loop -1 -i ${input_file} \
       -vcodec libx264 \
       -tune zerolatency \
       -g 30 \
       -x264-params cabac=1:bframes=0:keyint=30:min-keyint=30:no-scenecut \
       -vf "settb=AVTB,setpts='trunc(PTS/1K)*1K+st(1,trunc(RTCTIME/1K))-1K*trunc(ld(1)/1K)',drawtext=fontsize=72:box=1:boxcolor=black@0.75:boxborderw=5:fontcolor=white:x=(w-text_w)/2: y=((h-text_h)/2)+((h-text_h)/4):text='%{localtime}.%{eif\:1M*t-1K*trunc(t*1K)\:d}'" \
       -f flv ${rtmp_url}
```

<br>

## RTSP/RTMP/HLS to MP4

```bash
$ ffmpeg -i ${input_url} -c copy -bsf:a aac_adtstoasc ${output_file}
```

<br>

## MP4/MOV to GIF

- `gifsicle` 패키지(GIF 이미지/애니메이션을 생성, 조작, 최적화하는 도구) 설치 필요

```bash
# MP4/MOV 포맷을 GIF 포맷로 변환 (-pix_fmt, -r 옵션을 통해 GIF 포맷의 픽셀 포맷과 FPS를 설정)
$ ffmpeg -i ${mp4_or_mov} -pix_fmt rgb8 -r 10 output.gif && 

# GIF 파일의 용량을 줄이기 위해 -03 옵션을 통해 최적화 작업 진행
$ gifsicle -O3 output.gif -o output.gif
```

<br>
