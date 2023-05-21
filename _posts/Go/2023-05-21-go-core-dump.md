---
title: "Go Core Dump"
excerpt: ""

categories:
  - Go

toc: true
toc_sticky: false

date: 2023-05-21
last_modified_at: 2023-05-21
---

## 서론

Go에서 프로세스가 비정상적으로 종료되는 주된 이유는 런타임 상황에서 panic이 발생했기 때문이다.

Go에서는 `recover()` 함수를 통해 panic에 대응할 수 있다. 

```go
func main() {
  defer func() {
    if err := recover(); err != nil {
      fmt.Printf("%v\n", err)
    }
  }()

  // panic 발생...
}
```

그러나 `recover()` 함수는 동일한 고루틴에서만 동작하기 때문에 모든 panic 상황에 대응하기에는 한계가 있다.

> 내가 사용하는 외부 모듈 내부에서 고루틴을 사용하고, 그 안에서 panic이 발생한다면?

따라서 panic이 발생했을 때 문제 상황을 파악하기 위해서는 core dump가 필요할 수 있다.

Go에서 core dump를 생성하고 분석하는 방법에 대해 알아보자. (리눅스 시스템 기준)

<br>

## Core Dump

Core dump는 프로세스가 비정상적으로 종료된 경우, 커널이 해당 프로세스와 관련된 메모리 상태를 기록해둔 파일을 의미한다.

디버거를 사용하여 core dump의 스택 트레이스, 변수, 메모리, 레지스터 등을 조사하고 문제의 원인을 분석할 수 있다.

<br>

## Core Dump 생성 방법

### 1. 환경 변수 설정

```bash
$ export GOTRACEBACK=crash
```

`GOTRACEBACK` 환경 변수는 Go 런타임에서 panic이 발생할 때 출력되는 로그의 양을 설정할 수 있다. ([https://pkg.go.dev/runtime](https://pkg.go.dev/runtime#:~:text=The%20GOTRACEBACK%20variable,debug/%23SetTraceback.))

`GOTRACEBACK=crash`로 설정할 경우, 유저+시스템 고루틴의 스택 트레이스를 전부 출력하고 해당 운영체제에 맞는 크래시 대응을 수행한다.

리눅스 시스템에서는 `SIGABRT` 시그널이 발생하고 이를 트리거로 커널은 core dump를 생성한다.

### 2. Core Dump 최대 크기 설정

```bash
$ ulimit -c unlimited
```

리눅스 시스템에서는 `ulimit` 명령을 통해 프로세스 리소스에 대한 제약을 설정할 수 있다.

`ulimit -c` 명령을 통해 core dump 최대 크기를 `unlimited`로 설정하면 크기가 큰 core dump도 문제 없이 생성할 수 있다.

### 3. Core Dump 생성 위치 설정

```bash
$ cat /proc/sys/kernel/core_pattern
/var/crash/core.%p
```

리눅스 시스템에서는 core dump의 생성 위치와 네이밍 규칙을 설정하는 `/proc/sys/kernel/core_pattern` 파일이 존재한다.

자세한 네이밍 규칙은 다음 [링크](https://man7.org/linux/man-pages/man5/core.5.html#:~:text=Naming%20of%20core%20dump%20files)를 참고하자.


<br>

## Core Dump 분석 방법

앞서 디버거를 사용하면 core dump의 스택 트레이스, 변수, 메모리, 레지스터 등을 조사하고 문제의 원인을 분석할 수 있다고 했다.

대표적인 디버거로는 GDB(GNU Debugger)가 있지만 Go에서는 [Delve](https://github.com/go-delve/delve)를 권장한다.

이유는 GDB가 Go의 고루틴, 채널, GC를 제대로 이해하지 못하여 디버깅이나 분석에 한계가 있기 때문이다.

Delve는 Go 전용 디버거로 제작되었기 때문에 좀 더 명확한 분석이 가능하다.

<br>

아래 예제 코드로 생성된 core dump를 GDB와 Delve를 사용하여 분석해보자.

```go
func main() {
  fmt.Print("Hello World\n")

  timer := time.NewTimer(time.Second * 5)
  go func() {
    panic("PANIC")
  }()

  <-timer.C
}
```

### GDB

```
$ gdb <executable> <core>

$ gdb main /var/crash/core.1450335
(gdb) bt
#0  runtime.raise () at /usr/local/go/src/runtime/sys_linux_amd64.s:154
#1  0x0000000000445665 in runtime.dieFromSignal (sig=6) at /usr/local/go/src/runtime/signal_unix.go:879
#2  0x0000000000445c06 in runtime.sigfwdgo (sig=6, info=<optimized out>, ctx=<optimized out>, ~r0=<optimized out>) at /usr/local/go/src/runtime/signal_unix.go:1092
#3  0x0000000000444327 in runtime.sigtrampgo (sig=0, info=0xffffffffffffffff, ctx=0x45fce1 <runtime.raise+33>) at /usr/local/go/src/runtime/signal_unix.go:432
#4  0x000000000045ffc6 in runtime.sigtramp () at /usr/local/go/src/runtime/sys_linux_amd64.s:354
#5  0x00000000004600c0 in ?? ()
#6  0x0000000000000007 in ?? ()
#7  0x0000000000000000 in ?? ()
```

### Delve

```
$ dlv core <executable> <core>

$ dlv core main /var/crash/core.1450335
(dlv) bt
 0  0x000000000045fce1 in runtime.raise
    at /usr/local/go/src/runtime/sys_linux_amd64.s:154
 1  0x0000000000445665 in runtime.dieFromSignal
    at /usr/local/go/src/runtime/signal_unix.go:879
 2  0x0000000000445c06 in runtime.sigfwdgo
    at /usr/local/go/src/runtime/signal_unix.go:1092
 3  0x0000000000444327 in runtime.sigtrampgo
    at /usr/local/go/src/runtime/signal_unix.go:432
 4  0x000000000045ffc6 in runtime.sigtramp
    at /usr/local/go/src/runtime/sys_linux_amd64.s:354
 5  0x00000000004600c0 in runtime.sigreturn
    at /usr/local/go/src/runtime/sys_linux_amd64.s:468
 6  0x00000000004313c9 in runtime.crash
    at /usr/local/go/src/runtime/signal_unix.go:971
 7  0x00000000004313c9 in runtime.fatalpanic
    at /usr/local/go/src/runtime/panic.go:1168
 8  0x0000000000430afb in runtime.gopanic
    at /usr/local/go/src/runtime/panic.go:987
 9  0x000000000048d1c7 in main.main.func1
    at ./main.go:13
10  0x000000000045e3c1 in runtime.goexit
    at /usr/local/go/src/runtime/asm_amd64.s:1598
(dlv) goroutines
  Goroutine 1 - User: ./main.go:16 main.main (0x48d185) [chan receive]
  Goroutine 2 - User: /usr/local/go/src/runtime/proc.go:382 runtime.gopark (0x433c56) [force gc (idle)]
  Goroutine 3 - User: /usr/local/go/src/runtime/proc.go:382 runtime.gopark (0x433c56) [GC sweep wait]
  Goroutine 4 - User: /usr/local/go/src/runtime/proc.go:382 runtime.gopark (0x433c56) [GC scavenge wait]
  Goroutine 5 - User: /usr/local/go/src/runtime/proc.go:382 runtime.gopark (0x433c56) [finalizer wait]
* Goroutine 6 - User: ./main.go:13 main.main.func1 (0x48d1c7) (thread 1450335)
[6 goroutines]
```

분석 결과를 보면 GDB와 Delve의 차이를 명확하게 확인할 수 있다.

<br>