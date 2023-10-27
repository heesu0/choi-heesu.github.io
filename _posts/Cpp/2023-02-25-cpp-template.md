---
title: "C++ 정리 - Template"
excerpt: ""

categories:
  - Cpp

toc: true
toc_sticky: false

date: 2023-02-25
last_modified_at: 2023-02-25
---

# Template

## 템플릿(Template)
- 클래스나 함수를 개별적으로 다시 작성하지 않아도, 여러 자료형으로 사용할 수 있도록 하게 만들어 놓은 틀이다.
- 템플릿은 컴파일(Compile) 과정에서 호출되는 타입에 맞게 실제 코드로 실체화(Instantiation)된다.
  - 만약 템플릿의 선언과 정의가 같은 파일에 있지 않으면 컴파일 과정에서 어떤 타입으로 템플릿을 생성할지 몰라서 에러가 발생한다.
  - 템플릿의 선언과 정의를 헤더 파일과 소스 파일로 분리하고 싶으면 특정 타입으로 explicit instantiation 해야 한다.
- 사용 대상에 따라 클래스 템플릿(Class Template)과 함수 템플릿(Function Template)으로 나뉜다.

```c++
template <typename T>
class CT {
private:
  T t;
};

CT<std::string> ct;
```

```c++
template <typename T>
void func(T t);

func(10); // func<int>(10);
```

## 템플릿 타입 추론(Template Type Deduction)
- 함수 템플릿은 클래스 템플릿을 실체화할 때와 다르게 `<>` 부분을 생략하고 컴파일러가 타입을 추론하게 할 수 있다.
- 템플릿 타입 추론 시 주의할 점
  - 컴파일러는 자신의 앞에 정의되어 있는 함수들만 볼 수 있기 때문에 함수 템플릿를 작성할 때는 순서에 유의해야 한다.
  - 인수(argument)가 배열이나 함수 이름인 경우, 타입 추론 과정에서 포인터로 붕괴(decay)되어 추론된다.
    - ParamType이 reference인 경우, 타입 추론 과정에서 포인터로 decay되지 않는다.

## 템플릿 특수화(Template Specialization)
- 템플릿 특수화는 특정 타입을 따로 처리할 수 있다.
- 전달하고 싶은 템플릿 인자가 없더라도 특수화를 하고 싶으면 `template<>`를 남겨줘야 한다.

```c++
template <typename A, typename B, typename C>
class test {};
 
template <typename B>
class test<int, B, double> {};
 
template <>
class test<int, int, double> {};
```

## 디폴트 템플릿 인자(Default Template Parameters)
- 템플릿은 디폴트 인자를 지정할 수 있다.

```c++
template <typename T, int num = 5>
T add_num(T t) {
  return t + num;
}
 
int main() {
  int x = 3;
  std::cout << "x : " << add_num(x) << std::endl;
}
```
 
## 타입이 아닌 템플릿 인자(None Type Parameters)
- 템플릿은 타입이 아닌 인자도 받을 수 있다.
  - 정수 타입 (bool, char, int, long 등)
  - 포인터 타입
  - `enum` 타입
  - `std::nullptr_t`
- `std::array`도 동일한 방법으로 배열 크기를 받는다.

```c++
template <typename T, int num>
T add_num(T t) {
  return t + num;
}
 
add_num<int, 5>(x)
```

## 가변 길이 템플릿 인자(Variadic Template Parameters)

- 템플릿은 파라미터 팩(Parameter Pack)을 사용하여 임의의 개수의 인자를 받을 수 있다.
  - 파라미터 팩 : 0개 이상의 템플릿 인자를 받는 표현 방식
  - 템플릿 파라미터 팩은 `template <typename T, typename... Types>` 처럼 타입 앞에 `...`이 온다.
  - 함수 파라미터 팩은 `void print(T arg, Types... args)` 처럼 타입 뒤에 `...`이 온다.
- 가변 길이 템플릿 사용 시 주의할 점
  - 가변 길이 템플릿 함수의 우선순위가 높다.
  - `template <typename T>`와 같은 베이스 템플릿(베이스 케이스)을 먼저 정의해야 가변 길이 템플릿을 사용할 수 있다.
    - 재귀적으로 함수가 호출되어 결국 베이스 템플릿을 호출하기 때문이다.

```c++
template <typename T>
void print(T arg) {
  std::cout << arg << std::endl;
}
 
template <typename T, typename... Types>
 void print(T arg, Types... args) {
  std::cout << arg << ", ";
  print(args...);
}
 
int main() {
  print(1, 3.1, "abc");
  print(1, 2, 3, 4, 5, 6, 7);
}
```

## 별칭 템플릿(Alias Template)
- 별칭 템플릿을 사용하면 컴파일 타임에 별칭을 정할 수 있다.

```cpp
template<typename T>
using custom_array = std::vector<std::array<T, 64>>

custom_array<int> test;
```

## 변수 템플릿(Variable Template)
- 변수 템플릿을 사용하면 컴파일 타임에 원하는 타입에 맞는 변수 값을 정의할 수 있다. (C++14부터 적용)


```cpp
template<class T>
constexpr T pi = T(3.14159265359);

int intPI = pi<int>;
float floatPI = pi<float>;
double doublePI = pi<double>;
```


## SFINAE(Substitution Failure Is Not An Error)
- 템플릿 인자 치환에 실패할 경우, 컴파일러는 이 오류를 무시하고 오버로딩 후보에서 제외한다.
  - 컴파일러는 템플릿 인자 치환 과정에서 단순히 함수의 인자들과 리턴 타입만이 문법적으로 올바른지만 확인한다.
  - 따라서 함수 내부에 문법 오류가 있어도 오버로딩 후보에서 제외되지 않는다. 
- SFINAE를 활용하면 원하지 않는 타입을 오버로딩 후보군에서 제외할 수 있다.

```c++
template <typename T>
void test(typename T::x a) {
  std::cout << "T::x \n";
}
 
template <typename T>
void test(typename T::y b) {
  std::cout << "T::y \n";
}
 
struct A {
  using x = int;
};
 
struct B {
  using y = int;
};
 
int main() {
  test<A>(33);
  test<B>(22);
}
```

## enable_if

- `enable_if`는 SFINAE를 활용하여 조건에 맞지 않는 함수들을 오버로딩 후보군에서 제외할 수 있는 템플릿 메타 함수이다.

```c++
template <typename T, typename = typename std::enable_if<std::is_integral<T>::value>::type>
void test(const T& t) {
  std::cout << "t : " << t << std::endl;
}

int main() {
  test(1);      // int
  test(false);  // bool
  test('c');    // char
}
```