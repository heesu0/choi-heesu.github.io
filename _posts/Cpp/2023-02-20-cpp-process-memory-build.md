---
title: "C++ 정리 - Process, Memory, Build"
excerpt: ""

categories:
  - Cpp

toc: true
toc_sticky: false

date: 2023-02-20
last_modified_at: 2023-02-20
---

# Process, Memory

## 프로세스의 메모리 영역

<img width="400" alt="image" src="https://github.com/heesu0/choi-heesu.github.io/assets/34677157/3b0b17b0-f85b-44d4-943e-e912766f99f5">

- 정적 할당 영역 : 텍스트(코드) 영역, 데이터 영역 (메모리 할당이 고정)
    - 텍스트(코드) 영역 : 프로그램의 코드가 저장된 영역
    - 데이터 영역(+ BSS): 프로그램이 사용하려고 정의한 변수와 데이터가 있는 영역
- 동적 할당 영역 : 힙 영역, 스택 영역 (실행 중 메모리를 할당하고 반납하는 영역)
    - 힙 영역 : 자율적으로 메모리 크기를 할당, 반납할 수 있는 영역
    - 스택 영역 : 지역 변수가 할당되는 영역으로 함수가 호출되면 할당되었다가 함수 종료 시 반납

## 스택(Stack) 영역
- 스택 메모리는 스택 프레임(함수) 단위로 쌓인다.
- 스택 프레임 안에는 매개 변수와 지역 변수, 함수 호출이 끝나면 되돌아갈 주소 정보 등이 들어 있다.
- 스택 프레임 안의 변수들에 접근할 때 top에서 얼마나 떨어져 있는지로 접근할 수 있다.
- 스택 메모리 영역을 넘어서 데이터를 저장하면 스택 오버플로우가 발생한다.
- 스택의 top 영역에서만 메모리 할당 및 해제가 발생하기 때문에 속도가 매우 빠르다.
- `std::array`는 스택 영역에 데이터를 할당한다.

## 힙(Heap) 영역
- 힙 영역을 사용하는 이유
    1. Life Cycle : 함수가 종료되면 스택 메모리에 있는 데이터는 삭제되지만 힙 영역에 있는 데이터는 유지된다.
    2. Large Size : 스택 메모리 크기는 한정되어 있기 때문에 큰 데이터는 힙 영역에서 관리하는게 효율적이다.
    3. Dynamic Allocation (Runtime) : 컴파일 타임이 아닌 런타임에 데이터 크기를 정하고 할당할 수 있다.
- 힙 영역의 메모리 할당 및 해제는 스택 영역의 메모리 할당 및 해제보다 속도가 더 느리다.
- `std::vector`는 힙 영역에 데이터를 할당한다.

## 힙 영역에 메모리 할당
- C 방식에서는 `malloc`, `free`를 사용한다.
- C 방식으로 객체 생성 시 생성자와 소멸자가 안불린다. (C++ 에서는 사용하지 않는다.)
- C++ 방식에서는 `new`, `delete`를 사용한다.
- Safer C++ 방식에서는 스마트 포인터와 STL(Standard Template Library)을 사용한다.

```cpp
// C 방식에서는 malloc과 free를 사용한다.
int* ip = (int*)malloc(sizeof(int));
*ip = 100;
free(ip);

int* iap = (int*)malloc(sizeof(int) * 5);
iap[0] = 100;
free(iap);

// C 방식에서는 객체 생성 시 생성자와 소멸자가 호출되지 않는다.
Cat* catp = (Cat*)malloc(sizeof(Cat));
free(catp);

// C++ 방식에서는 new, delete 사용한다.
int* ip = new int;
*ip = 100;
delete ip;

int* iap = new int[5];
ip[0] = 100;
delete[] iap;

Cat* catp = new Cat;
delete catp;

Cat* catap = new Cat[5];
delete[] catap;

// C++ 방식에서는 제대로 할당 해제를 안하면 메모리릭이 발생할 수 있다.
// 이러한 위험을 방지하기 위해서 Safer C++ 방식의 스마트 포인터를 사용한다.
std::unique_ptr<int> ip = std::make_unique<int>();
*ip = 100;

std::unique_ptr<Cat> catp = std::make_unique<Cat>();

// std::vector와 같은 STL을 사용하면 알아서 메모리 관리를 해준다.
std::vector<int> iap(5);
iap[0] = 100;

std::vector<Cat> catap(5);
```

