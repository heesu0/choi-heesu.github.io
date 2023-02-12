---
title: "서버 자원 - CPU"
excerpt: ""

categories:
  - Server

toc: true
toc_sticky: false

date: 2023-01-18
last_modified_at: 2023-01-18
---

# 서론

서버 프로그램을 개발하고 운영하기 위해서는 서버에 대한 이해가 필요하다.

서버라는 말이 거창하게 느껴질 수 있지만, 서버는 결국 우리가 사용하는 PC와 같은 컴퓨터이다.

> 물론 좋은 서버는 전원을 이중화하여 안정성을 높이고 고성능의 CPU와 대용량의 메모리, 디스크 등 전용 장비를 사용한다. 하지만 우리가 사용하는 일반 PC도 언제든지 서버로 사용할 수 있다.

따라서 서버에서 이야기하는 자원(Resource)은 말 그대로 컴퓨터의 자원(CPU, Memory, Disk, Network, ..)을 의미한다.

서버 프로그램의 성능을 높이고 장애를 방지하기 위해서는 각 서버 자원을 이해하고 모니터링할 필요가 있다.

대표적인 서버 자원들과 이를 측정하기 위해 리눅스에서 사용하는 모니터링 도구에 대해 알아보자.

<br>

# CPU

## CPU란

CPU(Central Processing Unit)는 명령을 입력받아 연산을 수행하고 결과를 출력하는 중앙 처리 장치이다.

<br>

## CPU 성능

CPU의 성능은 클럭(Clock) 속도와 코어(Core)의 수로 결정된다.

클럭(Clock)은 CPU 내에서 일정한 주파수를 가지는 전기 신호로 CPU의 모든 명령어는 클럭에 동기화되어 동작한다. 클럭 속도는 초당 처리 가능한 클럭(사이클) 수를 의미하며 기가 헤르츠(GHz) 단위로 표현된다.

만약 클럭 속도가 2.4GHz라면 CPU는 대략 초당 24억 번의 명령어를 처리할 수 있다. (명령어 하나를 처리하는데 필요한 클럭(사이클) 수는 명령어 종류마다 다르다.)

코어(Core)는 CPU 내에서 명령어를 처리하는 블록을 의미한다. 코어의 수가 많을수록 동시에 처리할 수 있는 작업의 수가 증가한다. 

따라서 CPU 클럭 속도를 높이면 서버의 처리 능력을 향상할 수 있고 CPU 코어 수를 높이면 서버의 동시 처리량을 향상할 수 있다.

> 이를 보통 스케일 업(Scale Up) 이라 한다.

하지만 서버의 성능을 향상하기 위해서 무작정 스케일 업하는 것이 능사는 아니다.

CPU 클럭 속도를 계속 높이기에는 물리적인 한계와 비용 문제가 있고 처리 시간이 긴 작업을 개선하기 위해 CPU 코어 수를 높여도 만약 해당 작업이 병렬 처리를 지원하지 않으면 효과가 없을 수 있다.

따라서 서버 성능을 최적화 하기 위해서는 병목(Bottleneck) 지점을 정확하게 판단해야 한다.

<br>

## CPU 사용률

CPU 사용률은 단위 시간당 CPU 코어를 사용한 시간의 비율(%)이다.

CPU 사용률이 높아질수록 실행 대기 중인 스레드는 CPU를 점유하지 못하고 스케줄러에 의해 실행 큐(Run Queue)에 쌓이게 된다. 만약 실행 큐에 3개 이상 스레드가 쌓이게 되면 작업을 제시간에 수행하지 못하고 성능 저하가 발생할 가능성이 있다.

안정적인 서버를 운영하기 위해서는 CPU 코어당 사용률은 70% 이하, 실행 큐에 있는 스레드 개수는 3개 이하로 유지하는 것이 좋다.

<br>

## CPU 사용 유형

CPU 사용률은 사용 유형에 따라 세분화된다.

```
CPU     %user     %system   %iowait    %idle
all     49.97        5.14      0.00    44.88
```

### CPU System Usage

