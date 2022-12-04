---
title: "서버 모니터링 (Server Monitoring)"
excerpt: ""

categories:
  - Server
tags:
  - [Server]

toc: true
toc_sticky: false

date: 2022-11-12
last_modified_at: 2022-11-12
---

# 서론

서버의 상태를 확인하거나 성능을 측정하기 위해서는 서버 자원들의 모니터링할 필요가 있다.

> 서버 자원(Server Resource)은 말 그대로 서버 컴퓨터의 자원(CPU, Memory, Disk, Network 등)을 의미한다.

대표적인 서버 모니터링 지표들과 이를 측정하기 위해 리눅스에서 사용하는 모니터링 도구에 대해 알아보자.


<br>
<br>

# CPU Usage

## CPU 사용량

CPU 사용량은 서버 성능의 핵심 지표이다.

CPU 사용량은 단위 시간당 CPU 코어를 사용한 시간의 비율(%)이다.

CPU 사용량이 많아질수록 실행 대기 중인 스레드는 CPU를 점유하지 못하고 스케줄러에 의해 실행 큐(Run Queue)에 쌓이게 되는데 실행 큐에 3개 이상 스레드가 쌓이게 되면 제시간에 작업을 수행하지 못하고 성능 저하가 발생할 수 있다.

안정적인 서버를 운영하기 위해서는 CPU 코어당 사용량은 70% 이하, 실행 큐에 있는 스레드 개수는 3개 이하로 유지하는 것이 좋다.

<br>

## CPU 사용 유형
```
CPU     %user     %nice   %system   %iowait    %steal     %idle
all      0.25      0.00      0.19      0.00      0.00     99.56
```

CPU 사용량은 사용 유형에 따라 세분화된다.

### CPU System Usage

System 사용량(`%system`)은 시스템 모드에서 사용한 CPU 시간의 비율이다.

시스템 모드는 시스템 프로그램을 실행하는 상태로 커널 레벨의 코드 실행, System Call, Context Switching 등이 있다.

일반적인 System 사용량은 전체 사용량 대비 30% 미만이다.

### CPU User Usage

User 사용량(`%user`)은 유저 모드에서 사용한 CPU 시간의 비율이다.

유저 모드는 사용자 프로그램을 실행하는 상태로 일반적인 애플리케이션 함수 호출 등이 있다.

### CPU I/O Usage

I/O Wait 사용량(`%iowait`)은 I/O 작업이 완료되기까지 기다린 CPU 시간의 비율이다.

해당 서버의 작업이 입출력 위주의 작업이 아니라면 일반적인 I/O Wait 사용량은 전체 사용량 대비 20% 미만이다. 

### CPU Idle Usage

Idle 사용량(`%idle`)은 아무 작업 없이 쉬고 있는 CPU 시간의 비율이다.

<br>

## Load Average
```
load average: 3.68, 3.39, 3.08
```

Load Average는 1분, 5분, 15분 동안 실행 중이거나 대기 된 프로세스(스레드) 개수의 평균을 의미한다.

여기서 말하는 대기 된 프로세스(스레드)는 다음 두 가지 경우를 말한다.

- R(Running or Runnable) : 실행 큐(Run Queue)에서 CPU 실행 권한이 부여되기를 기다리는 프로세스(스레드)
- D(Uninterruptible Sleep) : 대기 큐(Wait Queue)에서 I/O 작업이 완료되기를 기다리는 프로세스(스레드)

Load Average는 시스템 전체의 부하 상황을 나타내기 위한 지표로 사용하는데 이는 CPU 사용량만으로는 알 수 없는 정보를 알려주기 때문이다.

Load Average는 CPU 코어당 1.00 이상이면 작업이 지연되고 있다는 뜻으로 부하가 있다고 판단할 수 있다. (안정적인 서버 운영을 위해서는 0.7 미만을 유지하는 것이 좋다.)

또한, 같은 작업을 수행하는 두 개의 서버가 CPU 사용량이 100%인 상황에서 Load Average가 서로 다르다면 낮은 쪽이 더 성능이 좋다는 것(부하가 낮다는 것)을 알 수 있다.

<br>

## CPU 모니터링 도구들

### CPU 코어 정보 및 개수 확인
```bash
# cpu 코어 정보 확인
$ cat /proc/cpuinfo

# cpu 코어 개수 확인
$ grep -c processor /proc/cpuinfo
```