## 리터럴(Literal)
- 소스 코드 상에서 고정된 값 (메모리 상에서 데이터 영역(rodata)에 포함한다.)
- 컴파일러가 컴파일 타임에 정의할 수 있는 타입이다.
- `"hello"` 처럼 큰 따옴표로 묶인 것들을 문자열 리터럴이라 부른다.

```cpp
// 리터럴은 오로지 읽기만 가능하다.

char str[] = "hello"; // "hello" 리터럴을 배열에 복사한다. (스택 영역)
char* pstr = "hello"; // "hello" 리터럴을 가리킨다. (데이터 영역)

str[1] = 'a'; // 가능
pstr[1] = 'a'; // 불가능

// const를 붙이는 것이 좋다.
const char* pstr = "hello";
```

<br>

# Build

## C++이 빠른 이유
- C++은 소스 코드를 CPU가 이해할 수 있는 기계어로 컴파일하기 때문에 속도가 빠르다.
- Java는 소스 코드를 가상 머신(VM, Virtual Machine)이 이해할 수 있는 중간(Intermediate) 언어인 바이트코드(bytecode)로 컴파일한 후 VM이 한 줄씩 읽어주는 방식이다.
- Python은 소스 코드를 인터프리터가 한줄씩 번역하며 실행하는 방식이다.
- C++은 중간 과정이 적은 만큼 빠르지만 VM이나 인터프리터 대신 직접 메모리를 관리하기 때문에 컴파일러 최적화가 안 되는 비효율적인 코드를 짜면 더 느려질 수 있다.

## C++ 컴파일러 종류 및 옵션
- gcc(g++), clang + llvm, msvc 등
- 유명한 gcc 옵션들
    - `-o` :  컴파일된 파일 이름 정하기
    - `-W` : 컴파일러 경고 메시지 활성화 (`-Wall`, `-Werror` 등)
    - `-O` : 최적화 레벨 설정 (`-O0`, `-O2` 등)
    - `-std` : C++ 버전 설정 (`-std=c++11`, `-std=c++17` 등)
    - `-g` : 디버그 옵션
    - `-I`  : 소스를 컴파일할 때 참조하는 헤더 파일들이 들어있는 폴더 경로를 명시하는 옵션 (`-I"디렉토리 경로"`)
    - `-i` : 소스를 컴파일할 때 참조하는 헤더 파일의 경로를 명시하는 옵션 (`-i"파일 경로"`)
    - `-L` : 같이 링크할 라이브러리가 들어있는 폴더 경로를 명시하는 옵션, 컴파일러가 탐색할 라이브러리의 경로를 알려준다. (`-L"디렉토리 경로"`)
    - `-l` : 같이 링크할 라이브러리 파일의 이름을 명시하는 옵션, 앞에 lib와 확장자(`.so`, `.a`)는 생략한다. (`-l"파일 경로"`)

## 빌드(컴파일) 과정
- 전처리 과정 -컴파일 과정 - 어셈블리 과정 - 링킹 과정
- [관련 포스트](/language/compile)

## 전처리 과정
- 매크로 치환, 헤더 파일 붙여 넣기
- 헤더 파일의 중복을 막기 위해 헤더 가드를 사용한다.
  - `#pragma once`를 사용해서 간결하게 헤더 가드를 표현할 수 있지만 모든 컴파일러가 지원하지는 않는다.
  - [Google Style Guide](https://google.github.io/styleguide/cppguide.html#The__define_Guard)에서는 `<PROJECT>_<PATH>_<FILE>_H_` 헤더 가드를 사용한다.
    ```c++
    #ifndef FOO_BAR_BAZ_H_
    #define FOO_BAR_BAZ_H_

    ...

    #endif  // FOO_BAR_BAZ_H_
    ```
- `#define`, `#ifdef`는 Modern C++에서는 사용을 지양한다. (`constexpr` 권장)

<br>
