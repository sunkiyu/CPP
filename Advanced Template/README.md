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
