---
title: "C++ 정리 - Pointer, Reference, LR Value"
excerpt: ""

categories:
  - Cpp

toc: true
toc_sticky: false

date: 2023-02-21
last_modified_at: 2023-02-21
---

# Pointer, Reference, LR Value

## 매개변수(Parameter), 인자(Argument)
- 매개변수(parameter)는 함수의 정의 부분에 나열된 변수(variable)이다.
- 인수(argument)는 함수가 호출될 때 함수로 전달해주는 값(value)이다.

```cpp
// a와 b는 parameter
int sum(int a, int b) {
  return a + b;
}

int result = sum(1, 2); // 1, 2는 argument
```

## 네임스페이스(Namespace)
- 개체를 구분할 수 있는 범위를 나타내는 말로 정의된 객체에 대해 어느 소속인지 지정한다.
- `std::`는 C++ 표준 라이브러리의 함수, 객체 등이 정의된 네임스페이스이다.
- 익명 네임스페이스(anonymous namespace)를 사용하면 해당 파일에서만 사용 가능하게 범위를 정할 수 있다.

```cpp
namespace A {
  // ...
}

// anonymous namespace
namespace {
  // ...
}
```

## 포인터(Pointer)
- 메모리 상에서 위치한 특정한 데이터의 (시작) 주소값을 저장하는 변수이다.
- 말 그대로 특정 변수를 가리키는(pointing) 변수라고 할 수 있다.
- 포인터 함께 사용되는 연산자는 다음과 같다.
  - `*` : 역참조 연산자(dereference operator)를 사용하면 해당 포인터가 가리키는 변수의 값을 반환한다.
  - `&` : 주소 연산자(address operator)를 사용하면 해당 변수의 주소값을 반환한다.
- 포인터는 오른쪽에서 왼쪽으로 영어로 읽으면 이해하기 편하다.
  - `int* p` : p is pointer to int (p는 int의 포인터이다.)
  - `const int** p` : p is pointer to pointer to const int (p는 상수 int를 가리키는 포인터의 포인터이다.)
- 여러 포인터 변수를 선언할 때 주의가 필요하다. (ex. `int* a, b; // 이러면 a만 포인터`)

```cpp
int n = 10;
int* p = &n;

std::cout << n << std::endl;  // 10
std::cout << *p << std::endl; // 10
std::cout << p << std::endl;  // 0x16f38b968
```


## 상수 포인터(Const Pointer)
- `const` 다음에 오는 값이 변하면 안된다.

```cpp
int a, b;
const int* pa = &a; // pa is pointer to const int (pa는 상수 int를 가리키는 포인터)

*pa = 3; // 에러 발생
pa = &b; // 가능
```
```cpp
int a, b;
int* const pa = &a; // pa is const pointer to int (pa는 int를 가리키는 상수 포인터)

*pa = 3; // 가능
pa = &b  // 에러 발생
```

## 포인터(Pointer)와 배열(Array)의 차이
- 배열은 일종의 포인터이기 때문에 포인터도 배열처럼 사용할 수 있다.
- 포인터 변수와 배열 변수는 모두 데이터의 시작 주소를 가리키고 있으며 `[]`(인덱스 연산자)를 사용해서 접근할 수 있다.
- 하지만 포인터와 배열은 차이가 존재한다.
  1. 배열의 변수는 주소값을 변경할 수 없는 상수 포인터(const pointer)이다.
  2. 배열의 사이즈는 배열의 총 크기지만 포인터의 사이즈는 포인터의 크기이다.
  3. 배열은 리터럴을 이용한 문자열 초기화가 가능하지만, 포인터는 불가능하다.

