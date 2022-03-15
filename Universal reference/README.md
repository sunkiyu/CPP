## Universal reference 보편적 레퍼런스   
```cpp
template <typename T>
void wrapper(T&& u) {
  g(std::forward<T>(u));
}

//오로지 우측값만 받을 수 있다.
void show_value(int&& t) { std::cout << "우측값 : " << t << std::endl; }

typedef int& T;
T& r1;   // int& &; r1 은 int&
T&& r2;  // int & &&;  r2 는 int&

typedef int&& U;
U& r3;   // int && &; r3 는 int&
U&& r4;  // int && &&; r4 는 int&&

//& =1, && =0 이라 생각하고 OR 연산하면된다.
```
*  __템플릿__ 인자 T 에 대해서, 우측값 또는 좌측값을 받는 형태를 보편적 레퍼런스(Universal reference) 라고 한다.
*  템플릿 타입의 우측값 레퍼런스(보편적 레퍼런스)는 우측값 뿐만이 아니라 좌측값 역시 받아낼 수 있다.
* 아무때나 move 를 하면 안됨. 인자로 받은 u 가 우측값 레퍼런스 일 때 에만 move 를 해줘야 한다.(g() u가 좌측값이기 때문에 원복인 우측값을 그대로 전달해주기 위해)
* 좌측값 레퍼런스일 때 move 를 해버린다면 좌측값에 오버로딩 되는 g 가 아닌 우측값에 오버로딩 되는 g 가 호출.
* => 이 문제를 해결해주는 것이 forward 함수. 이 함수는 u 가 우측값 레퍼런스 일 때 에만 마치 move 를 적용한 것 처럼 동작함

[참조 씹어먹는 C\+\+ - \<12 - 2. Move 문법 (std\:\:move semantics) 과 완벽한 전달 (perfect forwarding)\>](https://modoocode.com/228)
