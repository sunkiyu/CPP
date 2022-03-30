```cpp
const int number = 10; //...1
int const number = 10; //...2
//1과 2는 같은 표현이다.
number = 11; //number은 const 속성이기 때문에 컴파일 에러

//const 변수는 반드시 초기화해야함.
//class 내의 const 멤버변수는 생성자에서 초기화해야함.
//class 내의 const int number; 과 같은 선언은 메모리에 할당된 상태가 아닌 선언형태이기 때문에 가능하며 반드시 생성자에서 초기화해야한다.

int number = 10;
const int* ptr = &number; 
*ptr = 11; //컴파일 에러, 포인터 변수가 가르키는 값에 대해 상수화
num = 11; //OK

int num1 = 1;
int num2 = 2;
int* const ptr = &num1; // ptr을 상수화
ptr = &num2; // 컴파일 에러

//포인터가 가르키는 값, 포인터 자체를 동시에 상수화
const int *const ptr;
//*ptr = 11; ptr = &numb2가 모두 안된다.

int testfunc(void) const; // 컴파일 에러, 클래스 내의 멤버함수만 가능.

class Foo
{
  int num = 1;
  int testfunc(void) const//const의 의미 : 해당 멤버 함수 내에서는 모든 멤버 변수를 상수화 시킨다
  {
    int a = 1;
    a++; // 로컬 변수 수정 가능
    num++; //컴파일 에러
    return num;
  }
};
```