System 사용률(`%system`)은 시스템 모드에서 사용한 CPU 시간의 비율이다.

시스템 모드는 시스템 프로그램을 실행하는 상태로 커널 레벨의 코드 실행, 시스템 콜(System Call), 컨텍스트 스위칭(Context Switching) 등이 있다.

일반적인 System 사용률은 전체 사용률 대비 30% 미만이다.

### CPU User Usage

User 사용률(`%user`)은 유저 모드에서 사용한 CPU 시간의 비율이다.

유저 모드는 사용자 프로그램을 실행하는 상태로 일반적인 애플리케이션 함수 호출 등이 있다.

### CPU I/O Usage

I/O Wait 사용률(`%iowait`)은 I/O 작업이 완료되기까지 기다린 CPU 시간의 비율이다.

해당 서버의 작업이 입출력 위주의 작업이 아니라면 일반적인 I/O Wait 사용률은 전체 사용률 대비 20% 미만이다. 

### CPU Idle Usage

Idle 사용률(`%idle`)은 아무 작업 없이 쉬고 있는 CPU 시간의 비율이다.

<br>

## Load Average
```
load average: 3.68, 3.39, 3.08
```

Load Average는 1분, 5분, 15분 동안 실행 중이거나 대기 된 프로세스(스레드) 개수의 평균을 의미한다.

여기서 말하는 대기 된 프로세스(스레드)는 다음 두 가지 경우를 말한다.

- R(Running or Runnable) : 실행 큐(Run Queue)에서 CPU 실행 권한이 부여되기를 기다리는 프로세스(스레드)
- D(Uninterruptible Sleep) : 대기 큐(Wait Queue)에서 I/O 작업이 완료되기를 기다리는 프로세스(스레드)

Load Average는 시스템 전체의 부하 상황을 나타내기 위한 지표로 사용하는데 이는 CPU 사용률만으로는 알 수 없는 정보를 알려주기 때문이다.

Load Average는 CPU 코어당 1.00 이상이면 작업이 지연되고 있다는 뜻으로 부하가 있다고 판단할 수 있다. (안정적인 서버 운영을 위해서는 0.7 미만을 유지하는 것이 좋다.)

또한, 같은 작업을 수행하는 두 개의 서버가 CPU 사용률이 100%인 상황에서 Load Average가 서로 다르다면 낮은 쪽이 더 성능이 좋다는 것(부하가 낮다는 것)을 알 수 있다.

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
  - `us` : 사용자 모드에서 CPU가 소비된 시간의 비율
  - `sy` : 시스템 모드에서 CPU가 소비된 시간의 비율
  - `ni` : 사용자 모드에서 nice(낮은 우선 순위) 프로세스를 실행하는데 소요된 시간의 비율
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

### sar를 사용한 CPU 유형별 사용률 확인
```bash
# 1초 간격으로 CPU 유형별 사용률 출력
$ sar 1 -u
00시 15분 29초     CPU     %user     %nice   %system   %iowait    %steal     %idle
00시 15분 30초     all      0.06      0.00      0.06      0.00      0.00     99.88
00시 15분 31초     all      0.06      0.00      0.00      0.00      0.00     99.94
00시 15분 32초     all      0.00      0.00      0.06      0.00      0.00     99.94
00시 15분 33초     all      0.00      0.00      0.00      0.00      0.00    100.00
```
- `%user` : 사용자 모드에서 CPU가 소비된 시간의 비율
- `%nice` : 사용자 모드에서 nice(낮은 우선 순위) 프로세스를 실행하는데 소요된 시간의 비율
- `%system` : 시스템 모드에서 CPU가 소비된 시간의 비율
- `%iowait` : CPU가 디스크 I/O 대기를 위해 idle 상태로 소비한 시간의 비율
- `%steal` : Xen 등 OS 가상화를 사용하고 있을 경우, 다른 가상 CPU의 계산으로 대기한 시간의 비율
- `%idle` : CPU가 디스크 I/O 대기 등으로 대기되지 않고 idle 상태로 소비한 시간의 비율

<br>
