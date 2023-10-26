---
title: "C++ 정리 - Functional Programming"
excerpt: ""

categories:
  - Cpp

toc: true
toc_sticky: false

date: 2023-02-26
last_modified_at: 2023-02-26
---

# Functional Programming

- 함수를 변수처럼 사용하는 프로그래밍 방식

## std::function
- `std::function`은 Callable들을 객체의 형태로 보관할 수 있는 객체이다.
  - Callable : C++에서 `()`를 붙여서 호출할 수 있는 모든 것을 의미한다.
    - 함수, 함수 포인터, 람다 표현식, 함수 객체(Functor=Functon Object) 등
      - 함수 객체 : 함수처럼 호출 가능한 클래스 객체로 `operator()`를 통해 구현된다.
- `std::function`에 멤버 함수를 포함하는 경우, 어떤 클래스의 객체인지 명시해줘야 한다.
  - `std::function<int(A&)> f1 = &A::some_func;`

## std::bind
- `std::bind`는 함수에 특정 인자를 붙여준다.
- 인자로 reference를 전달하고 싶은 경우, `std::ref` 함수를 사용한다. (const reference의 경우, `std::cref`)

```c++
void add(int x, int y) {
  std::cout << x << " + " << y << " = " << x + y << std::endl;
}

void subtract(int x, int y) {
  std::cout << x << " - " << y << " = " << x - y << std::endl;
}

int main() {
  auto add_with_2 = std::bind(add, 2, std::placeholders::_1);
  add_with_2(3);

  // 두 번째 인자는 무시된다.
  add_with_2(3, 4);

  auto subtract_from_2 = std::bind(subtract, std::placeholders::_1, 2);
  auto negate = std::bind(subtract, std::placeholders::_2, std::placeholders::_1);

  subtract_from_2(3);  // 3 - 2 를 계산한다.
  negate(4, 2);        // 2 - 4 를 계산한다
}
```

## 람다 표현식(Lambda Expression)
- 람다 표현식은 함수 포인터나 함수 객체를 정의하지 않고 필요한 지점에서 이름 없는 함수 객체(익명 함수)를 만드는 방법이다.
- 람다 표현식에 의해 생성되는 객체 인스턴스를 클로저(Closure)라고 한다.
- 람다 캡처(Lambda Capture)를 통해 함수 외부에 있는 변수를 복사 혹은 참조할 수 있다.
  - 함수 외부에 있는 변수가 복사되거나 참조되는 시점은 클로저가 생성될 때이다.
  - 자세한 규칙은 [cpp reference lambda capture](https://en.cppreference.com/w/cpp/language/lambda#Lambda_capture) 참고
- 클로저의 타입은 `std::function`으로 표현할 수도 있지만 `auto`를 사용하여 컴파일러가 추론하는 것이 속도와 메모리 측면에서 좋다.
  - `auto`를 사용하는 변수는 클로저와 같은 타입이므로 필요한 크기의 메모리만 사용한다. 그러나 `std::function`을 사용하는 변수는 고정된 메모리 크기를 사용하여 더 많은 메모리를 소비하며, 만약 클로저가 고정된 메모리 크기보다 크다면 heap 메모리에 저장해야 한다.
  - `std::function`은 구현 세부 사항들(간접 함수 호출, 인라인화 제한)이 함수 호출을 느리게 만들어 `auto`를 사용하는 것보다 호출 속도가 느리다.

```c++
// captures, paramters, return type, statement로 구성되어 있다.
[my_mod](int v_) -> int { return v_ % my_mod; }
```

<br>

