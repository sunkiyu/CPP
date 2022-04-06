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

```cpp
class A;
void g(A&& arg)
{
    cout<<"Right Value Ref"<<endl;
}

class A
{
public:
    int memA;
};

int main()
{
   A valA;
   g(valA); //컴파일 에러
   g(A()); //OK
	 
	 return 0;
}
```
* 일반 함수에서 &&파라미터는 우측값 밖에 받지 못한다.
* 하지만 템플릿을 사용한다면 &&는 우측값과 좌측값 모두를 받게 된다.

```cpp
template <typename T>
void g(T&& arg)
{
    cout<<"Right Value Ref"<<endl;
}

template <typename T>
void g(T& arg)
{
    cout<<"Left Value Ref"<<endl;
}

template <typename T>
void wrapper(T&& u) {
  g(u);
}

class A
{
public:
    int memA;
};

int main()
{
   A valA;
   wrapper(valA);//...1
   wrapper(A());//...2
   return 0;
}
```
출력 
Left Value Ref
Left Value Ref

* void wrapper(T&& u)의 u 값이 좌측값이라는 것은 불변의 진리이기 때문에 g(u)에서 항상 좌측값 레퍼런스가 호출된다.

```cpp
template <typename T>
void g(T&& arg)
{
    cout<<"Right Value Ref"<<endl;
}

template <typename T>
void g(T& arg)
{
    cout<<"Left Value Ref"<<endl;
}

template <typename T>
void wrapper(T&& u) {
  g(std::move(u));
}

class A
{
public:
    int memA;
};

int main()
{
   A valA;
   wrapper(valA);//...1
   wrapper(A());//...2
   return 0;
}
```
출력 
Right Value Ref
Right Value Ref

* 위의 예제의 경우 1은 좌측값 2는 우측값으로 wrapper에 인자를 전달하고 있다.
* void wrapper(T&& u)의 u 값이 좌측값이라는 것은 불변의 진리이기 때문에 g(std::move(u))에서 항상 우측값 레퍼런스가 호출출된다.

```cpp
template <typename T>
void g(T&& arg)
{
    cout<<"Right Value Ref"<<endl;
}

template <typename T>
void g(T& arg)
{
    cout<<"Left Value Ref"<<endl;
}

template <typename T>
void wrapper(T&& u) {
  g(std::forward<T>(u));
}

class A
{
public:
    int memA;
};

int main()
{
   A valA;
   wrapper(valA);//...1
   wrapper(A());//...2
   return 0;
}
```
출력 
Left Value Ref
Right Value Ref

* 위의 문제를 해결하기위해 좌측값 전달은 좌측값으로 우측값 전달은 우측값을 인자로 받는 레퍼런스 함수를 호출하는 방법은 위와 같다.
* std::forward<T>를 사용하여 인자를 넘긴다.
