---
title: "C++ 정리 - Specifiers"
excerpt: ""

categories:
  - Cpp

toc: true
toc_sticky: false

date: 2023-02-24
last_modified_at: 2023-02-24
---

# Storage Class Specifiers
- 변수와 함수의 연결 및 저장 기간을 결정하는 지정자

## static
- `static` 지정자는 사용하는 상황에 따라 의미가 다르다.
- 지역 변수에 `static`을 쓰면 프로그램 종료까지 살아 있는 정적 지역 변수를 의미한다.
  - 정적 지역 변수는 함수가 호출될 때 한 번만 초기화되며 연속적인 함수 호출에서도 값을 유지(공유)한다.
- 전역 변수/함수에 `static`을 쓰면 현재 파일(오브젝트 파일) 안에서만 사용 가능한 정적 전역 변수/함수를 의미한다.
  - 다른 파일에 같은 변수 이름이 있어도 `static`을 쓰면 서로 다른 변수로 취급한다.
- 클래스의 멤버 함수로 `static`을 쓰면 객체를 생성하지 않아도 호출할 수 있는 정적 멤버 함수를 의미한다.
  - 대신 내부에서 `this`를 사용해야 쓸 수 있는 멤버 변수, 멤버 함수를 사용하지 못한다.
- 클래스의 멤버 변수에 `static`을 쓰면 해당 클래스로 생성되는 객체들이 모두 공유할 수 있는 정적 멤버 변수를 의미한다.


## extern
- `extern` 지정자는 컴파일러에게 전역 변수/함수가 다른 파일에 정의되어 있다는 것을 알려준다.
- `extern "C"`는 C++ 네임 맹글링(Name Mangling) 때문에 사용한다.
  - C++ 네임 맹글링 :  함수를 오버로딩할 때 오류가 발생하지 않도록 컴파일러가 같은 이름의 함수를 다른 이름으로 바꾸는 것을 말한다. (컴파일 타임에 일어나서 정적 다형성(Static Polymorphism)이라 부른다.)
  - C에서는 네임 맹글링이 없기 때문에 C++에서 C 라이브러리나 함수를 사용할 경우 컴파일러에게 네임 맹글링을 하지 말라고 `extern "C"` 키워드를 넣어준다.

## thread_local
- `thread_local` 지정자는 스레드마다 독립적인 저장 공간을 가지는 전역(global)/정적(static) 변수를 만들 수 있다.
- `thread_local`은 동일한 스레드 내에서 `static` 처럼 한 번만 초기화되며 연속적인 함수 호출에서도 값을 유지(공유)한다.

<br>

# Function Specifiers

- 함수의 동작 및 관계에 대한 정보를 수정하거나 제공하는 지정자

## explicit
- `explicit` 지정자는 클래스 생성자에서 암시적 형변환(implicit conversions) 혹은 복사 초기화(copy initialization)가 발생하지 않도록 제한한다.

```c++
class A {
  A(int) { }
};
 
class B {
  explicit B(int) { }
};

void testA(A a) {
  // ...
}

void testB(B b) {
  // ...
}

int main() {
  A a1 = 1; // 복사 초기화 사용 가능
  testA(1); // 암시적 형변환 가능

  B b1 = 1; // 복사 초기화 불가능
  testB(1); // 암시적 형변환 불가능
}
```

## noexcept
- `noexcept` 지정자는 컴파일러에게 함수가 예외를 발생시키지 않는다는 것을 알려준다.
- `noexcept` 사용 시 장점
  - 함수의 예외 발생 여부를 명확하게 알 수 있다.
  - 컴파일러 최적화에 유용하다.
    - `noexcept`를 사용하면 컴파일러가 스택 되감기(stack unwinding)에 필요한 추가 코드를 생성하지 않는다.
  - STL 최적화에 유용하다.
    - `std::vector<T>::resize()` 함수는 T의 이동 생성자가 `noexcept`인 경우에만 객체를 복사하지 않고 이동한다.
- C++11부터 모든 메모리 해제 함수와 모든 소멸자는 암묵적으로 `noexcept`로 선언된다.

