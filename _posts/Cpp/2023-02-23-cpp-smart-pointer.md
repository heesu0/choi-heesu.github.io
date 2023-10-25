---
title: "C++ 정리 - Smart Pointer"
excerpt: ""

categories:
  - Cpp

toc: true
toc_sticky: false

date: 2023-02-23
last_modified_at: 2023-02-23
---

# Smart Pointer

## C++ 메모리 관리 문제점
1. 메모리를 사용한 후에도 해제하지 않은 경우 (Memory Leak, 메모리 누수)
    - 프로그램이 점점 사용하는 메모리 양이 늘어나 나중에 시스템 메모리가 부족해질 수 있다.
2. 이미 해제된 메모리를 다시 참조하는 경우
    - 이미 delete한 객체를 다시 한번 delete할 경우 double free 에러로 프로그램이 죽는다.
    - 이는 객체의 소유권이 명확하지 않아서 발생한다.

## 생 포인터(Raw Pointer)의 한계점
- 포인터가 하나의 객체를 가리키는지 배열을 가리키는지 구분하기 어렵다.
- `delete`와 `delete[]` 중 뭘 써야 하는지 알기 어렵다.
- 포인터를 사용한 후에 가리키는 객체를 삭제해야 하는지 알 수 없다. (포인터가 가리키고 있는 객체에 대한 소유권을 알 수 없다.)
- 파괴 방법을 알 수 없다. (`delete`를 사용해서 자원을 파괴해야 하는지 특정한 함수를 호출하여 자원을 파괴해야 하는지 알 수 없다.)
- 파괴는 한 번만 하도록 신경 써야 한다. (중복 해제를 방지해야 한다.)
- 포인터가 가리키는 객체가 여전히 살아있는지 알 방법이 없다.

## 스마트 포인터(Smart Pointer)
- 포인터를 일반적인 포인터가 아니라 객체로 만들어서 자신이 소멸할 때 자신이 가리키고 있는 데이터도 같이 `delete` 해서 자원(메모리) 관리를 스택의 객체(포인터 객체)를 통해 수행할 수 있다.
- 스마트 포인터는 C++11부터 `<memory>` 라이브러리에서 제공한다.

## RAII(Resource Acquisition Is Initialization)
- 자원의 획득은 초기화다. (자원의 라이프 사이클과 객체의 라이프 사이클을 일치시킨다.)
- 자원 관리를 스택에 할당된 객체를 통해 수행하는 것을 말한다.
- 함수의 스택에 정의되어 있는 모든 객체들은 종료할 때든 예외 상황이 발생할 때든 빠짐 없이 소멸자가 호출된다.

## std::unique_ptr
- 특정 객체에 유일한 소유권(exclusive ownership)을 부여하는 포인터 객체
- 해당 객체를 가리키는 포인터는 하나만 존재할 수 있다.
- 다른 포인터에게 객체의 소유권을 넘겨주려면 `std::move`를 사용한다.
- `std::unique_ptr`는 복사 생성자가 명시적으로 삭제된다. (복사 생성을 할 수 있으면 소유권 문제가 발생하기 때문)
- 소유권이 이전된 `std::unique_ptr` 를 댕글링 포인터(dangling pointer) 라고 하며 이를 재 참조할 시에 런타임 오류가 발생한다.
- 클래스 멤버 변수로 `std::unique_ptr`를 사용하면 복사 생성자나 복사 할당 연산자가 호출될 때 에러가 발생할 수 있다.
- 사용자가 직접 복사 생성자를 수정해야할 수 있다.
- `std::unique_ptr`의 장점
  - 가리키는 대상이 명확하다.
    - 개별 객체(`std::unique_ptr<T>`)를 가리키면 인덱스 연산자(`operator[]`)를 지원하지 않고 배열 객체(`std::unique_ptr<T[]>`)를 가리키면 역참조 연산자(`operator→`, `operator*`)를 지원하지 않는다.
  - `std::unique_ptr`는 `std::shared_ptr`로 변환이 쉽다.
- `std::unique_ptr`의 custom deleter 사용 시 주의할 점
  - custom deleter도 타입에 포함된다.
  - 포인터의 크기가 달라진다.
    - 기본 삭제자를 사용할 경우 : raw pointer와 크기가 동일하다. (1워드)
    - 함수 포인터를 custom deleter를 사용할 경우 : raw pointer의 2배 (2워드) + 함수 객체에 저장된 크기
    - capture 없는 람다 표현식을 사용할 경우 : raw pointer와 크기가 동일하다. (1워드)


## std::shared_ptr
- 특정 객체에 대해 공유된 소유권(shared ownership)을 가지는 포인터 객체
- 공유 자원을 관리하기 위해 참조 횟수(reference count)를 사용한다.
  - reference count : 현재 객체를 가리키고 있는 `std::shared_ptr`의 갯수
  - `std::shared_ptr`의 생성자는 reference count를 증가시키고 `std::shared_ptr`의 소멸자는 reference count를 감소시킨다.
  - reference count가 0이 되면 해당 객체를 가리키는 포인터가 없다고 판단하여 자원을 파괴한다.
  - reference count 연산은 thread-safe한 원자적 연산이다. (연산이 많을수록 성능이 떨어진다.)
- `std::shared_ptr`가 관리하는 객체당 하나의 제어 블록(control block)이 존재한다.
  - control block : reference count, weak count, custom deleter 등을 저장하고 있는 자료구조
  - `std::shared_ptr`의 크기는 raw pointer의 2배이다. (자원을 가리키는 포인터 + control block을 가리키는 포인터)
  - custom deleter가 control block에 저장되기 때문에 `std::unique_ptr` 처럼 타입에 포함되거나 포인터 크기에 영향을 주지 않는다.
