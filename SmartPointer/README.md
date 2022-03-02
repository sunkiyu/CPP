## Smart Pointer
* C\+\+은 한 번 획득한 자원은 직접 해제해주지 않는 이상 프로그램이 종료되기 전 까지 영원히 남아있다.
* 자원관리 디자인 패턴 (RAII => 자원의 획득은 초기화다 - *Resource Acquisition Is Initialization*)
* RAII는 자원관리를 스택에 할당한 객체를 통해서 한다.
* 예외가 발생해서 함수를 빠져나가도 그 함수의 스택에 정의되어 있는 모든 객체들은 빠짐없이 소멸자가 호출된다는 특성을 이용한다.
```cpp
class A {
  int *data;

 public:
  A() {
    data = new int[100];
    std::cout << "자원을 획득함!" << std::endl;
  }

  ~A() {
    std::cout << "소멸자 호출!" << std::endl;
    delete[] data;
  }
};

void do_something() { A *pa = new A(); }
```
* pa를 일반 포인터가 아닌 포인터 객체로 만들면 자신이 사라질 때 자신이 가르키고 있는 데이터도 같이 delete 하면 되는데
* 이 기능을 제공하는 것을 스마트 포인터라한다.
***
## UniquePtr 

***