```cpp
int a[] = {1, 2, 3, 4};
int* pa = a;

std::cout << sizeof(a) << std::endl;   // 4 * 4 = 16
std::cout << sizeof(pa) << std:: endl; // 8 (64bit 주소값)

int b = 10;
a = &b;  // 배열은 상수 포인터라 불가능
pa = &b; // 가능

char str[] = "hello"; // "hello" 리터럴을 배열에 복사한다. (스택 영역)
char* pstr = "hello"; // "hello" 리터럴을 가리킨다. (데이터 영역)

str[1] = 'a';  // 가능
pstr[1] = 'a'; // 불가능

// 리터럴을 이용해서 문자열을 초기화 할거면 const를 붙여서 사용하는 것을 권장한다.
const char* pstr = "hello";
```

## 배열 포인터(Array Pointer)와 포인터 배열(Pointer Array)의 차이
- 배열 포인터 : 배열을 가리키는 포인터

```cpp
int arr[3] = {1, 2, 3};
int* p = arr; //arr 말고 &arr[0] 해도 같다.

printf("%d %d", arr[1], p[1]) // 2 2
// p[1]은 *(p + 1) 와 같다.

int arr[3] = {1, 2, 3};
int (*p)[3] = &arr;

printf("%d %d", arr[1], (*p)[1]); // 2 2 
pritnf("%p %p", arr, p) // 둘 다 같은 주소값을 가진다.

int arr[2][3] = { {1, 2, 3}, {4, 5, 6} };
int (*p)[3] = arr;

printf("%d %d", arr[1][1], p[1][1]); // 5 5 
pritnf("%d %d", arr[1][1], (*(p+1))[1]); // 5 5
pritnf("%d %d", arr[1][1], *(*(p+1)+1)); // 5 5
// *(*(p+i)+j) i는 행 j는 열
```
- 포인터 배열 : 포인터를 모아 놓은 배열

```cpp
int* arr[3];
int a = 1, b = 2, c = 3;
arr[0] = &a;
arr[1] = &b;
arr[2] = &c;

printf("%d %d", a, *arr[0]); // 1 1
printf("%d %d", b, *arr[1]); // 2 2
printf("%d %d", c, *arr[2]); // 3 3
```

## Null Pointer
- 어떤 변수도 가리키고 있지 않은 포인터
- C++11 전까지는 포인터를 초기화할 때 `NULL`을 사용했다.
- C++에서 컴파일러는 `NULL`을 상수 0으로 정의한다.
  - C에서는 `((void *)0)`이 다른 포인터 형과 호환되지만 C++에서는 불가능하기 때문이다.
- 따라서 포인터를 초기화에 `NULL`을 사용하면 다음과 같은 문제가 발생할 수 있다.

```cpp
void print(int input) {
  cout << "integer " << endl;
}

void print(int* input) {
  cout << "pointer" << endl;
}

print(10);     // integer
print(NULL);   // NULL을 인자로 받는 함수 입장에서 정수인지 포인터인지 구별 불가
```
```cpp
int* a = NULL;

// a가 int 값의 0인지 포인터 타입인지 헷갈릴 수 있다.
if (a == 0) {
  cout << "a == 0" << endl;
}
```

- 이 문제를 해결하기 위해 C++11에서는 `nullptr`이라는 전역 객체가 추가되었다.
- `nullptr`의 데이터 타입은 `std::nullptr_t`으로 모든 타입의 포인터로 암시적 형 변환이 가능하다.

```cpp
print(10);      // integer
print(nullptr); // pointer
```
```cpp
int* a = nullptr;

// a가 포인터 타입인지 알 수 있다.
if (a == nullptr) {
  cout << "a == nullptr" << endl;
}
```

## 좌측값(Lvalue), 우측값(Rvalue)
- 주소값을 취할 수 있는 값을 lvalue
- 주소값을 취할 수 없는 값을 rvalue
- lvalue는 `=`(등호)의 좌변, 우변에 모두 올 수 있지만 rvalue는 우변에만 올 수 있다.

```cpp
int a = 3; // a는 lvalue, 3은 rvalue
```

