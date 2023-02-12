---
title: "유지보수 가능한 Go 프로그램을 만드는 팁"
excerpt: ""

categories:
  - Go

toc: true
toc_sticky: false

date: 2022-09-16
last_modified_at: 2022-09-16
---

<br>

# 서론

[Practical Go: Real world advice for writing maintainable Go programs](https://dave.cheney.net/practical-go/presentations/qcon-china.html) 칼럼을 읽고 흥미로웠던 내용 위주로 정리해보았다.

반드시 따라야 할 필요는 없지만 참고할만하다고 생각한다.

<br>

# 변수명(Identifiers) 팁

## 변수명 길이 정하기

루프 및 분기에 사용하는 변수명은 한 문자, 매개 변수 및 반환 값에는 사용하는 변수명은 한 단어, 함수 및 패키지 수준의 정의에서 사용하는 변수명은 여러 단어로 짓는 것이 좋다.

```go
type Person struct {
  Name string
  Age  int
}

// AverageAge returns the average age of people.
func AverageAge(people []Person) int {
  if len(people) == 0 {
    return 0
  }

  var count, sum int
  for _, p := range people {
    sum += p.Age
    count += 1
  }

  return sum / count
}
```

특히 짧은 변수명은 변수의 선언과 사용 간의 거리가 가까운 상황일 때 유용하다.

## 패키지명의 중요성

패키지명은 호출자가 해당 패키지를 참조할 때 사용하는 이름의 한 부분이 된다. 패키지 내에서 변수명을 지을 때는 항상 이를 염두에 두어야 한다.

예를 들어 HTTP 서버 구조체를 구현한다고 가정해보자.

```go
package http

type HTTPServer struct {
  // ...
}
```

이때 HTTP 서버 구조체명을 `HTTPServer`로 짓는 것은 적절해 보인다.

```go
package main

// ...

s := &http.HTTPServer{}
```

하지만 해당 구조체가 `http` 패키지 내에 있으면 이는 부적절한 구조체명이 될 수 있다. `http` 패키지를 참조하는 호출자가 이를 사용할 경우, `http.HTTPServer` 가 되어 불필요한 단어 중복이 발생하기 때문이다.

```go
package http

type Server struct {
  // ...
}
```

```go
package main

// ...

s := &http.Server{}
```

따라서 `http` 패키지 내에 있는 HTTP 서버 구조체명을 지을 때는 `Server`로 지어서 호출자가 `http.Server`를 사용하는 것이 바람직하다.

또한, 패키지명이 좋은 변수명을 뺏지 않도록 지어야 한다.

대표적인 예시로 `context` 패키지가 있다.

```go
// Bad
func WriteLog(context context.Context, message string)

// Good
func WriteLog(ctx context.Context, message string)
```

패키지명을 `context`로 지었기 때문에 이를 변수명으로는 사용할 수 없다.

## 변수명에 변수 타입명이 들어가면 안된다.

예를 들어 key 타입이 `string`, value 타입이 `*User`인 map을 선언해보자.

```go
var usersMap map[string]*User
```

`usersMap`과 같은 변수명은 변수의 타입을 유추할 수 있으니 잘못 사용할 여지가 적다는 장점이 있지만, Go와 같은 정적 타입 언어에서는 어차피 컴파일러가 잘못 사용하는 상황을 막아줄 것이다.

접미사로 `Map`을 붙인다고 해당 코드의 명확성을 향상시키지 않으므로 제거하는 것이 좋다.

```go
var users map[string]*User
```

만약 `users`가 충분히 변수에 관해 설명하지 못한다면 `usersMap`도 마찬가지이다. 변수명을 바꿔야지 뒤에 타입명을 붙일 이유는 없다.

## 변수명과 파라미터 타입명이 중복되면 변수명을 더 줄여라.
```go
type Config struct {
  // ...
}

// Bad
func WriteConfig(w io.Writer, config *Config)

// Good
func WriteConfig(w io.Writer, conf *Config)
func WriteConfig(w io.Writer, c *Config)
```

## 일관된 스타일을 유지해라.

좋은 변수명은 예측 가능해야 한다. 어떤 의도로 변수명을 지었는지 이해할 수 있어야 하고 중간에 이유 없이 변수명이 변경되어서는 안 된다.

루프문의 `i, j` 혹은 카운터의 `n` 처럼 관용적인 줄임말을 사용하는 것이 좋다.

method receiver 같은 경우, 한 문자에서 두 문자 정도로 일관성 있게 사용하는 것이 좋다.

Go에서는 다양한 선언 스타일이 가능하다. 최대한 일관된 스타일을 유지하기 위해서 다음과 같은 규칙을 사용하면 좋다.

### 1. 변수를 선언하지만 초기화하지 않을 경우, `var` 사용해라.

```go
var players int    // 0

var things []Thing // an empty slice of Things

var thing Thing    // empty Thing struct
json.Unmarshall(reader, &thing)
```

### 2. 변수의 선언과 초기화를 동시에 할 경우, `:=` 사용해라.

```go
player := 0

things := make([]Thing, 0)

things := &Thing{}
```

## 기타 변수명 팁

- 메서드, 인터페이스, 패키지명은 한 단어를 사용하는 것이 좋다.
- 상수 변수명은 값이 사용되는 방식이 아니라 보유하고 있는 값을 설명해야 한다.

<br>

# 패키지(Package) 설계 팁

## 좋은 패키지명은 고유해야 한다.

만약 같은 이름이 있어야 하는 패키지가 두 개 이상 있다면 그 이름이 너무 일반적이게 지었거나 해당 패키지들을 합치거나 재설계할 필요가 있다.

특히 `base`, `common`, `util` 과 같은 패키지명은 피하는 것이 좋다. 다양한 기능을 포함하고 있는 유틸리티 패키지는 패키지명만 보고 해당 패키지가 제공하는 내용을 파악하기 힘들다.

## Guard Clauses를 사용해라.

Go 코드는 함수가 진행됨에 따라 성공 경로가 오른쪽이 아닌 아래로 이어지는 스타일로 구현한다.

다시 말해 Go 코드는 최대한 중첩 조건문을 사용하지 않고 Guard Clauses를 사용하여 오류 발생 시 초기에 반환하는 형태로 구현한다.

> Guard Clauses : 조건이 거짓이면 다음 단계를 진행하지 않는 방식으로 중첩된 조건문을 평탄화 시킬 수 있음
> 

```go
// Bad
func (b *Buffer) UnreadRune() error {
  if b.lastRead > opInvalid {
    if b.off >= int(b.lastRead) {
      b.off -= int(b.lastRead)
    }
    b.lastRead = opInvalid
    return nil
  }
  return errors.New("bytes.Buffer: UnreadRune: previous operation was not a successful ReadRune")
}

// Good
func (b *Buffer) UnreadRune() error {
  if b.lastRead <= opInvalid {
    return errors.New("bytes.Buffer: UnreadRune: previous operation was not a successful ReadRune")
  }
  if b.off >= int(b.lastRead) {
    b.off -= int(b.lastRead)
  }
  b.lastRead = opInvalid
  return nil
}
```

## Zero Value를 활용해라.

Go 코드에서는 값이 `nil`인 타입의 메서드도 호출할 수 있다.

이러한 특성을 이용하면 다음과 같은 구현도 가능하다.

```go
type Config struct {
  path string
}

func (c *Config) Path() string {
  if c == nil {
    return "/usr/home"
  }
  return c.path
}

func main() {
  var c1 *Config
  var c2 = &Config{
    path: "/export",
  }
  fmt.Println(c1.Path(), c2.Path())
}
```

## 패키지 레벨의 상태 변수를 피해라.

패키지 전역 변수를 사용하여 상태를 표현하면 문제가 발생할 수 있다.

해당 패키지를 사용하는 프로그램의 모든 함수가 전역 변수에 접근할 수 있으므로 의도치 않은 동작이 발생할 수 있고 코드의 결합도를 높인다.

<br>

# 프로젝트 구조(Project Structure) 팁

## 파일 정렬 팁

1. 각 패키지를 하나의 `.go` 파일로 시작한다. 이때 패키지 이름, 폴더 이름, 파일 이름은 모두 같아야 한다.
2. 패키지가 커짐에 따라 책임을 나누고 파일을 분리한다. (예를 들어 `message.go`에는 Request/Response 구조체, `client.go`에는 Client 구조체, `server.go`에는 Server 구조체를 포함하게 나눈다.)
3. 만약 파일들이 비슷한 모듈(패키지)을 사용하고 있으면 하나로 합치는 방법을 고려한다.
4. 각 파일은 각자 다른 영역에 대해 책임을 져야 한다. (예를 들어 `message.go`는 네트워크에서 HTTP Request/Response 메시지를 마샬링(Marshaling)하는 역할을 수행하고 `http.go`는 저수준 네트워크 처리를 수행, `client.go` 및 `server.go`는 요청 구성 및 라우팅 등의 HTTP 비즈니스 로직을 담당하는 역할을 수행한다.)

Go 컴파일러는 패키지 내에 있는 각 함수를 병렬로 컴파일한다. 

따라서 패키지 내에 코드가 한 파일에 몰려있든 여러 파일로 쪼개져 있든 컴파일 시간에 영향을 주지 않는다.

## `internal` 패키지를 사용해라.

`internal` 패키지를 사용해서 외부에 노출된 Public API를 최소화하는 것이 좋다.

<br>

# API 설계 팁

## 동일한 타입을 가지는 매개 변수들을 주의해라.

동일한 타입의 매개 변수를 사용하는 API는 실수하기 쉽다.

아래와 같은 두 가지 함수 시그니처가 있다고 가정해보자.

```go
func Max(a, b int) int
func CopyFile(to, from string) error
```

`Max` 함수는 매개 변수의 순서가 바뀌어도 동일한 결과를 제공한다.

```go
Max(8, 10) // 10
Max(10, 8) // 10
```

하지만 `CopyFile` 함수는 매개 변수의 순서에 따라 다른 결과가 발생한다.

```go
CopyFile("/tmp/backup", "presentation.md")
CopyFile("presentation.md", "/tmp/backup")
```

함수의 설명을 보지 않고는 어떤 파일이 어떤 파일을 복사하는지 파악하기 어렵다.

```go
type Source string

func (src Source) CopyTo(dest string) error {
  return CopyFile(dest, string(src))
}

func main() {
  var from Source = "presentation.md"
  from.CopyTo("/tmp/backup")
}
```

이를 해결하기 위한 한 가지 방법으로 헬퍼 타입을 사용하여 호출의 책임을 전가하는 방법이 있다.

이를 통해 해당 API를 잘못 사용할 가능성을 줄일 수 있다.

## 매개 변수가 `nil`을 허용하는 구현을 피해라.

`net/http` 패키지에 있는 `ListenAndServe` 함수는 두 가지 매개 변수를 가진다.

```go
package http

// ListenAndServe listens on the TCP network address addr and then calls
// Serve with handler to handle requests on incoming connections.
// Accepted connections are configured to enable TCP keep-alives.
//
// The handler is typically nil, in which case the DefaultServeMux is used.
//
// ListenAndServe always returns a non-nil error.
func ListenAndServe(addr string, handler Handler) error {
```

이때 두 번째 매개 변수인 `handler`는 인자로 `nil`이 들어오는 상황을 허용한다.

만약 인자로 `nil`이 들어올 경우, 암시적으로 `http.DefaultServeMux`를 `handler`로 사용한다.

```go
func ListenAndServe(addr string, handler Handler) error {
  l, err := net.Listen("tcp", addr)
  if err != nil {
    return err
  }
  defer l.Close()
  return Serve(l, handler)
}
```

이는 사용자의 편의성을 높이려는 방법이지만 내부에서 호출하는 함수에서도 계속 `nil`을 허용해야 하는 상황을 만들 수 있다. 

```go
http.ListenAndServe("0.0.0.0:8080", nil)
http.ListenAndServe("0.0.0.0:8080", http.DefaultServeMux)
```

따라서 `nil`을 허용하여 편의성을 높이기보단 `http.DefaultServeMux`을 사용하는 방법이 코드를 더 명확하게 만든다.

 
## Slices 매개 변수 대신 Variadic Function을 사용해라.

다음과 같이 여러 필드 중 0보다 큰 값이 하나라도 존재하는지 확인하는 조건문이 있다고 가정해보자. 

```go
if svc.MaxConnections > 0 || svc.MaxPendingRequests > 0 || svc.MaxRequests > 0 || svc.MaxRetries > 0 {
  // apply the non zero parameters
}
```

해당 조건문을 다음과 같이 Slices(`[]T`) 타입을 매개 변수로 사용하는 함수로 만들 수 있다.

```go
func anyPositive(values []int) bool {
  for _, v := range values {
    if v > 0 {
      return true
    }
  }
  return false
}
```

```go
values := []int{svc.MaxConnections, svc.MaxPendingRequests, svc.MaxRequests, svc.MaxRetries}

if anyPositive(values) {
  // ...
}
```

위 방식도 문제는 없지만 Variadic Function(`…T`) 타입을 매개 변수로 사용하는 함수를 만들면 호출자는 훨씬 가독성이 좋은 코드를 구현할 수 있다.

```go
func anyPositive(values ...int) bool {
  for _, v := range values {
    if v > 0 {
      return true
    }
  }
  return false
}
```

```go
if anyPositive(svc.MaxConnections, svc.MaxPendingRequests, svc.MaxRequests, svc.MaxRetries) {
  // ...
}
```

## 함수가 필요한 동작을 정의하도록 해라.

다음과 같이 `Document` 구조체를 디스크에 저장하는 함수가 있다고 가정해보자.

```go
func Save(f *os.File, doc *Document) error
```

`*os.File`에는 다양한 메서드가 있을 수 있지만, 단일 책임 원칙(Single Responsibility Principle)에 따라 `Save` 함수는 필요한 쓰기 동작만 수행하도록 `io.Writer` 인터페이스를 사용한다.

```go
func Save(w io.Writer, doc *Document) error
```

<br>

# 동시성(Concurrency) 팁

## 초기화하지 않은 채널(Channel) 사용을 피해라.

초기화하지 않은 채널에 송신/수신을 하게 되면 교착 상태(Deadlock)에 빠질 수 있다.

```go
func main() {
  var c chan string
  c <- "let's get started" // Deadlock
}

func main() {
  var c chan string
  fmt.Println(<-c) // Deadlock
}
```

## 닫힌 채널에 데이터를 송신하면 panic 상태에 빠진다.

채널을 배웠으면 닫힌 채널에 데이터를 송신하면 안 된다는 것을 알 것이다.

```go
func main() {
  var c = make(chan int, 100)
  for i := 0; i < 10; i++ {
    go func() {
      for j := 0; j < 10; j++ {
        c <- j
      }
      close(c)
    }()
  }
  for i := range c {
    fmt.Println(i)
  }
}
```

이를 해결하기 위해 다음과 같은 조건문을 추가할 수 있지만, 이 방법 역시 문제가 있다.

```go
if !isClosed(c) {
  // c isn't closed, send the value
  c <- v
}
```

동시성 프로그래밍에서는 조건문을 통과한 순간 채널이 닫히는 상황이 충분히 발생할 수 있기 때문이다.

```go
func main() {
  var c = make(chan int, 100)
  var mu sync.Mutex
  var closed bool
  for i := 0; i < 10; i++ {
    go func() {
      for j := 0; j < 10; j++ {
        c <- j
      }
      mu.Lock()
      if !closed {
        close(c)
        closed = true
      }
      mu.Unlock()
    }()
  }
  for i := range c {
    fmt.Println(i)
  }
}
```

따라서 `sync.Mutex`와 같은 동기화 방법이 반드시 필요하다.

## 채널이 닫히고 버퍼에서 모든 값이 배출되면 0 값을 반환한다.

```go
func main() {
  c := make(chan int, 3)
  c <- 1
  c <- 2
  c <- 3
  close(c)
  for i := 0; i < 4; i++ {
    fmt.Printf("%d ", <-c) // prints 1 2 3 0
  }
}
```

`range` 문을 통해 더 깔끔하게 구현할 수 있다.

```go
for v := range c {
  fmt.Printf("%d ", v) // prints 1 2 3
}
```

## 고루틴(Goroutine)을 남발하지 마라.

`main.main` 함수가 반환되면 다른 고루틴에서 작업이 끝나지 않았어도 Go 프로그램은 종료된다.

그러다 보니 고루틴이 작업을 다 수행할 때까지 메인 스레드가 기다려야 하는 상황이 발생한다.

```go
func main() {
  http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "Hello, GopherCon SG")
  })
  go func() {
    if err := http.ListenAndServe(":8080", nil); err != nil {
      log.Fatal(err)
    }
  }()

  for {
  }
}
```

메인 스레드에서 무한 루프를 돌면서 다른 작업을 기다리는 방식은 CPU 자원을 낭비한다.

CPU 자원을 절약하기 위해 `runtime.GoSched()` 함수를 사용하여 스케줄러가 다른 고루틴을 실행하도록할 수 있지만 이는 근본적인 해결 방법이 아니다.

```go
func main() {
  http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "Hello, GopherCon SG")
  })
  if err := http.ListenAndServe(":8080", nil); err != nil {
    log.Fatal(err)
  }
}
```

이런 상황에서는 고루틴에게 작업을 위임하지 않고 직접 수행하는 것이 더 옳은 선택일 수 있다.

## 함수를 비동기적으로 실행하는 여부는 호출자에게 맡겨라.

다음과 같이 디렉토리 리스트를 얻기 위한 함수가 있다고 가정해보자.

```go
// slices를 반환하는 방법
func ListDirectory(dir string) ([]string, error)
```

슬라이스를 반환하는 방법은 간단하다고 명확하다는 장점이 있지만, 디렉토리의 크기가 크면 시간이 오래 걸리고 슬라이스를 생성하기 위해 많은 메모리를 할당해야 하는 단점이 있다.

```go
// channel을 반환하는 방법
func ListDirectory(dir string) chan string
```

채널을 반환하는 방법은 내부에서 고루틴을 사용하여 비동기적을 작업을 처리할 수 있는 장점이 있지만, 작업이 완료되어 채널이 종료되는지 오류가 발생하여 채널이 종료되는지 파악하기 어렵고 중간에 원하는 디텍터리를 찾아도 채널이 닫힐 때까지 채널을 읽어야 하는 단점이 있다.

따라서 함수를 구현할 때 비동기 실행을 강제하는 것보다 해당 호출자가 선택할 수 있도록 구현하는 것이 좋다. 만약 비동기 실행을 강제한다면 해당 함수의 고루틴을 명시적으로 중지하는 방법도 제공하는 것이 좋다.

```go
// callback 함수를 사용하는 방법
func ListDirectory(dir string, fn func(string))
```

앞선 문제들을 해결하기 위한 방법으로 콜백 함수를 사용하는 방법이 있다.

실제 `filepath.WalkDir` 함수도 콜백 함수를 사용한다.

## 언제 멈출지 모르는 고루틴을 시작하지 마라.

Go 프로그램은 종료될 때 현재 실행 중인 고루틴들을 멈추게 하는 방법이 있어야 한다.

또한, 고루틴에서 오류가 발생한다면 해당 오류를 전달받고 프로세스를 깨끗하게 종료할 수 있어야 한다.