---
title: "서버 자원 - Disk"
excerpt: ""

categories:
  - Server

toc: true
toc_sticky: false

date: 2023-02-12
last_modified_at: 2023-02-12
---

# Disk

## 디스크란

디스크는 반영구적으로 데이터를 저장하는 보조 기억 장치이다.

디스크는 크게 원형 디스크를 사용하는 HDD(Hard Disk Drive)와 플래시 메모리를 사용하는 SSD(Solid State Disk)로 나눌 수 있다.

HDD는 가격이 저렴하고 수명이 길다는 장점이 있지만 물리적인 움직임이 필요한 만큼 데이터 처리 속도가 매우 느리다.

그래서 요즘은 전자적 특성을 사용하는 SSD를 많이 사용하는 추세이다. 

하지만 SSD를 사용하더라도 전송 속도, 대역폭 등을 고려하면 CPU와 메모리에 비해 데이터 처리 속도는 여전히 느리다.

따라서 디스크 I/O가 서버 성능의 병목 지점이 될 수 있기 때문에 주의가 필요하다.

만약 디스크 I/O가 병목 지점이 된다면 CPU 성능을 높여도 효과가 없으므로 디스크 성능을 높이거나 I/O 횟수를 줄여야 한다.

디스크 성능은 데이터 처리 속도 이외에도 다양한 요소들의 영향을 받는다.

- 스토리지 구성 방식(ex. DAS, NAS, SAN, ..)
- RAID 수준(ex. RAID1, RAID10, RAID5, ..)
- 파일 시스템 종류(ex. EXT, FAT, NTFS, ..) 
- 인터페이스 및 통신 방식(ex. SATA/IDE, SATA/AHCI, PCIe/AHCI, PCIe/NVMe, ..)

<br>

그럼 대표적인 디스크 모니터링 지표인 디스크 사용률, 디스크 처리량, 디스크 소요 시간에 대해 알아보자.

<br>

## 디스크 사용률

디스크 사용률은 디스크 전체 용량 대비 사용 중인 디스크 용량 비율(%)이다.

디스크 용량이 부족하면 서버를 정상적으로 운영할 수 없으므로 지속적인 모니터링이 필요하다.

<br>

## 디스크 처리량

디스크 처리량(Disk Read/Write Bps)은 초당 디스크에 읽고 쓰는 바이트 수이다.

디스크 처리량은 IOPS(Input/Output Operations Per Second) * Block Size 를 통해 구할 수 있다.

IOPS는 초당 디스크에 읽고 쓰는 횟수를 의미하고 Block Size는 블럭(I/O 작업 단위)의 크기를 의미한다.

디스크 처리량은 디스크의 전송 속도를 고려해야 한다. 만약 이를 초과할 경우 I/O 작업이 지연될 수 있다.

> SATA SSD의 최대 전송 속도는 550MBps, NVMe(PCIe 3.0) SSD의 최대 전송 속도는 3,500MBps 이다. 


<br>

## 디스크 소요 시간

디스크 소요 시간은 대기 시간(Wait Time)과 서비스 시간(Service Time)을 합친 시간이다.

대기 시간은 해당 디스크가 다른 스레드가 요청한 데이터를 읽고 있어 I/O Queue에서 대기하는 시간을 의미하고, 서비스 시간은 대기가 끝나고 요청한 데이터를 디스크에서 읽어오는데 걸리는 시간을 의미한다.

일반적으로 디스크 소요 시간은 2~3ms 이내이고 과부하 시 소요시간은 20~30ms 이내이다.


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
- `Mounted on` : 마운트 된 디렉토리

### du 도구를 사용한 디렉토리 디스크 사용량 확인
```bash
# /home 디렉토리의 디스크 사용량 (-h 옵션을 쓰면 K/M/G 단위로 표시, --max-depth=1 옵션을 쓰면 현재 디렉토리에서만 확인)
$ du -h --max-depth=1 /home
8.3G	/home/heesu/develop
4.0K	/home/heesu/Public
 27G	/home/heesu
```

### iostat 도구를 사용한 디스크 I/O 확인
```bash
# iostat [모니터링 간격] [모니터링 횟수]
$ iostat 1 1
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.16    0.01    0.10    0.01    0.00   99.72

Device      tps    kB_read/s    kB_wrtn/s    kB_dscd/s    kB_read    kB_wrtn    kB_dscd
nvme0n1    1.59         2.36        61.77       336.42   23279079  609373893 3318774448
sda        0.01         0.00         0.00         0.00      29474          0          0
```
- `%user` : 사용자 모드에서 CPU가 소비된 시간의 비율
- `%nice` : 사용자 모드에서 nice(낮은 우선 순위) 프로세스를 실행하는데 소요된 시간의 비율
- `%system` : 시스템 모드에서 CPU가 소비된 시간의 비율
- `%iowait` : CPU가 디스크 I/O 대기를 위해 idle 상태로 소비한 시간의 비율
- `%steal` : Xen 등 OS 가상화를 사용하고 있을 경우, 다른 가상 CPU의 계산으로 대기한 시간의 비율
- `%idle` : CPU가 디스크 I/O 대기 등으로 대기되지 않고 idle 상태로 소비한 시간의 비율
- `Device` : /dev 디렉토리에 나열된 디바이스 이름
- `tps` : 해당 디바이스에서 초당 처리한 I/O 작업 개수
- `kB_read/s` : 해당 디바이스로부터 읽어들인 초 당 데이터 용량(kB)
- `kB_wrtn/s` : 해당 디바이스에서 쓰여진 초 당 데이터 용량(kB)
- `kB_dscd/s ` : 해당 디바이스에서 삭제된 초 당 데이터 용량(kB)
- `kB_read` : 해당 디바이스로부터 읽어들인 데이터 총량(kB)
- `kB_wrtn` : 해당 디바이스에서 쓰여진 데이터 총량(kB)
- `kB_dscd` : 해당 디바이스에서 삭제된 초 당 데이터 총량(kB)


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
- `rkB/s` : 디스크에서 읽은 초당 데이터 용량(과거에는 섹터 단위로 표현되었으며 섹터 크기는 512 bytes)
- `wkB/s` : 디스크에서 쓴 초당 데이터 용량
- `sreq-sz` : 디스크 I/O 요청의 평균 크기(kB)
- `aqu-sz` :디스크 I/O 요청의 평균 queue 길이
- `await` : 디스크 I/O 요청이 처리되는 평균 시간(ms) (대기 시간 + 서비스 시간)
- `%util` : 디스크 I/O 요청을 실행한 CPU 시간의 비율

### lsof 도구를 사용한 프로세스 작업 파일 확인
```bash
# 1234 PID를 가진 프로세스가 사용 중인 파일 목록 출력
$ lsof -p 1234

# Desktop 디렉토리 내 프로세스가 사용 중인 파일 목록 출력
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
  - `cwd` : 최근에 작업한 디렉토리
  - `rtd` : 루트 디렉토리
  - `mem` : 메모리 매핑 파일
  - `txt` : 프로그램 텍스트(코드 혹은 데이터)
  - (숫자)`r` : FD 번호와 Read Access
  - (숫자)`w` : FD 번호와 Write Access
  - (숫자)`u` : FD 번호와 Read and Write Access
- `TYPE` : 파일 종류
  - `DIR` : 디렉토리
  - `CHR` : Charactor Special File
  - `REG` : Regular File
  - `unix` : Unix Domain Socket
- `DEVICE` : 장치 번호
- `SIZE/OFF` : 파일 크기 혹은 오프셋
- `NODE` : 노드 번호
- `NAME` : 파일명

<br>