## 참조자(Reference)
- 참조자는 변수의 별명이다. 즉, 변수와 완전히 동일하게 동작한다.
- 참조자는 내부적으로 포인터처럼 참조하는 변수의 주소값을 저장하고 있다.
- 참조자는 다음과 같은 제약 조건이 있다.
  - 참조자는 반드시 처음에 누구의 별명이 될 것인지 지정해야 한다. (상수는 불가능하다.)
  - 한 번 별명이 되면 절대 다른 이의 별명이 될 수 없다.
  - 참조자의 참조자는 불가능하다. (포인터의 포인터는 가능하다.)
  - 참조자가 리터럴을 참조하려면 앞에 `const`를 붙여야 한다.
- 참조자는 메모리 상에 존재하지 않을 수 있다.
- 참조자는 값의 종류에 따라 좌측값 참조자(lvalue reference)와 우측값 참조자(rvalue reference)로 나눌 수 있다.


## 좌측값 참조자(Lvalue Reference)
- 참조자를 선언할 때 `&`를 붙이면 lvalue reference 이다.
- lvalue reference는 lvalue만 참조할 수 있다.
- 만약 rvalue를 참조하고 싶으면 `const` 키워드를 붙여야 한다. (리터럴 참조가 가능하다.)
- 일반적으로 lvalue reference를 참조자로 지칭한다.

```cpp
int a = 10;            // a는 lvalue
int& ref_a = a;        // ref_a는 lvalue reference
int& ref_a = 10;       // rvalue를 참조하려고 하면 오류가 발생한다.
const int& ref_a = 10; // const를 붙이면 rvalue도 참조 가능하다.
```
- 함수 인자를 받을 때 참조자를 사용하면 포인터를 사용하는 것보다 더 직관적인 코드를 짤 수 있다.

```cpp
// *를 사용하지 않아 코드가 더 직관적이다.
void change_val(int &p) {
  p = 3;
}

int number = 5;
change_val(number);
```
- 리턴 타입으로 참조자를 사용할 때는 주의가 필요하다.

```cpp
int& get_val() {
  int a = 2;
  return a;
}

int b = get_val(); // 참조하고 있는 변수가 사라진다. (Dangling Reference)
b = 3; // 에러 발생! 
```
- `const`를 사용하면 리턴 값의 생명을 연장할 수 있다.

```cpp
int get_val() {
  int a = 5;
  return a;
}

const int& c = get_val();
std::cout << "c : " << c << std::endl;
```

## 우측값 참조자(Rvalue Reference)
- 참조자를 선언할 때 `&&`를 붙이면 우측값 참조자이다.
- 우측값 참조자는 우측값만 참조할 수 있다.
- 우측값 참조자는 주소값을 취할 수 있으므로 우측값이 아닌 좌측값이다.

```cpp
int b = 10;        // b는 lvalue
int&& ref_b = b;   // lvalue를 참조하려고 하면 오류가 발생한다.
int&& ref_b = 10;  // ref_b는 rvalue reference
```

- 우측값 참조자는 참조하는 임시 객체를 소멸하지 않게 유지 시킬 수 있다.

```cpp
int plusFive(int n) {
  return n + 5;
}

plusFive(5); // return 값이 10인 임시 객체(rvalue)가 생성된다. 이 라인이 끝나면 임시 객체는 소멸한다.

int&& n = plusFive(5); // rvalue reference를 통해 임시 객체를 생성하면 이 라인이 끝나도 소멸되지 않는다.
std::cout << &n << std::endl; // 임시 객체의 주소에도 접근할 수 있다.
```

## 보편 참조(Universal Reference)
- universal reference는 참조하는 객체가 lvalue면 lvalue reference가 되고 rvalue면 rvalue reference가 되는 유연성을 가진 reference이다.
- 클래스의 이동 생성자/이동 할당 연산자를 호출하기 위한 파라미터 타입으로 사용된다.
- universal reference 조건
  - 타입 추론이 일어나야 한다.
    - 타입 추론이 일어나지 않으면 rvalue reference 이다.
    - 템플릿 클래스 안의 함수들은 타입 추론이 일어나지 않는다. 템플릿 함수에서 타입 추론이 일어난다.
  - `T&&` 형태여야 한다.
    - 타입 추론이 일어나도 `T&&` 형태가 아니면 rvalue reference 이다.
    - `const T&&`와 `std::vector<T>&&` 도 rvalue reference 이다.