### top을 이용한 CPU 정보 확인
```bash
$ top
top - 00:02:26 up 25 days,  6:39,  3 users,  load average: 0.06, 0.02, 0.00
Tasks: 330 total,   1 running, 329 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.1 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  31814.8 total,  12214.4 free,   1448.7 used,  18151.7 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.  29747.3 avail Mem

PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
941 root      20   0  413468  21596  17232 S   2.7   0.1  32:49.01 NetworkManager
```
- `top - 00:02:26` : 현재 시스템의 시간
- `up 25 days,  6:39` : 현재 시스템에서 운영 중인 총 시간
- `3 users` : 현재 시스템에 접속 중인 사용자
- `load average: 0.06, 0.02, 0.00` : 1분, 5분, 15분 동안 실행 중이거나 대기 된 프로세스(스레드) 개수의 평균
- `Tasks: 330 total, 1 running, 329 sleeping, 0 stopped, 0 zombie`
  - 전체, 실행 상태, 대기 상태, 정지 상태, 좀비 상태인 프로세스 개수
- `%Cpu(s):  0.1 us,  0.1 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st`
  - `us` : 사용자가 사용한 CPU 시간의 비율
  - `sy` : 커널이 사용한 CPU 시간의 비율
  - `ni` : niced 사용자 프로세스(사용자가 정의한 우선 순위)를 실행하는데 소요 된 시간의 비율
  - `id` : 대기한 CPU 시간의 비율
  - `wa` : I/O 대기한 CPU 시간의 비율
  - `hi` : 하드웨어 인터럽트 사용 시간의 비율
  - `si` : 소프트웨어 인터럽트 사용 시간의 비율
  - `st` : 하이퍼 바이저가 다른 프로세스를 처리하는 동안 가상 CPU가 의도치 않게 대기하는 시간의 비율
- `MiB Mem: 31814.8 total, 12214.4 free, 1448.7 used, 18151.7 buff/cache`
  - 전체 메모리 용량, 사용되고 있지 않은 메모리 용량, 사용 중인 메모리 용량, 버퍼(Buffer Cache)/캐시(Page Cache)로 사용되는 메모리 용량
- `MiB Swap: 2048.0 total, 2048.0 free, 0.0 used. 29747.3 avail Mem`
  - 전체 스왑 영역 용량, 사용되고 있지 않은 스왑 영역 용량, 사용 중인 스왑 영역 용량, 스왑하지 않고 사용할 수 있는 메모리 용량 추정치

### ps를 사용한 CPU 정보 확인
```bash
# 현재 실행 중인 모든 프로세스 출력 및 유저명, PID, CPU 사용률, CPU 사용시간 표시
$ ps -eo user,pid,pcpu,time

# ID가 453080인 프로세스 출력 및 유저명, PID, CPU 사용률, CPU 사용시간 표시
$ ps -o user,pid,pcpu,time -p 453080

# 현재 실행 중인 모든 프로세스 출력 및 여러 프로세스 상태 정보 표시
$ ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0 171848 13328 ?        Ss   10월21   0:37 /sbin/init splash
root           2  0.0  0.0      0     0 ?        S    10월21   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   10월21   0:00 [rcu_gp]
root           4  0.0  0.0      0     0 ?        I<   10월21   0:00 [rcu_par_gp]
root           5  0.0  0.0      0     0 ?        I<   10월21   0:00 [netns]
root           7  0.0  0.0      0     0 ?        I<   10월21   0:00 [kworker/0:0H-events_highpri]
root           9  0.0  0.0      0     0 ?        I<   10월21   0:00 [mm_percpu_wq]
root          10  0.0  0.0      0     0 ?        S    10월21   0:00 [rcu_tasks_rude_]
root          11  0.0  0.0      0     0 ?        S    10월21   0:00 [rcu_tasks_trace]
```
- `USER` : 프로세스를 실행시킨 사용자
- `PID` : 프로세스 고유 식별자
- `%CPU` : CPU 사용률
- `%MEM` : 메모리 사용률
- `VSZ, RSS` : 프로세스가 확보하고 있는 가상 메모리 영역의 크기, 물리 메모리 영역의 크기
- `TTY` : 프로세스와 연결된 터미널(하드웨어 리소스)
- `STAT` : 프로세스 상태
  - `I`(Idle) : 대기 중인 커널 스레드
  - `R`(Running) : 실행이 가능한 상태(실행 큐에 있음)
  - `S`(Interruptible Sleep) :  깨울 수 있는 대기 상태(사용자 입력 대기 혹은 sleep 상태)
  - `D`(Uninterruptible Sleep) : 깨울 수 없는 대기 상태(I/O 대기)
  - `T`(Stopped) : 중지된 상태
  - `X`(Dead) : 죽은 상태(보이면 문제가 있음)
  - `Z`(Zombie, TASK_ZOMBIE) : 자식 프로세스가 exit 해서 부모 프로세스에 반환되기 전까지의 상태
  - (접미사) `<` : 높은 우선 순위
  - (접미사) `N` : 낮은 우선 순위
  - (접미사) `L` : 페이지 락 상태
  - (접미사) `s` : 세션 리더
  - (접미사) `l` : 멀티 스레드로 동작
  - (접미사) `+` : foreground process(수행 종료 전까지 다른 입력을 받을 수 없는 프로세스)
