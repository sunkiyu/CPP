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
* pa를 일반 포인터이기 때문에 소멸자가 호출되지 않는다. 포인터 객체로 만들면 자신이 소멸될 때 자신이 가르키고 있는 데이터도 같이 delete 하면 되는데
* 이 기능을 제공하는 것을 스마트 포인터라한다.
***
## unique_Ptr 
* 메모리 관련 문제는 2가지가 발생할 수 있다.
* 1. 메모리 사용 후 해제를 하지 않아 누수 메모리가 계속 증가하여 프로그램이 뻗어버림(RAII 패턴을 통해 해결할 수 있다.)   
* 2. 이미 해제된 메모리를 참조하는 문제
```cpp
Data* data = new Data();
Date* data2 = data;
delete data;
// ...
// 메모리 오류가 나면서 프로그램이 죽는다. Double Free Bug
delete data2;
```
* 포인터에 소유권을 부여하여 소유권이 있는 포인터 말고는 메모리 해제를 못하게하여 해결가능하다.
* 즉 delete data는 가능하지만 delete data2는 불가능하도록 한다.
* 이렇게 특정 객체에 유일한 소유권을 부여하는 포인터 객체를 UniquePtr이라 부른다.
```cpp
class A {
  int *data;

 public:
  A() {
    std::cout << "자원을 획득함!" << std::endl;
    data = new int[100];
  }

  void some() { std::cout << "일반 포인터와 동일하게 사용가능!" << std::endl; }

  ~A() {
    std::cout << "자원을 해제함!" << std::endl;
    delete[] data;
  }
};

void do_something() {
  std::unique_ptr<A> pa(new A());
  //unique_ptr 은 -> 연산자를 오버로드해서 포인터를 다루는 것과 같이 사용할 수 있음
  pa->some();
}

int main() { do_something(); }
```
* pa는 스택에 정의된 객체이기 때문에 Scope를 벗어나면 소멸자가 호출된다.
* 그리고 이 UniquePtr의 소멸자 안에서 자신이 가르키고 있는 객체를 delete 해준다.
```cpp
std::unique_ptr<A> pa(new A());
//오류 발생 삭제 함수 사용
std::unique_ptr<A> pb = pa;

//unique 함수 정의를 들어가보면 복사생성자와 복사 대입연산자가 delete 되어있다.
//unique_ptr 는 어떠한 객체를 유일하게 소유해야 하기 때문.
// Disable copy from lvalue.
unique_ptr(const unique_ptr&) = delete;
unique_ptr& operator=(const unique_ptr&) = delete;
```
* unique_ptr의 소유권 이전은 가능하다.
```cpp
  std::unique_ptr<A> pa(new A());
  pa->some();

  // pb 에 소유권을 이전.
  //unique_ptr 은 복사 생성자는 정의되어 있지 않지만, 이동 생성자는 가능.
  std::unique_ptr<A> pb = std::move(pa);
  pb->some();
  
  //이동 생성자가 아래와 같이 정의 되어 있다.   
   /// Move constructor.
   unique_ptr(unique_ptr&& __u) noexcept
   : _M_t(__u.release(), std::forward<deleter_type>(__u.get_deleter())) { }
```