- universal reference는 오버로딩 우선순위가 높아서 오버로딩을 사용하지 않는 것이 좋다.


## 이동 문법(Move Semantics)
- move semantics는 객체를 생성하거나 대입할 때 이름이 있는 객체는 복사하고 이름이 없는 임시 객체는 이동하여 불필요한 복사를 방지하는 기능이다.
- move semantics는 이동 생성자(move constructor)와 이동 대입 연산자(move assignment operator)를 통해 구현된다.
- move semantics는 기존 객체에 있는 모든 멤버 변수의 주소를 이동하는 객체에게 넘겨주고 기존 객체가 가지고 있는 주소값을 지우기 때문에 소유권을 이전한다고 표현한다.

```cpp
#include <iostream>
#include <string>

class Cat {
public:
  // 기본 생상자(default constructor)
  Cat() = default;

  // 기본 소멸자(default destructor)
  ~Cat() = default;

  // 커스텀 생성자(custom constructor)
  Cat(std::string name) : name_{std::move(name)} {}

  // 복사 생성자(copy constructor)
  Cat(const Cat& other) : name_{other.name_} {}

  // 이동 생성자(move constructor)
  Cat(Cat&& other) noexcept : name_{std::move(other.name_)} {}

  // 복사 대입 연산자(copy assign operator)
  Cat& operator=(const Cat& other) {
     // 같은 객체의 대입을 방지
    if(&other == this) {
      return *this;
    }
    name_ = other.name_;
    return *this;
  }

  // 이동 대입 연산자(move assign operator)
  Cat& operator=(Cat&& other) noexcept {
    // 같은 객체의 대입을 방지
    if(&other == this) {
      return *this;
    }
    name_ = std::move(other.name_);
    return *this;
  }

private:
  std::string name_;
};

int main() {
  Cat cat1{"cat1"}; // 커스텀 생성자
  Cat cat2{"cat2"}; // 커스텀 생성자

  Cat cat3(cat1);            // 복사 생성자 호출, cat1 객체의 멤버 변수를 cat3 객체의 멤버 변수가 복사한다.
  Cat cat4(std::move(cat1)); // 이동 생성자 호출, cat1 객체의 멤버 변수가 cat4 객체의 멤버 변수로 이동한다.

  cat3 = cat2;            // 복사 대입 연산자 호출, cat2 객체의 멤버 변수를 cat3 객체의 멤버 변수가 복사한다.
  cat4 = std::move(cat2); // 이동 대입 연산자 호출, cat2 객체의 멤버 변수가 cat4 객체의 멤버 변수로 이동한다.

  // 임시 객체(rvalue)를 인자로 넣어줬으니 이동 생성자가 호출될 것 같지만 커스텀 생성자가 호출된다.
  // 컴파일러가 최적화를 통해 바로 커스텀 생성자를 호출해준다. -> 복사 생략(copy elision)
  Cat cat5(Cat{"cat5"});

  return 0;
}
```

## std::move
- lvalue를 rvalue로 캐스팅(타입 변환)하는 함수
- C++11부터 `<utility>` 라이브러리에서 제공한다.
- `std::move`는 실제로 이동 시키는 것이 아니라 단순히 타입을 변환하는 함수이다.
- `std::move`를 argument로 받는 함수는 이를 rvalue로 인식하여 특정 목적에 맞게 사용할 수 있다. (lvalue 객체를 복사 생성/대입이 아닌 이동 생성/대입으로 구현할 수 있다.)
- 즉, `std::move` 자체가 의미 있는게 아니라 `std::move`를 인자로 받은 쪽에서 어떻게 사용하는지가 핵심이다.
- std::move 사용 시 주의할 점
  - 이동에 사용할 객체는 `const`로 선언하면 이동 연산이 아닌 복사 연산이 일어난다.
    - const rvalue 인자를 사용하면 객체의 복사 생성자/복사 할당 연산자가 호출되기 때문이다.
  - primitive type(`int`, `char`, `double` 등등)은 `std::move`를 사용할 필요가 없다.

