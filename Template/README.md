* Template에 인자를 넘길 수 있는데 해당 인자는 다음과 같다.  [참고](https://en.cppreference.com/w/cpp/language/template_parameters)
-bool, char, int, long 등  float,double 제외   
-포인터 타입   
-enum 타입   
-nullptr   
```cpp
template <typename T, int num>
T custom_add_num(T t) {
  return t + num;
}

int main() {
  int x = 3;
  std::cout << "x : " << custom_add_num<int, 5>(x) << std::endl;
}
```
