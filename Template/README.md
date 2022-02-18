## Template   
* template \<typename T\>와 template \<class T\>는 같은 의미이다.   
* template는 인스턴스화 되어야지만 컴파일러가 실제 코드를 생성한다.   

***
### 템플릿 특수화(일부의 경우를 따로 처리)   
```cpp
//템플릿 정의
template <typename A, typename B, typename C>
class test {};

//A가 int이고 C가 double일 경우 따로 처리
template <typename B>
class test<int, B, double> {};

//A,B,C 모두 int int double 타입으로 처리
template <>
class test<int, int, double> {};

//템플릿 인자가 없더라도 특수화 할 경우
//template<>를 남겨줘야한다.
template <>
class Vector<bool> {
  ... 
}
```
* 함수 템플릿   

```cpp
template <typename T>
T max(T& a, T& b) {
  return a > b ? a : b;
}

int a = 1, b = 2;
                                                   //템플릿 함수가 인스턴스화 하는 부분
std::cout << "Max (" << a << "," << b << ") ? : " << max(a, b) << std::endl;

//컨테이너를 정렬하는 템플릿
//컨테이너 안에 size(), swap(), [] 연산자가 정의되의 었어야 정상적으로 동작한다.
template <typename Cont>
void bubble_sort(Cont& cont) {
  for (int i = 0; i < cont.size(); i++) {
    for (int j = i + 1; j < cont.size(); j++) {
      if (cont[i] > cont[j]) {
        cont.swap(i, j);
      }
    }
  }
}
```
*  컴파일 시에 모든 템플릿을 실제 코드로 변환하므로 템플릿으로 발생되는 오류는 프로그램이 실행되었을 때가 아니라 컴파일 할 때 발생한다.
```cpp
template <typename Cont, typename Comp>
void bubble_sort(Cont& cont, Comp& comp) {
  for (int i = 0; i < cont.size(); i++) {
    for (int j = i + 1; j < cont.size(); j++) {
      //comp는 함수가 아니라 객체이며, Comp클래스에서 ()연산자를 오버로딩하였다.
      //Comp1 과 Comp2 객체들은 bubble_sort 함수 안에서 함수처럼 사용됨
      if (!comp(cont[i], cont[j])) {
        cont.swap(i, j);
      }
    }
  }
}
struct Comp1 {
  bool operator()(int a, int b) { return a > b; }
};

struct Comp2 {
  bool operator()(int a, int b) { return a < b; }
};
int main()   
{
   ...
   bubble_sort(int_vec, comp1);
}
```
* Comp1 과 Comp2 객체들은 bubble_sort 함수 안에서 함수처럼 사용됨
* 함수는 아니지만 함수처럼 동작하는 객체를 **함수 객체 \(Function Object\=\=Functor\)** 라고 한다.   
* Functor 는 컴파일러가 operator() 자체를 인라인화 시켜서 빠르게 작업 수행
* Template에 인자를 넘길 수 있는데 해당 인자는 다음과 같다.  [참고](https://en.cppreference.com/w/cpp/language/template_parameters)   
-bool, char, int, long 등  float,double 제외   
-포인터 타입   
-enum 타입   
-nullptr   
*  C스타일 배열을 함수에 전달할 때 배열의 크기에 대한 정보를 잃어버리는데 템플릿 인자를 활용하면 유용하다   
```cpp
template <typename T, int num>
T custom_add_num(T t) {
  return t + num;
}

int main() {
  int x = 3;
  std::cout << "x : " << custom_add_num<int, 5>(x) << std::endl;
}

//디폴트 템플릿 인자도 사용이 가능하다
template <typename T, int num = 5>
T add_num(T t) {
  return t + num;
}

int main() {
  int x = 3;
  std::cout << "x : " << add_num(x) << std::endl;
}

struct Compare {
  bool operator()(const T& a, const T& b) const { return a < b; }
};

int a = 3, b = 4;
std::cout << "min : " << Min<int, Compare<int>>(a, b);

```