## virtual
- `virtual` 지정자는 컴파일러에게 자식 클래스에서 함수를 재정의 한다는 것을 알려준다.
- `virtual`을 사용해야만 vtable이 생성되어 부모 클래스 포인터로 자식 클래스를 가리킬 때 제대로된 메서드를 호출할 수 있다.

## override, final
- `override` 지정자는 컴파일러에게 부모 클래스에서 있는 함수를 재정의 한다는 것을 알려준다.
- `final` 지정자는 컴파일러에게 부모 클래스에 있는 함수를 마지막으로 재정의 한다는 것을 알려준다.
- `virtual` 함수를 상속받은 함수는 `override` 혹은 `final`를 사용해야 한다.
  - 사용하지 않을 경우, 암시적으로 `override`가 적용된다.
- `override`와 `final`은 생략할 수 있지만, 사용하면 컴파일 경고를 받을 수 있고 코드를 보는 입장에서 해당 함수가 오버라이딩하는 함수인지 쉽게 알 수 있다.

## default(defaulted function)
- `= default` 지정자(defaulted function)는 컴파일러에게 클래스에 대한 특수 멤버 함수를 생성하도록 지시한다.

## delete(deleted function)
- `= delete` 지정자(deleted function)는 컴파일러에게 함수를 삭제하도록 지시한다.
- deleted function의 장점
  - 의도가 명확하다.
    - deleted function을 사용하면 삭제된 함수를 사용했다는 에러 메시지를 통해 의도를 명확하게 알 수 있다.
  - 에러를 발견하는 시점이 빠르다.
    - deleted function을 사용하면 컴파일 과정에서 에러 메시지를 알 수 있다.
    - 정의되지 않은 private function을 사용하면 링킹 과정에 가서야 에러 메시지를 확인할 수 있다.
  - 어떤 함수도 삭제할 수 있다.
    - 정의되지 않은 private function은 클래스 멤버 함수에서만 사용 가능하다.
  - 함수가 의도하지 않은 타입으로 오버로딩되는 것을 막을 수 있다.
    - ```c++
      bool isLucky(int number);
      bool isLucky(char) = delete;
      bool isLucky(bool) = delete;
      bool isLucky(double) = delete;
      ``` 
  - 원치 않는 템플릿 인스턴스화를 방지한다.
    - e.g. `void processPointer<void>(void*) = delete;`

## friend
- `friend` 지정자는 클래스 혹은 함수에게 `private` 및 `protected` 멤버에 접근할 수 있는 권한을 준다.

<br>

# CV Type Qualifiers
- 타입의 불변성 혹은 변동성을 수정하는 지정자

## const
- `const` 지정자는 초기화 후에 변수의 값을 수정할 수 없다.
- 멤버 함수에 붙는 `const`는 멤버 변수의 값을 바꾸지 않는 상수 함수를 의미한다.
  - 상수 함수 내에서는 상수 함수만 호출할 수 있다.

## volatile
- `volatile` 지정자는 컴파일러가 변수를 최적화하지 못하게 한다.
- 하드웨어 제어나 멀티스레드 환경에서 의도하지 않은 컴파일러 최적화를 방지한다.

## mutable
- `mutable` 지정자는 `const` 멤버 함수 내에서도 멤버 변수를 변경할 수 있게 한다.


<br>

# Type Deduction Specifiers
- 변수 또는 표현식의 타입을 추론하는 지정자

## auto
- `auto` 지정자는 선언되는 변수의 타입이 초기화 과정에서 자동으로 추론되도록 한다.
- `auto` 사용 시 장점
  - 초기화를 잊어버릴 일이 없다.
  - 길고 복잡한 타입을 간단하게 표현할 수 있다.
  - 컴파일러만 알 수 있는 타입을 지정할 수 있다.
  - type shortcut 문제를 피할 수 있다. (플랫폼 이식성이 좋다.)
    - e.g. `std::vector<int>::size_type`은 64/32비트 시스템에 따라 크기가 다름
  - 실수할 수 있는 타입 선언을 줄여 준다.
    - e.g. `std::unordered_map<std::string, int>`의 요소들은 `std::pair<std::string, int>` 타입이 아닌 `std::pair<const std::string, int>` 타입이다.
