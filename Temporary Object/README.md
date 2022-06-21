# 임시 객체

```cpp
class TmpInstance
{
public:
	TmpInstance() { cout << "Constructor" << endl; };
	TmpInstance(const TmpInstance& tmp) { cout << "CP Constructor" <<this<< endl; };
	TmpInstance(TmpInstance&& tmp) noexcept { cout << "MV Constructor" <<this<< endl; } ;
	TmpInstance(string name) :m_name(name) { cout << "Constructor" <<this<< endl; };
	~TmpInstance() { cout << "Destructor" << this<< endl; };
	void printName() { cout << m_name << endl; }

private:
	string m_name;
};

//void g(TmpInstance a) { std::cout << "값 호출" << std::endl; }
void g(TmpInstance& a) { std::cout << "좌측값 레퍼런스 호출" << std::endl; }
void g(const TmpInstance& a) { std::cout << "좌측값 상수 레퍼런스 호출" << std::endl; }
void g(TmpInstance&& a) {
	std::cout << "우측값 레퍼런스 호출" << &a << std::endl;
}

vector<TmpInstance> vec;

int main()
{
	const TmpInstance ti("zz");
  //LValue를 전달하게 되면 복사 생성자가 호출된다. 
  g(ti);
  //RValue를 전달하게 되면 이동 생성자가 호출된다.
  //임시객체 TmpInstance("hi")가 생성되었다 소멸된다. 
	g(TmpInstance("hi"));

	return true;
}
```