- `START` : 프로세스가 시작된 시간
- `TIME` : 프로세스가 소비한 전체 CPU 시간(User + System)
- `COMMAND` : 명령어

### sar를 사용한 CPU 유형별 사용량 확인
```bash
# 1초 간격으로 CPU 유형별 사용량 출력
$ sar 1 -u
00시 15분 29초     CPU     %user     %nice   %system   %iowait    %steal     %idle
00시 15분 30초     all      0.06      0.00      0.06      0.00      0.00     99.88
00시 15분 31초     all      0.06      0.00      0.00      0.00      0.00     99.94
00시 15분 32초     all      0.00      0.00      0.06      0.00      0.00     99.94
00시 15분 33초     all      0.00      0.00      0.00      0.00      0.00    100.00
```
- `%user` : 사용자 모드에서 CPU가 소비된 시간의 비율
- `%nice` : nice로 시케줄링의 우선도를 변경한 프로세스가 사용자 모드에서 CPU를 소비한 시간의 비율
- `%system` : 시스템 모드에서 CPU가 소비된 시간의 비율
- `%iowait` : CPU가 디스크 I/O 대기를 위해 idle 상태로 소비한 시간의 비율
- `%steal` : Xen 등 OS 가상화를 사용하고 있을 경우, 다른 가상 CPU의 계산으로 대기한 시간의 비율
- `%idle` : CPU가 디스크 I/O 대기 등으로 대기되지 않고 idle 상태로 소비한 시간의 비율

<br>
<br>

# Memory Usage

## 메모리 사용량

메모리 사용량은 전체 메모리 대비 사용한 메모리 비율(%) 혹은 용량(KB)이다.

메모리 사용량을 이해하기 위해서는 먼저 운영체제의 메모리 관리 방식을 알아야 한다.

운영체제는 프로그램이 물리 메모리에 직접 접근하지 않고 추상화된 가상 메모리(Virtual Memory)에 접근하도록 메모리를 관리한다.

가상 메모리 구조를 사용하는 이유는 다음과 같다.

1. 스왑 영역(메모리가 부족할 때 사용하는 디스크 공간)을 묶어서 물리 메모리보다 더 큰 용량의 메모리를 다룰 수 있게 해준다.
2. 물리 메모리 상에서 연속적이지 않은 메모리 영역을 하나의 연속된 영역처럼 인식하게 해준다.
3. 서로 다른 프로세스가 참조하는 가상 메모리 영역을 동일한 물리 메모리 영역에 대응시켜 내용을 공유할 수 있게 해준다. (Shared Memory, IPC)

이처럼 가상 메모리 구조를 통해 얻을 수 있는 이득이 많지만, 메모리 일부를 스왑 영역으로 옮기거나 스왑 영역으로부터 메모리를 로드하는 스와핑 작업은 디스크를 사용하는 만큼 서버에 큰 부하를 준다.

따라서 메모리 사용량이 100%여도 서버 성능에 문제가 없을 수 있지만 스와핑이 자주 발생하면 높은 확률로 서버 성능 저하가 발생한다.

즉, 안정적인 서버를 운영하기 위해서는 스와핑 발생을 최소화해야 한다.

> 앞서 프로세스 정보에서 나온 VSZ(Virtual Set Size)는 프로세스가 확보한 가상 메모리 영역의 크기를 의미하고 RSS(Resident Set Size)는 물리 메모리 영역의 크기를 의미한다.

<br>

## Page Cache

리눅스는 한번 디스크에서 읽어 들인 데이터는 가능한 한 메모리에 캐싱해서 디스크 읽기를 고속으로 처리할 수 있게 한다.
이때 메모리에 캐싱한 데이터를 Page Cache라고 한다.

> 페이지(Page) : 운영체제에서 관리하는 4kB 크기의 메모리 접근 단위

리눅스는 남아있는 메모리 공간을 최대한 Page Cache로 활용하다 보니 모니터링할 때 캐시된 메모리 영역 때문에 메모리 여유 공간이 부족해 보일 수 있다.

