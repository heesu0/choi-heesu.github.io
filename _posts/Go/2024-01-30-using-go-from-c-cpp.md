---
title: "C/C++에서 Go를 사용하는 방법"
excerpt: ""

categories:
  - Go
  - Cpp

toc: true
toc_sticky: false

date: 2024-01-30
last_modified_at: 2024-01-30
---

## 서론

C 라이브러리에 의존성이 있거나 성능을 높이기 위해 Go 코드에서 C 코드를 사용하는 경우는 꽤 많다.

그러나 C/C++ 코드에서 Go 코드를 사용하는 경우는 매우 드물다.

> 적어도 난 그런 프로젝트를 본 적이 없다.

만약 모종의 이유로 C/C++ 코드에서 Go 코드를 사용해야 하는 상황이 생겼다면 어떻게 하는지 알아보자.

<br>

## Go, C/C++ 코드 간 사용 방법

Go와 C/C++ 코드 간의 사용 방법은 두 가지가 있다.

### Cgo

첫번째는 [cgo](https://pkg.go.dev/cmd/cgo)를 사용하는 방법이다.

Cgo는 Go와 C 코드 간의 FFI(Foreign Function Interface)를 지원하는 도구이다. Cgo를 사용하면 Go 코드에서 C 함수를 호출하거나 Go 함수를 C 라이브러리로 내보낼(export) 수 있다.

Cgo는 C++ 기능(예: 클래스, 레퍼런스, 템플릿 등)을 사용할 수 없으며 C 인터페이스만 지원한다. 따라서 C++ 코드에서 Go 함수를 호출하려면, C 라이브러리를 사용하는 것처럼 `extern "C"` 키워드를 사용하여 name mangling을 비활성화해야 한다.

> Go 코드에서 C++ 코드를 사용하기 위해 Go 기본 컴파일러(gc) 대신 gccgo, gollvm을 사용하여 C++ 함수를 링킹하는 방법이 있지만, 이는 Go에서 권장하는 방법이 아니다.

### SWIG (Simple Wrapper and Interface Generator)

두번째는 [SWIG](https://swig.org/)를 사용하는 방법이다.

SWIG는 다양한 프로그래밍 언어에서 C/C++ 코드를 사용할 수 있도록 인터페이스를 지원하는 도구이다. SWIG를 사용하면 Go 코드에서 C++ 함수를 호출할 수 있다.

하지만 SWIG는 C/C++ 코드에서 Go 함수를 호출하는 방법을 지원하지 않는다.

결국 C/C++ 코드에서 Go 함수를 호출하려면 cgo를 사용해야 한다.

<br>

## Cgo를 사용하여 C/C++ 코드에서 Go 함수를 호출하는 방법

Cgo는 Go 함수를 C 라이브러리로 내보내는(빌드하는) 기능을 제공한다. C/C++ 코드는 해당 라이브러리를 링킹하여 Go 함수를 호출할 수 있다.

### Go 함수 내보내기

먼저 내보내고 싶은 Go 함수 위에 `//export` 주석을 추가한다.

```go
import "C"

//export PrintGoString
func PrintGoString(str string) {
  fmt.Println(str)
}

//export PrintCString
func PrintCString(cstr *C.char) {
  str := C.GoString(cstr)
  fmt.Println(str)
}
```

Go 함수를 내보내면 cgo를 통해 C 함수로 변환되는데, 이 과정에는 다양한 제약 사항이 존재한다.

이에 대해서는 [아래](#cgo-제약-사항)에서 자세히 다루겠다.

### Go 커맨드로 C 라이브러리 빌드

내보낼 Go 함수들을 정했으면 Go 커맨드를 통해 해당 파일을 C 라이브러리로 빌드한다.

```bash
$ go build -o libgo.a -buildmode=c-archive main.go
```

> Go 커맨드는 `-buildmode` 옵션을 통해 Go 코드를 C 정적/공유 라이브러리로 빌드하는 방법을 제공한다.

```
-buildmode=c-archive
    Build the listed main package, plus all packages it imports,
    into a C archive file. The only callable symbols will be those
    functions exported using a cgo //export comment. Requires
    exactly one main package to be listed.
 
-buildmode=c-shared
    Build the listed main package, plus all packages it imports,
    into a C shared library. The only callable symbols will
    be those functions exported using a cgo //export comment.
    Requires exactly one main package to be listed.
```

### C/C++ 코드에서 C 라이브러리 링킹

빌드가 완료되면 컴파일러는 C 라이브러리 파일과 헤더 파일을 생성한다.

```c
extern void PrintGoString(GoString str);
extern void PrintCString(char* cstr);
```

C/C++ 코드에서는 이를 사용하여 Go 함수들을 호출할 수 있다.

```c++
#include <iostream>
#include <string>
#include <cstring>
#include "libmain.h" // main.go를 빌드해서 생성한 헤더 파일

int main() {
  GoString gstr{};
  gstr.p = "Hello World!";
  gstr.n = strlen(gstr.p);
  PrintGoString(gstr);

  std::string str = "Hello World!";
  PrintCString(const_cast<char*>(str.c_str()));

  return 0;
}
```

```bash
$ g++ -O main.cpp libgo.a -lpthread -o main && ./main
```

<br>

## Cgo 제약 사항

Go 함수를 C 함수로 변환하고 사용하는 과정에서는 다양한 제약 사항이 존재한다

### 타입 호환 규칙

먼저 Go 함수의 인자와 반환 타입이 C 타입과 호환되어야 한다.

- Go의 기본 데이터 타입(rune, string, byte, int 등)과 동적 타입(slice, interface, channel, map, func)은 C 타입으로 변환할 수 있다.
- Go struct, array 타입은 C 타입으로 변환할 수 없다. (대신 C struct, pointer를 사용해야 한다.)
- Go의 `unsafe.Pointer`를 C의 `void*`로 변환할 수 있다.
- C의 기본 데이터 타입(char, int, float 등)을 Go 함수의 인자와 반환 타입으로 사용할 수 있다. (`C.char`, `C.int`, `C.float`)
- C의 구조체 타입(struct, union, enum)을 Go 함수의 인자와 반환 타입으로 사용할 수 있다. (`C.struct_***`, `C.union_***`, `C.enum_***`)

### 포인터 전달 규칙

C/C++에서는 메모리를 직접 관리하는 반면 Go에서는 gc(garbage collector)가 메모리를 관리하기 때문에 C/C++ 코드와 Go 코드 간의 포인터 전달 규칙이 존재한다.

우선 어디서 할당한 메모리에 대한 포인터인지 구분이 필요하다.

- Go 포인터 : Go에서 할당한 메모리에 대한 포인터
- C 포인터 : C에서 할당한 메모리에 대한 포인터 (Go에서 `C.malloc`으로 할당한 포인터도 포함된다.)

C 함수에 Go 포인터를 전달할 경우, C 코드는 함수 호출이 끝난 이후에는 Go 포인터를 가지고 있으면 안된다. 그 이유는 함수 호출 이후에는 Go 포인터가 가리키는 메모리가 gc에 의해 해제되거나 이동될 수 있기 때문이다.

따라서 Go 포인터를 저장하고 싶다면 C 메모리에 할당하고 복사하거나 [`runtime.Pinner`](https://pkg.go.dev/runtime#Pinner)를 사용하여 Go 메모리를 고정해야 한다.

> Go 1.7 부터는 [`cgo.Handle`](https://pkg.go.dev/runtime/cgo)을 통해 안전하게 포인터를 주고 받는 방법을 제공한다.

### C 함수 포인터 호출 불가

Go에서는 C 함수 포인터 호출은 지원하지 않는다. 물론 [이를 우회하는 방법](#c-함수-포인터-사용-방법)이 존재한다.

<br>

## Cgo 성능 문제

Cgo를 사용하면 빠질 수 없는 것이 성능 문제이다.

C/C++과 Go는 런타임 환경과 스택 관리 방식이 서로 다르기 때문에 호출 오버헤드가 크다.

> [언어별 C FFI 오버헤드](https://github.com/dyu/ffi-overhead)를 비교해보면 Go의 호출 오버헤드가 다른 언어에 비해 상당히 큰 편이다.

게다가 C 코드에서 Go 함수를 호출하는 오버헤드는 Go 코드에서 C 함수를 호출하는 오버헤드보다 훨씬 크다.

[GopherCon 2018 - Adventures in Cgo Performance](https://www.youtube.com/watch?v=71ggzBeHdmA&ab_channel=GopherAcademy) 자료의 예시를 보면 상대적인 차이가 얼마나 큰지 확인할 수 있다.

- Go 함수 호출 오버헤드 : `1.83ns/op`
- Go 코드에서 C 함수 호출 오버헤드 : `171ns/op`
- C 코드에서 Go 함수 호출 오버헤드 : `1-2ms/op`

이러한 성능 차이의 원인은 Go 런타임의 구현 코드를 통해 유추할 수 있다.

[Go에서 C를 호출하는 경우](https://github.com/golang/go/blob/master/src/runtime/cgocall.go#L117)와 달리 [C에서 Go를 호출하는 경우](https://github.com/golang/go/blob/master/src/runtime/cgocall.go#L278)에는 Go 런타임이 OS 스레드 락을 잡는다. 이는 Go 스케줄러가 Go 함수 실행 중에 고루틴을 다른 OS 스레드로 이동시키는 것을 방지하기 위한 조치이다. 이렇게 함으로써 Go 런타임을 안정적으로 관리할 수 있지만 불가피한 성능 저하가 발생한다.

현실적으로 성능 저하를 줄일 수 있는 방법은 함수 호출 횟수를 줄이는 방법뿐이다. C/C++ 코드에서 Go 함수를 호출해야 하는 경우, 함수 호출을 최대한 적게 하고 호출할 때마다 최대한 많은 작업을 수행하도록 구현하는 것이 좋다.

<br>

## Cgo 사용 팁

### Go, C 타입 변환 함수

Go 타입과 C 타입 사이의 변환을 지원하는 몇 가지 특수 함수들이 존재한다. 해당 함수들은 데이터의 복사본을 만들어서 타입을 변환한다.

```go
// Go string to C string
// The C string is allocated in the C heap using malloc.
// It is the caller's responsibility to arrange for it to be
// freed, such as by calling C.free (be sure to include stdlib.h
// if C.free is needed).
func C.CString(string) *C.char

// Go []byte slice to C array
// The C array is allocated in the C heap using malloc.
// It is the caller's responsibility to arrange for it to be
// freed, such as by calling C.free (be sure to include stdlib.h
// if C.free is needed).
func C.CBytes([]byte) unsafe.Pointer

// C string to Go string
func C.GoString(*C.char) string

// C data with explicit length to Go string
func C.GoStringN(*C.char, C.int) string

// C data with explicit length to Go []byte
func C.GoBytes(unsafe.Pointer, C.int) []byte
```

`C.CString`, `C.CBytes` 함수는 내부적으로 `C.malloc` 함수를 사용하여 메모리를 할당하므로, 사용 후에는 직접 `C.free` 함수를 호출하여 메모리를 해제해야 한다. (`C.free`를 사용하려면 `stdlib.h` 라이브러리를 포함해야 한다.)

```go
/*
#include <stdio.h>
#include <stdlib.h>

static void myprint(char* s) {
  printf("%s\n", s);
}
*/
import "C"
import "unsafe"

func main() {
  cs := C.CString("Hello from stdio")
  C.myprint(cs)
  C.free(unsafe.Pointer(cs))
}
```


### C 함수 포인터 사용 방법

Go에서 C 함수 포인터 호출을 지원하지 않으므로, 이를 우회하기 위해서는 C 함수 포인터를 호출(콜백)하는 C wrapper 함수를 작성해야 한다.

```go
/*
#include <stdio.h>
#include <stdlib.h>
 
typedef void (*callback)(char*);
 
static inline void WrapperCallback(char* str, callback cb) {
    return cb(str);
}
*/
import "C"
 
//export MyFunction
func MyFunction(cb C.callback) {
  str := C.CString("callback")
  defer C.free(unsafe.Pointer(str))
 
  C.WrapperCallback(str, cb)
}
```

### C 배열을 Go 슬라이스로 변환하는 방법

앞서 설명한 특수 함수인 `C.GoBytes` 함수를 사용하면 C 배열의 내용을 Go 슬라이스로 복사할 수 있다.

```go
import "C"
 
// C.GoBytes는 C array를 Go slice로 복사함
//
//export ProcessData1
func ProcessData1(data *C.uint8_t, size C.int) {
  slice := C.GoBytes(unsafe.Pointer(data), size)
 
  for i := range slice {
    slice[i] += 1
  }
}
```

만약 C 배열을 복사하지 않고 Go 슬라이스로 변환하고 싶으면 다음과 같은 방법을 사용한다.

```go
//export ProcessData2
func ProcessData2(data *C.uint8_t, size C.int) {
  slice := (*[1 << 30]C.uint8_t)(unsafe.Pointer(data))[:size:size]
 
  for i := range slice {
    slice[i] += 1
  }
}
 
// Go 1.17 이후부터 unsafe.Slice를 통해 C 배열을 복사하지 않고 Go 슬라이스로 변환하여 사용 가능
//
//export ProcessData3
func ProcessData3(data *C.uint8_t, size C.int) {
  slice := unsafe.Slice((*byte)(unsafe.Pointer(data)), size)
 
  for i := range slice {
    slice[i] += 1
  }
}
```

```c++
#include <iostream>
#include <cstdint>
#include "libmain.h"
 
int main() {
  int size = 10;
  auto* data = new uint8_t[size]{0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
     
  ProcessData1(data, 10); // 0 1 2 3 4 5 6 7 8 9
  // ProcessData2(data, 10); // 1 2 3 4 5 6 7 8 9 10
  // ProcessData3(data, 10); // 1 2 3 4 5 6 7 8 9 10
 
  for (int i = 0; i < size; ++i) {
      std::cout << static_cast<unsigned int>(data[i]) << " ";
  }
  std::cout << "\n";
 
  delete[] data;
}
```

<br>

## 참조

- [https://go.dev/wiki/cgo](https://go.dev/wiki/cgo)
- [https://pkg.go.dev/cmd/cgo](https://pkg.go.dev/cmd/cgo)
- [https://pkg.go.dev/cmd/go#hdr-Build_modes](https://pkg.go.dev/cmd/go#hdr-Build_modes)
- [https://pkg.go.dev/cmd/go#hdr-Calling_between_Go_and_C](https://pkg.go.dev/cmd/go#hdr-Calling_between_Go_and_C)
- [https://go.dev/doc/faq#Do_Go_programs_link_with_Cpp_programs](https://go.dev/doc/faq#Do_Go_programs_link_with_Cpp_programs)
- [https://dave.cheney.net/2016/01/18/cgo-is-not-go](https://dave.cheney.net/2016/01/18/cgo-is-not-go)
- [https://www.cockroachlabs.com/blog/the-cost-and-complexity-of-cgo/](https://www.cockroachlabs.com/blog/the-cost-and-complexity-of-cgo/)