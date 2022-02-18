## 가변길이 템플릿   
* 임의의 개수의 인자를 받는 템플릿
```cpp
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
>template <typename T, typename... Types>
* typename뒤에 오는 ...를 **템플릿 파라미터 팩**이라한다.   
>void print(T arg, Types... args) {
* 함수 인자로 ...가 오는 것을 **함수 파라미터 팩**이라한다.   

> concat = s1 + s2 + s3;
> concat = s1.operator+(s2).operator+(s3); 와 같다.   
* s2와 s3를 더할 때 메모리 할당 과정이 필요하다.   
```cpp
//재귀 호출의 베이스 케이스 설정
template <typename String>
std::string StrCat(const String& s) {
  return std::string(s);
}

template <typename String, typename... Strings>
std::string StrCat(const String& s, Strings... strs) {
  return std::string(s) + StrCat(strs...);
}

int main() {
  // std::string 과 const char* 을 혼합해서 사용 가능하다.
  std::cout << StrCat(std::string("this"), " ", "is", " ", std::string("a"),
                      " ", std::string("sentence"));
}
```
* 위의 코드 또한 string + 작업 마다 메모리 할당 과정이 필요하다.   

* string이 합쳐질 때마다 메모리 할당이 필요없도록 템플릿을 만들어보자   
```cpp
//문자열 전체 길이를 반환하는 템플릿 함수
size_t GetStringSize(const char* s) { return strlen(s); }

size_t GetStringSize(const std::string& s) { return s.size(); }

template <typename String, typename... Strings>
size_t GetStringSize(const String& s, Strings... strs) {
  return GetStringSize(s) + GetStringSize(strs...);
}
```
> 문자열의 전체 길이를 알아낸 후 reserve를 통해 미리 공간 할당을 받는다.

```cpp
template <typename String, typename... Strings>
std::string StrCat(const String& s, Strings... strs) {
  // 먼저 합쳐질 문자열의 총 길이를 구한다.
  size_t total_size = GetStringSize(s, strs...);

  // reserve 를 통해 미리 공간을 할당해 놓는다.
  std::string concat_str;
  concat_str.reserve(total_size);

  concat_str = s;

  // concat_str 에 문자열들을 붙인다.
  AppendToString(&concat_str, strs...);

  return concat_str;
}

```
* 문자열을 합치는 템플릿 함수
```cpp
// 재귀 호출의 마지막 단계로 strs... 아무 인자도 없을 때 까지 진행하기 때문에 재귀 호출을 끝내기 위해 AppendToString(std::string* concat_str)을 만든다.   
void AppendToString(std::string* concat_str) { return; }

template <typename String, typename... Strings>
void AppendToString(std::string* concat_str, const String& s, Strings... strs) {
  concat_str->append(s);
  AppendToString(concat_str, strs...);
}
```

> 평균을 구하는 템플릿 함수
```cpp
// 재귀 호출 종료를 위한 베이스 케이스
int sum_all() { return 0; }

template <typename... Ints>
int sum_all(int num, Ints... nums) {
  return num + sum_all(nums...);
}

template <typename... Ints>
double average(Ints... nums) {
//sizeof... 에 파라미터 팩 (nums) 를 전달하면 nums 에 해당하는 실제 인자의 개수를 리턴
  return static_cast<double>(sum_all(nums...)) / sizeof...(nums);
}

int main() {
  // (1 + 4 + 2 + 3 + 10) / 5
  std::cout << average(1, 4, 2, 3, 10) << std::endl;
}
```
* C++ 11 에서 도입된 가변 길이 템플릿 단점이 \=\> 재귀 함수 형태로 구성해야 하기 때문에, 반드시 재귀 호출 종료를 위한 함수를 따로 만들어야함.

```cpp
// 재귀 호출 종료를 위한 베이스 케이스
// 재귀 함수 호출을 종료하기 위해 베이스 케이스를 꼭 만들줘야함.
int sum_all() { return 0; }

```
C\+\+ 17에서는 Fold 형식을 도입하여 베이스 케이스를 생략해도 되도록 하였다.
```cpp
template <typename... Ints>
int sum_all(Ints... nums) {
  return (... + nums);
}
```
