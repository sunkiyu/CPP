## Universal reference 보편적 레퍼런스   
```cpp
template <typename T>
void wrapper(T&& u) {
  g(std::forward<T>(u));
}

//오로지 우측값만 받을 수 있다.
void show_value(int&& t) { std::cout << "우측값 : " << t << std::endl; }
```
*  __템플릿__ 인자 T 에 대해서, 우측값 레퍼런스를 받는 형태를 보편적 레퍼런스(Universal reference) 라고 한다.
*  템플릿 타입의 우측값 레퍼런스(보편적 레퍼런스)는 우측값 뿐만이 아니라 좌측값 역시 받아낼 수 있다.