하지만 프로세스에 메모리가 필요할 경우, 페이지 캐시보다 메모리 할당이 우선시 되므로 걱정할 필요 없다.

> Buffer Cache와 혼동할 수 있는데 Page Cache가 파일의 내용을 저장한다면 Buffer Cache는 파일 시스템의 메타 데이터와 관련된 블록을 저장한다. 커널 2.4 버전 이후부터 Buffer Cache는 Page Cache 내부로 통합되었다.

<br>

## 메모리 모니터링 도구들

### 메모리 정보 확인
```bash
# 메모리 정보 확인
$ cat /proc/meminfo
```

### free를 사용한 메모리 사용량 확인
```bash
$ free
           total        used        free      shared  buff/cache   available
Mem:    32578316     1476356    12507092      179116    18594868    30468380
스왑:    2097148           0     2097148
```
- `total` : 전체 메모리 용량
- `used` : 사용 중인 메모리 용량
- `free` : 사용되고 있지 않은 메모리 용량
- `shared` : 공유 중인 메모리 용량
- `buff/cache` : 버퍼(Buffer Cache)/캐시(Page Cache)로 사용되는 메모리 용량
- `available` : 스왑하지 않고 사용할 수 있는 메모리 용량 추정치

### sar를 사용한 메모리 사용량 확인
```bash
# 1초 간격으로 메모리 사용률 출력
$ sar 1 -r
21시 47분 24초 kbmemfree   kbavail kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
21시 47분 25초  12507372  30468668   1277564      3.92   1184368  16681252   5068720     14.62   8261104  10571560         4
21시 47분 26초  12507372  30468668   1277564      3.92   1184368  16681252   5068720     14.62   8261104  10571560         4
21시 47분 27초  12507372  30468668   1277564      3.92   1184368  16681252   5068720     14.62   8261104  10571560         4
21시 47분 28초  12507372  30468668   1277564      3.92   1184368  16681252   5068720     14.62   8261104  10571560         4
```
- `kbmemfree` : 사용되고 있지 않은 메모리 용량(KB)
- `kbmemused` : 사용 중인 메모리 용량(KB)
- `%memused` : 메모리 사용률(%)
- `kbbuffers` : 커널 내의 버퍼로 사용되고 있는 물리 메모리 용량
- `kbcached` : 커널 내에서 캐시용 메모리로 사용되고 있는 물리 메모리 용량
- `kbswpfree` : 사용되고 있지 않은 스왑 영역 용량
- `kbswpued` : 사용 중인 스왑 영역 용량
- `kbcommit` : overcommit(메모리 초과 할당)하는 메모리 용량
- `%commit` : overcommit(메모리 초과 할당)하는 메모리 사용률
- `kbactive` : 최근에 사용된 메모리 용량
- `kbinact` : 최근에 사용되지 않은 메모리 용량(메모리가 높아지면 커널이 제거할 수 있는 메모리 페이지)
- `kbdirty` : 디스크에 쓰기 대기 중인 메모리 용량

### vmstat을 사용한 메모리 사용량 확인
```bash
# 5초 간격으로 2회 통계한 가상 메모리 정보를 출력
$ vmstat 5 2
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 12506628 1184708 17410444    0    0     0     4    1    1  0  0 99  0  0
 0  0      0 12506628 1184708 17410444    0    0     0     9   90  208  0  0 100  0  0
```
- `r`(run-queue) : CPU 할당 대기 중인 프로세스(스레드) 개수
- `b`(blocking) : 입출력나 페이징으로 대기 중인 프로세스(스레드) 개수
- `swpd` : 가상 메모리 사용 페이지 개수 
- `free` : 메인 메모리 여유 페이지 개수
- `buff(buffer)` : Buffer Cache 페이지 개수
- `cache` : Page Cache 페이지 개수
- `si`(swap in) : 초당 스왑 영역에서 읽은 양
- `so`(swap out) : 초당 스왑 영역에서 쓴 양
- `bi`(block in) : 초당 디스크(블록 디바이스)에서 읽은 블록 양 
- `bo`(block out) : 초당 디스크(블록 디바이스)에서 쓴 블록 양
- `in` : 초당 인터럽트 발생 횟수
- `cs` : 초당 Context Switching 발생 횟수
- `us` : 사용자가 사용한 CPU 시간의 비율
- `sy` : 커널이 사용한 CPU 시간의 비율
- `id` : 대기한 CPU 시간의 비율
- `wa` : I/O 대기한 CPU 시간의 비율

<br>
<br>

# Disk Usage

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

# Network Traffic (WIP)