```cpp
class Cat {
public:
  // 이 경우 인자가 lvalue든 rvalue든 인자에서 1 copy 발생
  void setName(string name) {
    _name = move(name);
  }
  
  // 이 경우 인자가 lvalue인 상황에만 사용 가능하며 0 copy 발생
  // void setName(string& name) {
  //   _name = move(name);
  // }
  
  // 이 경우 인자가 lvlaue든 rvalue든 const를 move해서 1 copy 발생
  // void setName(const string& name) {
  //   _name = move(name);
  // }
  
  // 이 경우 인자가 rvalue인 상황에만 사용 가능하며 0 copy 발생
  // void setName(string&& name) {
  //   _name = move(name);
  // }
private:
  string _name;
};

int main(){
  Cat cat;
  string name = "maru";
  
  // 인자로 넘어갈 때 한번 copy하고 move
  cat.setName(name); // 1 copy

  // 이론상 인자로 넘어갈 때 한번 copy가 일어나고 그 다음 move가 일어날거 같지만
  // copy elison(optimization)으로 컴파일러가 name이 바로 "nabi"를 가리킨다. 즉, move만 일어난다.
  cat.setName("nabi"); // 0 copy
  
  return 0;
}
```
```cpp
class Cat {
public:
  Cat() : _s("hello") {
    cout << "Default Constructor" << endl;
  }
  Cat(const Cat& cat) : _s(cat._s) {
    cout << "Copy Constructor" << endl;
  }
  Cat(Cat&& cat) : _s(move(cat._s)){
    cout << "Move Constructor" << endl;
  }
  void show(){
    cout << _s << endl;
  }
private:
  string _s;
};

void storeByValue(Cat s){
  Cat sn = s;
  sn.show();
}

void storeByLRef(Cat& s){
  Cat sn = s;
  sn.show();
}

void storeByRRef(Cat&& s){
  Cat sn = move(s);
  sn.show();
}

int main(){
  Cat s;
  storeByValue(s); // 2 copy
  storeByLRef(s); // 1 copy
  storeByRRef(move(s)); // 0 copy

  return 0;
}
```

## std::forward
- `std::forward`는 lvalue reference를 lvalue로 rvalue reference를 rvalue로 캐스팅(타입 변환)하는 함수이다.
- 인자가 상황에 따라 lvalue와 rvalue가 둘 다 들어올 수 있을 때 유용하다.
- 템플릿의 universal reference(forward reference)와 함께 자주 쓰인다.
  - 함수가 자신의 인자들을 그대로 다른 함수에 전달하는 perfect fowarding을 구현할 수 있다.

```cpp
template <class T>
void wrapper(T&& param) {
  foo(std::forward<T>(param));
}
```

## push_back, emplace_back
- `push_back`과 `emplace_back`은 `std::vector` 컨테이너에 요소를 추가하는 함수이다.
- `push_back` 함수에 lvalue를 인자로 넣으면 복사 연산을 수행하고 rvlaue를 인자로 넣으면 이동 연산을 수행한다. (이동 연산 조건이 갖추지 않았으면 복사 연산을 수행한다.)
- `emplace_back` 함수에 lvalue를 인자로 넣으면 복사 연산을 수행하지만 rvalue가 임시 객체일 경우, 이동 연산이 아니라 컨테이너 내부에서 객체를 생성한다.
  - 이게 가능한 이유는 `emplace_back` 내부에 `std::forward`를 사용하여 perfect forwarding을 구현했기 때문이다.


