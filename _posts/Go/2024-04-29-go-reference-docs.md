---
title: "Go 참고 문서 모음"
excerpt: ""

categories:
  - Go

toc: true
toc_sticky: false

date: 2024-04-29
last_modified_at: 2024-04-29
---

### Go 초보 가이드
- [Go Documentation](https://go.dev/doc/)
  - Go 공식 문서
- [Go Download and Install](https://go.dev/doc/install)
  - Go 설치 가이드
- [A Tour of Go](https://go.dev/tour/welcome/1)
  - Go 문법 입문 가이드
- [50 Shades of Go](https://golang50shad.es/)
  - 초보 Go 개발자를 위한 50가지 팁 모음 ([정정이 필요한 내용](https://blog.banksalad.com/tech/go-best-practice-in-banksalad/#:~:text=%ED%95%B4%EC%A3%BC%EA%B3%A0%20%EB%8B%AB%EA%B3%A0%20%EC%9E%88%EC%9D%8C-,50%20Shades%20of%20Go%3A%20Traps%2C%20Gotchas%2C%20and%20Common%20Mistakes%20for%20New%20Golang%20Devs%20%EA%B8%80%20%EA%B8%B0%EB%B0%98%EC%9C%BC%EB%A1%9C%20%EB%A6%AC%EB%8B%A4%EC%9D%B4%EB%A0%89%ED%8A%B8%20%EC%8B%A4%ED%8C%A8%20%EC%8B%9C%20%EC%97%90%EB%9F%AC%EC%B2%B4%ED%81%AC%EB%B3%B4%EB%8B%A4%20%EB%A8%BC%EC%A0%80%20non%20nil%20response%20body%EB%A5%BC%20%EB%8B%AB%EC%95%84%EC%A4%98%EC%95%BC%20%ED%95%98%EB%8A%94%EA%B1%B8%EB%A1%9C%20%EC%95%8C%20%EC%88%98%20%EC%9E%88%EB%8A%94%EB%8D%B0%201.21%20%EA%B8%B0%EC%A4%80%20%ED%8B%80%EB%A6%B0%20%EC%84%9C%EC%88%A0%EC%9E%84,-net/http%20%ED%8C%A8%ED%82%A4%EC%A7%80%EC%9D%98))
- [The Go Programming Language Specification](https://go.dev/ref/spec)
  - Go 언어 스펙 (최신 버전 기준)

### Go 코드 작성 가이드
- [Effective Go](https://go.dev/doc/effective_go)
  - Go 공식 문서에서 제공하는 Go 코드 작성 팁
- [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments)
  - Go 공식 문서에서 제공하는 Go 코드 리뷰 코멘트 모음
- [Google Go Style Guide](https://google.github.io/styleguide/go/)
  - Google에서 제공하는 Go 스타일 가이드
- [Uber Go Style Guide](https://github.com/uber-go/guide)
  - Uber에서 제공하는 Go 스타일 가이드
- [뱅크샐러드 Go 코딩 컨벤션](https://blog.banksalad.com/tech/go-best-practice-in-banksalad/)
  - 뱅크샐러드에서 제공하는 Go 코딩 컨벤션 (= 코딩 스타일 + 프랙티스 + 원칙)
    
### Go 프로젝트 구조
- [Go Project Layout](https://github.com/golang-standards/project-layout)
  - 표준에 가까운(de facto standard) Go 프로젝트 구조

### Go 디자인 패턴
- [Go Patterns](https://github.com/crazybber/awesome-patterns)
  - Go에서 사용되는 디자인 패턴 모음 (원본 레포가 활동을 안해 포크한 레포)
- [Go로 된 디자인 패턴](https://refactoring.guru/ko/design-patterns/go)
  - Go 코드로 작성된 GoF 디자인 패턴 모음 (각 디자인 패턴이 Go 언어에 적합한지 고민 필요)
- [Functional Options Pattern](https://golang.cafe/blog/golang-functional-options-pattern.html), [Functional Options for Friendly APIs](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)
  - Go에서 자주 사용하는 functional options 패턴 소개

### Go 동시성
- [Go Concurrency Patterns](https://go.dev/talks/2012/concurrency.slide#1), [Advanced  Go Concurrency Patterns](https://go.dev/talks/2013/advconc.slide#1)
  - 2012, 2013 Google I/O에서 발표한 Go 동시성 패턴
- [Go Concurrency Guide](https://github.com/luk4z7/go-concurrency-guide)
  - 예제 기반의 Go 동시성 가이드
- [Visualizing Concurrency in Go](https://divan.dev/posts/go_concurrency_visualize/)
  - 3D WebGL을 통해 Go 동시성 시각화

### Go 테스트
- [Advanced Testing with Go](https://www.youtube.com/watch?v=yszygk1cpEc&ab_channel=NewStoreTechTalks)
  - NewStore TechTalk에서 발표한 Go 테스트 관련 팁
- [Prefer Table Driven Tests](https://dave.cheney.net/2019/05/07/prefer-table-driven-tests)
  - 테이블 기반 Go 테스트 소개
- [Learn Go with Tests](https://quii.gitbook.io/learn-go-with-tests)
  - 테스트 코드 작성 예시를 보며 Go와 TDD(Test Driven Development) 배우기

### Go 성능 최적화
- [pprof](https://github.com/google/pprof)
  - Go 프로그램의 프로파일링 데이터를 시각화 및 분석하는 도구
- [automaxprocs](https://github.com/uber-go/automaxprocs)
  - Go 프로그램을 컨테이너 환경에서 실행할 때, GOMAXPROCS 값을 CPU 코어 개수가 아닌 컨테이너 CPU 제한에 맞게 설정하는 모듈
- [go-perfbook](https://github.com/dgryski/go-perfbook)
  - Go 성능 최적화에 대한 생각을 담은 글
- [Reducing Memory Allocations in Golang](https://chris124567.github.io/2021-06-21-go-performance/)
  - Go 메모리 할당을 줄이는 팁 ([string/byte slice 캐스팅 트릭은 1.20 버전부터 함수로 지원](https://www.sobyte.net/post/2022-09/string-byte-convertion/#the-new-way-of-go-120))
- [The One Billion Row Challenge in Go](https://benhoyt.com/writings/go-1brc/)
  - Go를 사용하여 10억 행을 처리하는 시간을 1분 45초에서 4초로 단축하는 9가지 최적화 방법을 소개

### Go 린트
- [golangci-lint](https://golangci-lint.run/)
  - Go 코드를 검사할 수 있는 린트 도구 (Github Action과 연동해서 많이 사용)

### Go 프로젝트 모음
- [Awesome Go](https://github.com/avelino/awesome-go)
  - Go 코드로 작성된 훌륭한 프레임워크, 라이브러리, 소프트웨어 모음

### 참고할 만한 자료 모음
- [The Go Blog](https://go.dev/blog/)
  - Go 공식 문서에서 제공하는 블로그 글
- [GopherCon Talks](https://github.com/golangkorea/gophercon-talks)
  - Golang Korea에서 주최하는 밋업, 세미나 및 컨퍼런스의 발표 자료들을 모아두는 저장소
- [Go Developer Community](https://gdc.hashnode.dev/?source=top_nav_blog_home)
  - Go Developer Community에서 제공하는 Go 콘텐츠 뉴스레터 모음
