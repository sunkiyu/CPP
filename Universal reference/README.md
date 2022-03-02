## Universal reference 보편적 레퍼런스   
```cpp
template <typename T>
void wrapper(T&& u) {
  g(std::forward<T>(u));
}
```
*  __템플릿__ 인자 T 에 대해서, 우측값 레퍼런스를 받는 형태를 보편적 레퍼런스(Universal reference) 라고 한다.