```cpp
class Foo {
  int a, b;

 public:
  Foo(int a, int b) : a(a), b(b) { std::cout << "생성자 호출!" << std::endl; }
  void print() { std::cout << "a : " << a << ", b : " << b << std::endl; }
  ~Foo() { std::cout << "소멸자 호출!" << std::endl; }
};

int main() {
  //make_unique 함수는 템플릿 인자로 전달된 클래스의 생성자에 인자들에 직접 완벽한 전달을 수행
  auto ptr = std::make_unique<Foo>(3, 5);
  ptr->print();
}
```
```cpp
std::vector<std::unique_ptr<A>> vec;
std::unique_ptr<A> pa(new A(1));
//vector의 복사 생성자를 호출 Error
vec.push_back(pa);

int main() {
  std::vector<std::unique_ptr<A>> vec;
  std::unique_ptr<A> pa(new A(1));
  //이동 생성자 호출 OK
  vec.push_back(std::move(pa));  
}

td::vector<std::unique_ptr<A>> vec;

  // vec.push_back(std::unique_ptr<A>(new A(1))); 과 동일
  vec.emplace_back(new A(1));

  vec.back()->some();
```
* emplace_back 함수를 이용하면, vector 안에 unique_ptr 을 직접 생성 하면서 집어넣는다.(불필요한 이동 과정을 생략)
* emplace_back 함수는 전달된 인자를 완벽한 전달(perfect forwarding) 을 통해, 직접 unique_ptr<A> 의 생성자에 전달 해서, vector 맨 뒤에 unique_ptr<A> 객체를 생성. 
* [참조 씹어먹는 C \+\+ - \<13 - 1. 객체의 유일한 소유권 - unique_ptr\>](https://modoocode.com/229)
***   
## shared_ptr
* 여러 객체에서 하나의 자원을 사용하고자 한다.
* 추후에 자원을 해제하기 위해서는 이 자원을 사용하는 모든 객체들이 소멸되야 한다.(자원이 해제된 상태에서 다른 객체가 참조하여 에러가 발생할 수 있기 때문)
* 어떤 객체가 먼저 소멸되는지 알 수 없기 때문에 이 자원 역시 어느 타이밍에 해제 시켜야 할 지 알 수 없음.
* 해결방안 =>  특정 자원을 몇 개의 객체에서 가리키는지를 추적한 다음에, 그 수가 0 이 되야만 비로소 해제를 시켜주는 방식의 포인터가 필요.(SharedPtr)
	
```cpp
std::shared_ptr<A> p1(new A());
std::shared_ptr<A> p2(p1);  // p2 역시 생성된 객체 A 를 가리킨다.

// 반면에 unique_ptr 의 경우
std::unique_ptr<A> p1(new A());
std::unique_ptr<A> p2(p1);  // 컴파일 오류!
```
![image](https://user-images.githubusercontent.com/68372094/156302657-d120a39a-daa2-464e-aeff-7c4683a5242a.png)
*  참조 개수가 0 이 되어야 가리키고 있는 객체를 해제할 수 있다
```cpp
std::shared_ptr<A> p1(new A());
std::shared_ptr<A> p2(p1);  // p2 역시 생성된 객체 A 를 가리킨다.

std::cout << p1.use_count();  // 2
std::cout << p2.use_count();  // 2
```
* 현재 shared_ptr 의 참조 개수가 몇 개 인지는 use_count 함수를 통해 알 수 있다.
![image](https://user-images.githubusercontent.com/68372094/156303201-7803fdc8-4d2c-4324-92f7-e9cd59abccf9.png)
* shared_ptr 가 제어 블록(control block) 을 동적으로 할당한 후, shared_ptr 들이 이 제어 블록에 필요한 정보를 공유
```cpp
class A : public std::enable_shared_from_this<A> {
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

  std::shared_ptr<A> get_shared_ptr() { return shared_from_this(); }
};

int main() {
  //shared_ptr의 ref_count를 관리하는 제어블록을 새로 생성
  std::shared_ptr<A> pa1 = std::make_shared<A>();
  //앞서 이미 생성된 제어 블록에 ref_count를 증가시킨다. ref_count = 2;
  //But, enable_shared_from_this<A>를 상속받지 않았으면 동일한 코드라도 제어 블록을 또 생성하게 되므로
  //pa1과 pa2가 각각 ref_count=1로 서로다른 제어 블록을 관리하게 되어 p1이 먼저 소멸하면 가르키고 있는 메모리가 날라가는데
  //pa2가 소멸할 때 이미 해제된 메모리를 Double Free 해주려고 하여 에러가 발생한다.
  std::shared_ptr<A> pa2 = pa1->get_shared_ptr();

  std::cout << pa1.use_count() << std::endl;
  std::cout << pa2.use_count() << std::endl;
}
```
![image](https://user-images.githubusercontent.com/68372094/156503082-ca38e92e-74e4-44ac-9bb4-bdd31c1d287c.png)
![image](https://user-images.githubusercontent.com/68372094/156503143-b60010f4-ef87-4e20-aa18-bd96d77f67ec.png)

* enable_shared_from_this 클래스에는 shared_from_this 라는 멤버 함수를 정의하고 있는데, 이 함수는 이미 정의되어 있는 제어 블록을 사용해서 shared_ptr 을 생성.
* shared_from_this 가 작동하기 위해서는 해당 객체의 shared_ptr 가 반드시 먼저 정의되어 있어야 한다.
***
## weak_ptr
* weak_ptr 그 자체로는 원소를 참조할 수 없고, shared_ptr 로 변환해야 해야함. lock 함수를 통해 수행
* weak_ptr 에 정의된 lock 함수는 만일 weak_ptr 가 가리키는 객체가 아직 메모리에서 살아 있다면 (즉 참조 개수가 0 이 아니라면) 해당 객체를 가리키는 shared_ptr 을 반환하고, 
 이미 해제가 되었다면 아무것도 가리키지 않는 shared_ptr 을 반환
* 순환 참조 상대를 weak_ptr로 가르킨다.
```cpp
void access_other() {
  //other은 weak_ptr로 상대를 가르키고 있다.
  std::shared_ptr<A> o = other.lock();
  if (o) {
    std::cout << "접근 : " << o->name() << std::endl;
  } else {
    std::cout << "이미 소멸됨 ㅠ" << std::endl;
  }
}
```
* [참조 씹어먹는 C \+\+ - \<13 - 2. 자원을 공유할 때 - shared_ptr 와 weak_ptr\>](https://modoocode.com/252)