- `auto` 사용 시 주의할 점
  - 내가 원치 않는 타입으로 선언될 수 있다.
    - e.g. "invisible" proxy class (`std::vector<bool>::reference`, expression template 기법을 사용하는 라이브러리 등)
    - 이를 해결하기 위해 `auto`로 선언하되 초기화 표현식을 명시적으로 캐스팅하는 방법이 있다.
  - 타입 지정자(type specifier)가 pointer 혹은 reference가 아닌 경우, 타입 추론 과정에서 expression의 참조성(reference-ness)과 상수성(const-ness)을 무시한다.
    - Range-based for loop를 사용하여 STL 컨테이너를 순회할 때 `auto&` 대신 `auto`를 사용하면 실제 원소에 접근하지 않고 복사본을 생성한다.
  - 함수의 리턴 타입이나 람다 매개변수에 `auto`를 사용하면 template 타입 추론이 적용된다.
    - `auto` 타입 추론에서는 braced initializer를 `std::initializer_list<T>`로 추론한다.
    - template 타입 추론에서는 braced initializer를 `std::initializer_list<T>`을 추론하지 못해서 컴파일 오류가 발생한다. (사용하고 싶으면 직접 명시해야 한다.)

## decltype
- `decltype` 지정자는 변수나 표현식에 선언된 타입을 있는 그대로 추론한다.
- `decltype`이 유용한 경우
  - template 함수의 리턴 타입을 추론하는 경우
    - C++11에서 후행 반환 타입(trailing return type)과 `decltype`을 사용해서 추론 가능하다.
    - C++14부터는 `decltype(auto)`로 추론 가능하다.
  - 정확한 타입을 추론하고자 하는 경우
    - `auto`는 표현식의 const-ness를 무시하고 array를 pointer로 추론하지만 `decltype`은 타입 그대로 추론할 수 있다.

<br>

# Compile-time Evaluation Specifiers
- 컴파일 타임에 평가된 값 또는 함수와 관련된 지정자

## constexpr
- `constexpr` 지정자는 해당 식이 상수 표현식(constant expression)임을 명시한다.
  - 상수 표현식 : 컴파일러가 컴파일 타임에 값을 결정할 수 있는 식
- `constexpr`를 사용하면 해당 객체나 함수의 리턴 값을 컴파일 타임에 알 수 있음을 의미한다.

<br>

# Type Alias Specifiers
- 기존 타입에 대한 동의어 혹은 별칭을 만드는 지정자

## typedef, using
- `typedef`, `using` 지정자는 타입 이름을 새로운 별칭으로 선언(alias declaration)한다.
- C++11 이전에는 `typedef`를 사용했지만 이후부터는 `using` 사용을 권장한다.

```c++
using UPtrMapSS = std::unique_ptr<std::unordered_map<std::string, std::string>>;
```

<br>

# Type Definition Specifiers
- 새로운 타입을 정의하는 지정자

## enum(enscoped enum), enum class(scoped enum)
- `enum` 지정자는 모든 값이 기호 상수로 정의되는 자료형(enumerated type)을 의미한다.
- `enum class` 지정자는 C++11부터 도입되었으며, `enum`이 범위를 벗어나는 문제를 해결하기 위해 사용한다.
- `enum class` 장점
  - namespace 오염을 줄여준다.
    - `enum class`는 범위 연산자(`::`) 없이는 사용할 수 없다.
  - 열거자들의 타입이 강력하게 적용된다.
    - `enum class`는 열거자들의 타입이 암묵적으로 변환되지 않는다.
  - 전방 선언(forward declaration)이 가능하다.
    - `enum class`의 기본 바탕 타입(underlying type)이 `int`로 정해져 있기 때문에 전방 선언이 가능하다.
- `enum`이 유용한 경우
  - `std::tuple` 안에 있는 필드를 지칭하는 경우, `enum`이 가독성이 제일 좋다.
    - 인덱스 사용 : `auto val = std::get<1>(uInfo);`
    - `enum` 사용 : `auto val = std::get<uiEmail>(uInfo);`
    - `enum class` 사용 : `auto val = std::get<static_cast<std::size_t>(UserInfoFields::uiEmail)>(uInfo);`

<br>