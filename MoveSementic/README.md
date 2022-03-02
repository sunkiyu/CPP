## 이동시멘틱
```cpp
class A
{
public:
    A(){cout<<"Constructor"<<endl;};
    virtual ~A(){cout<<"Destructor"<<endl;};
public:
    int m_A;
    char * m_cA = nullptr;

};

int main(){
    A ia;
   ia.m_A=1000;
   ia.m_cA = new char[100];
   sprintf(ia.m_cA,"%s","TEST");

   //복사 생성자 호출(얕은복사)
   //ia.m_cA와 ia2.m_cA가 같은 힙 메모리를 가르킨다.
   A ia2(ia);
   //이동 생성자 호출
   A ia3(move(ia));
}
```
* 복사 생성자의 얕은 복사 문제와 이동 생성자를 구현한다.
```cpp
class A
{
public:
    A(){cout<<"Constructor"<<endl;};
    A(A& param)
    {
        cout<< "Copy Constructor" <<endl;
        m_cA = new char[100];
        m_A = param.m_A;
        memcpy(m_cA,param.m_cA,strlen(m_cA)+1);
    };
    A(A&& param)
    {
        cout<<"Move Constructor"<<endl;
        m_A = param.m_A;
        param.m_A = 0;
        m_cA = param.m_cA;
        param.m_cA = nullptr;
    }
    virtual ~A(){cout<<"Destructor"<<endl;};
public:
    int m_A;
    char * m_cA = nullptr;
};

int main(){
    A ia;
   ia.m_A=1000;
   ia.m_cA = new char[100];
   sprintf(ia.m_cA,"%s","TEST");

   //복사 생성자 호출(깊은 복사)
   A ia2(ia);
   //이동 생성자 호출(소유권 이전)
   //ia.m_cA가 가르키는 메모리가 nullptr로 되었기 때문에 ia 객체는 사용하지 않는것이 좋다.
   //복사 생성자 호출시 복사되는 비용없이 소유권 이전만 시키기에 복사생성자보다 비용이 적다.(빠르다)
   //우측값 전달이나 우측값 대입연산에 사용할 수 있다.
   A ia3(move(ia));
}
```