- `std::shared_ptr` 사용 시 주의할 점
    - raw pointer로 `std::shared_ptr`를 정의하는 방법을 피해야 한다.
      - 잘못 사용하면 control block이 두 개가 생성되는 상황이 생길 수 있다.
        ```c++
        auto pw = new Widget;
        
        // *pw에 대한 control block이 생성됨
        std::shared_ptr<Widget> spw1(pw, loggingDel);
        
        // *pw에 대한 두 번째 control block이 생성됨
        std::shared_ptr<Widget> spw2(pw, loggingDel);
        ```
        ```c++
        std::vector<std::shared_ptr<Widget>> processedWidgets;
        
        // 외부에서 Width을 가리키는 std::shared_ptr이 있다고 가정
        class Widget {
        public:
          void process(void);
        };
          
        void Widget::process(void) {
          processedWidgets.emplace_back(this); // 새로운 control block이 생성
        }
        ```
    - `std::shared_ptr`는 `std::unique_ptr`로 변환할 수 없다.
    - `std::shared_ptr`는 배열을 관리할 수 없다.
      - 배열 객체를 관리하고 싶으면 `std::array` 혹은 `std::vector`를 쓰는 것이 좋다.
    - 클래스 내부에 `std::shared_ptr`를 만들 경우 복사 생성 과정에서 `std::shared_ptr`가 같은 객체를 가리키고 있을 수 있다.
      - 이는 shallow copy와 동일한 상황이므로, 이를 방지하기 위해 clone 메서드를 구현하는 것이 좋다.


## std::weak_ptr

- `std::shared_ptr`와 비슷하지만 reference count에 영향을 주지 않는 스마트 포인터
- 객체를 참조하되 객체의 수명에는 영향을 주고 싶지 않을 때 사용한다.
- `std::weak_ptr`의 용도
  - caching에 사용하는 경우
    - `std::unique_ptr`는 객체에 대한 소유권을 넘겨줘야 하므로 caching이 불가능하지만, `std::shared_ptr`와 `std::weak_ptr`를 사용하면 객체의 수명에 영향을 끼치지 않으면서 객체 포인터를 caching 하는 함수를 구현할 수 있다.
      ```c++
      std::shared_ptr<const Widget> fastLoadWidget(WidgetID id) {
        static std::unordered_map<WidgetID, std::weak_ptr<const Widget>> cache;
    
        // 객체가 cache에 없으면 nullptr
        auto objPtr = cache[id].lock();
    
        if (!objPtr) {
          // cache에 없으면 추가
          objPtr = loadWidget(id);
          cache[id] = objPtr;
        }
    
        return objPtr;
      }
      ```
  - observer pattern에 사용하는 경우
    - observer pattern : subject(관찰 대상, 상태가 변할 수 있는 객체)와 observer(관찰자, 상태 변화를 통지 받는 객체)로 구성된 디자인 패턴
    - 대부분의 observer pattern 구현에서 subject 객체들은 자신을 관찰하는 observer 객체 포인터들을 멤버 변수로 저장하고 있다. (상태 변화가 생기면 통지해주기 편리하기 때문)
    - 이때 subject 객체는 파괴된 observer 객체에 접근하는 일이 없도록 `std::weak_ptr`를 사용하여 observer 객체 포인터들을 저장할 수 있다.
  - `std::shared_ptr`의 circular reference 해결에 사용하는 경우
    - circular reference : 두 개의 클래스가 서로를 가리키고 있는 `std::shared_ptr`를 만들면 영원히 메모리가 해제되지 않는 메모리 릭이 발생

## make 함수 (std::make_unique, std::make_shared)
- 임의의 타입 인자들을 받아서 그것들을 생성자로 perfect forwarding 하여 객체를 동적으로 생성하고 그 객체를 가리키는 스마트 포인터를 돌려주는 함수
- C++11 표준 라이브러리에는 `std::make_shared`만 포함되어 있고 C++14 표준 라이브러리부터 `std::make_unique`를 지원한다.
- make 함수 장점
  - 코드 중복을 피할 수 있다.
    ```c++
    auto upw1(std::make_unique<Widget>());    // make 함수 사용
    std::unique_ptr<Widget> upw2(new Widget); // 사용 안함

    auto spw1(std::make_shared<Widget>());    // make 함수 사용
    std::shared_ptr<Widget> spw2(new Widget); // 사용 안함
    ```
  - 예외 안정성이 높다.
    ```c++
    // new Widget으로 객체를 동적 할당하고 computePriority() 함수에서 예외가 발생하면 std::shared_ptr<Widget>의 생성자를 호출하지 않으면서 메모리 누수 발생
    processWidget(std::shared_ptr<Widget>(new Widget), computePriority());
    
    // std::make_shared를 사용하면 Widget 객체를 동적 할당하고 std::shared_ptr<Widget>를 생성하는 과정이 하나의 과정으로 묶어서 메모리 누수 방지
    processWidget(std::make_shared<Widget>(), computePriority());
    ```
  - 효율성이 향상된다.
    - `std::make_shared`를 사용해서 `std::shared_ptr`를 만들면 컴파일러가 객체와 control block 모두를 담을 수 있는 크기의 메모리를 한 번에 할당한다.
- make 함수 단점
  - custom deleter를 사용할 수 없다.
  - 중괄호 초기화를 사용할 수 없다.
  - `std::shared_ptr`만 가지는 단점
    - custom new/delete를 사용하는 클래스는 사용할 수 없다.
    - 메모리 해제 시점이 늦어져 메모리 낭비가 생길 수 있다.
      - `std::make_shared`를 사용해서 `std::shared_ptr`를 만들면 컴파일러가 객체와 control block을 한번에 할당하여 weak count가 0이 될때까지 객체도 해제되지 않는다.

<br>
