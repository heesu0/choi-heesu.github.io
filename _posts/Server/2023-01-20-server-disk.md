---
title: "(WIP) - 서버 자원 - Disk"
excerpt: ""

categories:
  - Server

toc: true
toc_sticky: false

date: 2023-01-20
last_modified_at: 2023-01-20
---

# Disk

## 디스크란

<br>

## 디스크 사용량

디스크 I/O 작업은 CPU, 메모리보다 데이터 처리 속도가 매우 느린 만큼 서버 성능에 큰 영향을 끼친다.

따라서 디스크 사용률, 디스크 소요 시간, 디스크 처리량 등의 지표를 잘 확인해야 한다.

디스크 사용률은 디스크 전체 용량 대비 사용 중인 디스크 용량 비율(%)이다.

디스크 소요 시간은 대기 시간(Wait Time)과 서비스 시간(Service Time)을 합친 시간이다.

대기 시간은 해당 디스크가 다른 스레드가 요청한 데이터를 읽고 있어 Queue에서 대기하는 시간을 의미하고, 서비스 시간은 대기가 끝나고 요청한 데이터를 디스크에서 읽어오는데 걸리는 시간을 의미한다.

일반적으로 디스크 소요 시간은 2~3ms 이내이고 과부하 시 소요시간은 20~30ms 이내이다.

디스크 처리량(Disk Read/Write Bps)은 초당 디스크에 읽고 쓰는 바이트 수이다.

디스크 처리량은 IOPS에 블록 크기(단위 데이터 용량)를 곱하면 구할 수 있다.

> IOPS(Input/Output Operations Per Second)는 초당 디스크에 읽고 쓰는 횟수를 의미한다. 일반적으로 IOPS의 범위는 HDD는 55~180, SSD는 3,000~40,000이다.

<br>

## 디스크 모니터링 도구들

### df 도구를 사용한 디스크 사용량 확인
```bash
# 리눅스 시스템 전체의 (마운트 된) 디스크 사용량 (-h 옵션을 쓰면 K/M/G 단위로 표시)
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev             16G     0   16G   0% /dev
tmpfs           3.2G  2.3M  3.2G   1% /run
/dev/nvme0n1p5  288G   58G  216G  22% /
tmpfs            16G   12K   16G   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs            16G     0   16G   0% /sys/fs/cgroup
```
- `Filesystem` : 리눅스에 마운트된 파일 시스템 (논리 볼륨)
- `Size` : 파일 시스템 전체 용량
- `Used` : 파일 시스템 사용량
- `Avail` : 파일 시스템 여유 용량
- `Used%` : 파일 시스템 사용률
- `Mounted on` : 마운트 된 디렉터리

### du 도구를 사용한 디렉터리 디스크 사용량 확인
```bash
# /home 디렉터리의 디스크 사용량 (-h 옵션을 쓰면 K/M/G 단위로 표시, --max-depth=1 옵션을 쓰면 현재 디렉터리에서만 확인)
$ df -h --max-depth=1 /home
8.3G	/home/heesu/develop
4.0K	/home/heesu/Public
27G	/home/heesu
```

### sar 도구를 사용한 디스크 소요 시간 확인
```bash
# 1초 간격으로 디스크 소요 시간 출력
$ sar 1 -d
00시 47분 25초       DEV       tps     rkB/s     wkB/s     dkB/s   areq-sz    aqu-sz     await     %util
00시 47분 26초    dev7-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초    dev7-1      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초    dev7-2      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초    dev7-3      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초    dev7-4      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초    dev7-5      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초    dev7-6      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초    dev7-7      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초  dev259-0      2.00      0.00     36.00      0.00     18.00      0.01      5.50      1.20
00시 47분 26초    dev8-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
00시 47분 26초   dev11-0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
```
- `DEV` : 논리 볼륨
- `tps` : 디스크에 실행된 초당 전송(I/O 요청) 횟수
- `rkB/s` : 디스크에서 읽은 초당 섹터 수(섹터 크기는 512 bytes)
- `wkB/s` : 디스크에서 쓴 초당 섹터 수(섹터 크기는 512 bytes)
- `sreq-sz` : 디스크 I/O 요청의 평균 크기(섹터 단위)
- `aqu-sz` :디스크 I/O 요청의 평균 queue 길이
- `await` : 디스크 I/O 요청이 처리되는 평균 시간(ms) (대기 시간 + 서비스 시간)
- `%util` : 디스크 I/O 요청을 실행한 CPU 시간의 비율

### lsof 도구를 사용한 프로세스 작업 파일 확인
```bash
# 1234 PID를 가진 프로세스가 사용 중인 파일 목록 출력
$ lsof -p 1234

# Desktop 디렉터리 내 프로세스가 사용 중인 파일 목록 출력
$ lsof +D Desktop
COMMAND    PID  USER   FD   TYPE DEVICE  SIZE/OFF     NODE NAME
zsh     473978 heesu  cwd    DIR  259,5      4096 14942497 Desktop
ffprobe 474296 heesu  cwd    DIR  259,5      4096 14942497 Desktop
ffprobe 474296 heesu    3r   REG  259,5 109143481 14958486 Desktop/test.mp4
grep    474297 heesu  cwd    DIR  259,5      4096 14942497 Desktop
grep    474297 heesu    1w   REG  259,5     32768 14951766 Desktop/test.log
```
- `COMMAND` : 실행한 명령어
- `PID` : 프로세스 식별 번호
- `USER` : 프로세스를 실행한 사용자
- `FD` : File Descriptor
  - `cwd` : 최근에 작업한 디렉터리
  - `rtd` : 루트 디렉터리
  - `mem` : 메모리 매핑 파일
  - `txt` : 프로그램 텍스트(코드 혹은 데이터)
  - (숫자)`r` : FD 번호와 Read Access
  - (숫자)`w` : FD 번호와 Write Access
  - (숫자)`u` : FD 번호와 Read and Write Access
- `TYPE` : 파일 종류
  - `DIR` : 디렉터리
  - `CHR` : Charactor Special File
  - `REG` : Regular File
  - `unix` : Unix Domain Socket
- `DEVICE` : 장치 번호
- `SIZE/OFF` : 파일 크기 혹은 오프셋
- `NODE` : 노드 번호
- `NAME` : 파일명

<br>
<br>