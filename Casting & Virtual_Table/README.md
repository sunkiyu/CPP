# Virtual Table

* 가상함수를 갖는 클래스는 인스턴스 생성시 가상함수 테이블을 생성하며 가상함수 테이블을 가상함수 포인터를 모아둔 배열이다.
* 가상함수 추가시 가상함수 테이블을 가르키는 포인터를 멤버변수로 추가한다.
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
	
	A staticA = static_cast<A>(memBTest);//...(1)
  	staticA = static_cast<A>(memBTest);//...(2)
   	A *castA = static_cast<A*>(&memBTest);//...(3)
  	B *castB = dynamic_cast<B*>(castA);//...(4)
  	B *downcastB = dynamic_cast<B*>(&memATest);//(5)
   	if(downcastB==nullptr)
       		cout<<"nullptr"<<endl;

   	castA->printString();//...(6)
   	castA->printString2();//...(7)
   
	return 0;
}
```
![image](https://user-images.githubusercontent.com/68372094/161896822-9420cb0e-64c3-470c-979c-f72cc1c5e86d.png)

* A를 인스턴스로 생성하였을 경우 가상함수테이블에 A에 선언된 virtual 함수 printString2,printString3 가 존재한다.
![image](https://user-images.githubusercontent.com/68372094/161897063-b9ed5b81-7697-4910-9e97-ca29543fae66.png)
* B를 인스턴스로 생성하였을 경우 가상함수테이블에 B에 선언된 virtual 함수 printString2와 A에 선언된 printString3가 존재한다.
* 클래스 B에서 A의 가상함수테이블을 상속받았으나 B에서 printString2 가상함수를 오버라이딩하였기 때문에 가상함수 테이블의 printString2 함수의 주소는 B의 것을 가르키게 된다.
* 
* (1) RTTI(늦은 바인딩)이 적용되지 않는다. A staticA; 에서 A의 생성자가 호출되고 A staticA = static_cast<A>(memBTest) 바로 초기화하여 복사 생성자가 호출된다.
      가상함수 테이블에도 마찬가지로 A의 가상함수만 들어있다.
	
* (2) 객체 생성 직후 = 대인연산자를 사용하였기 때문에 복사 대입 연산자가 호출된다.
	
* (3) RTTI가 적용된다. 실제 생성한 객체가 B이므로 castA 포인터는 B의 오버라이딩된 printString2 함수를 갖는다.
	
* (4) 다시 B로 복원하였다.
	
* (5) dynamic_cast는 상속관계의 캐스팅이 잘못되었을 경우 nullptr을 리턴한다. 실제 생성된 객체가 A인데 B로 다운캐스팅을 시도하므로 nullptr을 뱉는다.
     B->A로의 업캐스팅은 유효하다.
     실제 생성된 객체가 B일 경우 다운캐스팅이 가능하다. B->A->B 인 경우  
	
* (6) A의 printString()이 호출된다.
	
* (7) 오버라이딩된 B의 printString2()가 호출된다. RTTI(늦은 바인딩)의 영향을 받는다.
	