## Call by Value, Call by Pointer, Call by Reference
- call by value는 함수가 호출되면 argument의 값을 parameter로 복사하는 방식이다.
- call by pointer와 call by reference는 함수가 호출되면 argument의 주소를 parameter로 전달하는 방식으로 주소를 전달하기 때문에 함수 내부에서 parameter를 통해 참조하는 변수의 값을 변경할 수 있다.
- call by pointer와 call by reference는 어셈블리 레벨에서는 똑같지만, 코드를 구현하는데 있어서는 차이가 존재한다.
  - call by reference는 `nullptr`을 허용하지 않고 `*`(역참조 연산자) 없이 일반 변수처럼 사용할 수 있어서 가독성이 좋다는 장점이 있다.
  - call by pointer는 주소값을 변경할 수 있을 정도로 자유도가 높지만 필요한 상황이 아니면 불필요한 실수를 방지하기 위해 reference를 쓰는 것을 권장한다.

```cpp
// call by value
void funcV(int a){
  a = a + 1;
}

// call by pointer
void funcP(int* a){
  *a = *a + 1;
}

// call by reference
void funcR(int& a){
  a = a + 1;
}

int n = 0;
funcV(n);
std::cout << n << std::endl; // 0 

funcP(&n);
std::cout << n << std::endl; // 1

funcR(n);
std::cout << n << std::endl; // 2
```

## Return by Value, Return by Pointer, Return by Reference
- return by value는 return 뒤의 객체를 복사한 임시 객체를 반환한다. (rvalue를 반환)
- return by pointer는 return 뒤의 객체의 주소값을 반환한다. (lvalue를 반환)
- return by reference는 return 뒤의 실제 객체를 반환한다. (lvalue를 반환)
- RVO와 같이 컴파일러에서 해주는 최적화도 생각해야 한다.

## RVO(Return Value Optimization)
- 컴파일러에 의해 최적화되는 반환 값
  - 함수가 호출되면 스택 프레임이 쌓이고 그 안에 반환할 객체에 대한 주소값을 가지고 있다.
  - 그 이전 스택 프레임에는 반환받을 값에 대한 메모리 공간이 있고 함수가 종료되면 해당 공간으로 반환 값의 복사가 일어난다.
  - RVO는 처음부터 반환 받을 메모리 공간에 값을 할당해서 불필요한 복사를 방지한다. (복사 생략, Copy Elision)
- RVO 조건
  1. 지역 객체의 타입이 함수의 반환 타입과 같아야 한다.
  2. 지역 객체가 함수의 반환 값이어야 한다.
- RVO의 대상이 될 수 있는 지역 객체에는 `std::move` 혹은 `std::forward`를 적용하면 안된다.
  - `std::move` 혹은 `std::forward`를 적용하면 지역 객체가 아닌 지역 객체의 참조자를 반환하기 때문에 RVO 2번 조건을 위배한다.
- 변수 이름이 있나 없나로 NRVO, RVO로 나뉘지만 동작은 똑같다.

```cpp
// NRVO(Named Return Value Optimization)
std::string getString(){
  std::string s = "hello";
  return s;
}

// RVO(Return Value Optimization)
std::string getString(){
  return std::string("hello");
}

// getString() 메서드 내에서 s 객체가 생성되면서 생성자가 호출되고 이를 반환되면서
// a 객체의 복사 생성자가 호출될거 같지만 실제로는 a 생성자만 한번 호출된다.
std::string a = getString();
```

## Encapsulation Return Type
- 클래스 내부의 멤버 변수를 외부에서 참고할 때 복사하지 않고 참조할 수 있다.
- 그 대신 외부에서 참조자를 변경할 수 없게 `const`를 붙여주는 것이 좋다.

```cpp
class Cat {
public:
  Cat() : _s("coco") {};
  std::string name() const {
    return _s;
  }
private:
  std::string _s;
};

Cat cat;
std::string s = cat.name(); // 멤버 변수를 복사

class Cat {
public:
  Cat() : _s("coco") {};
  const std::string& name() const {
    return _s;
  }
private:
  std::string _s;
};

// 여기서 멤버 변수를 변경할 수 없게 const 키워드를 붙여준다.
const std::string& s = cat.name(); // 멤버 변수의 레퍼런스를 전달한다. (복사가 일어나지 않는다.)
```

<br>

