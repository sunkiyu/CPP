# Advanced Template 고급 템플릿

```cpp
//템플릿 구조체 선언
//이 템플릿은 여러개의 인자를 받을 수 있다.
template<typename ...T>
struct HeadTailTest{};

//명시적 특수화 explicit specialization
template <>
struct HeadTailTest<int, char>
{
   HeadTailTest()
   {
       cout<<""<<endl;
   }
};

//부분 특수화 partial spectialization
template <typename T>
struct HeadTailTest<T, char>
{
   HeadTailTest()
   {
       T t;
       cout<<""<<endl;
   }
};

//부분 특수화
template<typename T, typename U>
struct HeadTailTest<T,U>
{
    using Head = T;
    using Tail = U;
};

//부분 특수화
template <class T, class ...U>
struct HeadTailTest<T,U...>
{
    using Head = T;
    using Tail = HeadTailTest<U...>;
};
/////////////////////////////////////////////////////////////////
  //위의 구조체 템플릿 선언을 T,U 타입 두가지를 받도록 변형해보았다.
  //앞으로 이 구조체 템플릿은 최소 두 가지 타입을 받겠다는 선언이다.
  template<typename T, typename U>         ......(1) 템플릿 구조체 선언
struct HeadTailTest
{
    using Head = T;
    //using Tail = U;

    HeadTailTest(){
        cout<< "c"<<endl;
    };
};

template<typename T,typename U>
struct HeadTailTest<T,U>            //컴파일 에러 -> 템플릿 구조체 선언이 T,U 2가지 인자를 받는데 T,U 자체 그대로 사용하는 것은 특수화로 볼 수 없다.
{                                   //기본 템플릿 인자 그래로를 특수화 할 수 없다.
    using Head = T;
    HeadTailTest(){
        cout<< "c"<<endl;
    };

};
//How to Use
HeadTailTest<A, B>::Head who;//A
HeadTailTest<A, B>::Tail who2;//B

HeadTailTest<A, HeadTailTest<B, C>>::Head who3; //A
HeadTailTest<A, HeadTailTest<B, C>>::Tail::Head who4;//B
HeadTailTest<A, HeadTailTest<B, C>>::Tail::Tail who5;//C
```
## 여기서 특수화의 개념이 좀 더 명확해 진다. 특수화는 구조체 템플릿의 선언인 기본 틀에서 어떤 타입을 구체적으로 정해줄 경우에만 특수화로 볼 수 있다.
```cpp
 template<typename T>
struct HeadTailTest<T,T>    //기본 선언(T,U)에서 두 파라미터가 모두 같은 경우에 사용한다라고 특수화 OK
{
    using Head = T;
    HeadTailTest(){
        cout<< "c"<<endl;
    };

};

  template<typename T>
struct HeadTailTest<T,char> //마찬가지로 특수화 OK
{
    using Head = T;
    HeadTailTest(){
        cout<< "c"<<endl;
    };

};
```
* 리스트의 Length 
***
```cpp
template <typename ...ARGS>
struct TestStruct;

//이 템플릿 구조체는 템플파라미터로 하나만 받는다.
template<typename T>
struct HeadTailLength
{
    using Head = T;
    HeadTailLength(){
        cout<<"HeadTailLength()"<<endl;
    };
};

template<>
struct HeadTailLength<HeadTailTest<>>
{
    enum {value=0};
};

template <typename T,typename ...U>              //템플릿 구조체 기본 선언에서 파마미터를 하나만 받는다. 특수화 구조체 파마미터로 여러개 전달 할 수는 있다.
struct HeadTailLength<HeadTailTest<T,U...>>      //파라미터를 하나만 받으므로 하나만 받도록 특수화 할 수 있다. (HeadTailTest) 1개
{
    enum {value = 1 + HeadTailLength<HeadTailTest<U...>>::value};      //T,U... 처럼 여러개를 받는 템플릿 구조체면 모두 가능하다. 위의 TestStruct 샘플을 넣어도 OK
};

//How to Use
 HeadTailLength<HeadTailTest<int,int,int>>::value;//3
```
***
* Index to Type
```cpp
//이 템플릿 구조체는 타입 1개와 int 형 인자 1개만 받는다.
template<typename TL, int index>
struct FindType;

//index가 0일 경우 이쪽 FindType 구조체가 맵핑 될것이며
//이때는 Head에 찾고자하는 Type이 맵핑될 것이다.
//Tail은 없거나 HeadTailTest에 따라 뒤의 타입들이 있을 것이다.
template<typename Head, typename... Tail>
struct FindType<HeadTailTest<Head, Tail...>, 0>
{
    using Result = Head;
    FindType()
    {
        cout<<"TypeAt"<<endl;
    };
};

template<typename Head, typename... Tail, int index>
struct FindType<HeadTailTest<Head, Tail...>, index>
{
    using Result = typename FindType<HeadTailTest<Tail...>, index - 1>::Result;
    FindType()
    {
        cout<<"TypeAt"<<endl;
    };
};

//How to Use
using HTL = HeadTailTest<int,double,float>;
FindType<HTL, 1>::Result whoAMI6; // double
```
***
* Type to Index
```cpp
//이 템플릿 구조체는 T,U 두가지 타입만 받을 것이다.
template<typename T,typename U>
struct FindIndex;

template<typename... Tail, typename T>
//기본 템플릿이 T,U 두가지 인자만 받으므로 HeadTailTest<T, Tail...>,T 두가지 인자만 받는다.
//인자 순회 중 첫번째 타입이 T와 일치하는 경우
struct FindIndex<HeadTailTest<T, Tail...>, T>
{
    enum { value = 0 };
};

template<typename T>
//기본 템플릿이 T,U 두가지 인자만 받으므로 HeadTailTest<Head, Tail...>,T 두가지 인자만 받는다.
//인자를 모두 순회했느데도 타입 T를 찾지 못헀다.
struct FindIndex<HeadTailTest<>, T>
{
    enum { value = -1 };
};

template<typename Head, typename... Tail, typename T>
//기본 템플릿이 T,U 두가지 인자만 받으므로 HeadTailTest<Head, Tail...>,T 두가지 인자만 받는다.
struct FindIndex<HeadTailTest<Head, Tail...>, T>
{
private:
//찾았을 때는 temp 1, 못찾으면 -1
    enum { temp = FindIndex<HeadTailTest<Tail...>, T>::value };

public:
//못찾아서 temp가 -1이면 value =-1 찾았으면 temp가 0이므로 0+1이 value가 되고 계속 재귀를 1씩 더해가며 벋어나서 타입 인덱스를
//얻을 수 있다.
    enum { value = (temp == -1) ? -1 : temp + 1 };
};

//How to Use
FindIndex<HTL,double>::value; //1
```
