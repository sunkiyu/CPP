# Virtual Table

```cpp
class A
{
public:

    int memA=0;
    A(){cout<<"A Constructor"<<endl;};

    A(A& cp)
    {
        memA=10;
        cout<< "A cpConstructor&"<<endl;
    }

    A(A&& cp)
    {
        cout<< "A cpConstructor"<<endl;
    }

    void operator=(A&& cp)
    {
        cout<< "operator &&"<<endl;
    }

    void operator=(A& cp)
    {
        cout<< "operator &"<<endl;
    }
    void printString(){cout<< "A Called"<<endl;}
    virtual void printString2(){cout<< "A Called"<<endl;}
    virtual void printString3(){cout<< "A Called"<<endl;}
};

class B:public A
{
public:
    int memb=20;
    B(){cout<<"B Constructor"<<endl;};
    B(B&& cp)
    {
        cout<< "A cpConstructor"<<endl;
        memb=10;
    }

    void operator=(B&& cp)
    {
        cout<< "operator &&"<<endl;
    }

    void operator=(B& cp)
    {
        cout<< "operator &"<<endl;
    }
    void printString(){cout<< "B Called"<<endl;}
    virtual void printString2() override{cout<< "B Called"<<endl;}
};

int main()
{
 	 A memATest;
   B memBTest;
   
	 return 0;
}
```
![image](https://user-images.githubusercontent.com/68372094/161896822-9420cb0e-64c3-470c-979c-f72cc1c5e86d.png)

* A를 인스턴스로 생성하였을 경우 가상함수테이블에 A에 선언된 virtual 함수 printString2,printString3 가 존재한다.
![image](https://user-images.githubusercontent.com/68372094/161897063-b9ed5b81-7697-4910-9e97-ca29543fae66.png)
* B를 인스턴스로 생성하였을 경우 가상함수테이블에 B에 선언된 virtual 함수 printString2와 A에 선언된 printString3가 존재한다.
* 클래스 B에서 A의 가상함수테이블을 상속받았으나 B에서 printString2 가상함수를 오버라이딩하였기 때문에 가상함수 테이블의 printString2 함수의 주소는 B의 것을 가르키게 된다.
