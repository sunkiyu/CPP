```cpp
#pragma pack(1)
struct PKT_SK
{
	PKT_SK(int aa, char bb, double cc, char * pData)
	{
		a = aa;
		b = bb;
		c = cc;
		memcpy(data, pData, strlen(pData) + 1);
	}
	int a;
	char b;
	double c;
	BYTE data[100] = { 0, };
};
#pragma pack()

template<typename T, typename M>
class StructIterator
{
public:
	StructIterator() {};
	StructIterator(T& list, int idx):pList(list), curPos(idx) {};
	~StructIterator() {};

//Range Based For 지원
	M& operator*() { return *reinterpret_cast<M*>(pList[curPos]); };
	bool operator !=(StructIterator& other) { return other.curPos != curPos; };
	StructIterator& operator++() { curPos++; return *this; };
	StructIterator& operator--() { curPos--; return *this; };

private:
	T& pList;
	int curPos = 0;
};

template<typename T>
class StructList
{
public:
	StructList() {};
	StructList(T* data, int count):m_Data(data), m_Count(count) {};
	~StructList() {};

	T* operator[] (int idx)
	{
		return &m_Data[idx];
	};

	StructIterator<StructList<T>,T> begin() { return StructIterator<StructList<T>,T>(*this,0); };
	StructIterator<StructList<T>, T> end() { return StructIterator<StructList<T>, T>(*this,m_Count); };

private:
	T* m_Data = nullptr;
	int m_Count = 0;
};

template<typename T>
class StructWriter
{
public:
	StructWriter(BYTE* buff, int allocSize) : curHead(buff), totalSize(allocSize), usedSize(0){};
	StructWriter() {};
	~StructWriter() {};

	int freeSize() { return totalSize - usedSize; };

	StructWriter& operator << (T& data)
	{
		if (freeSize() < sizeof(T))
		{
			usedSize = 0;
		}
		*reinterpret_cast<PKT_SK*>(&curHead[usedSize]) = data;
		usedSize += sizeof(T);
		return *this;
	}

private:
	BYTE* curHead = nullptr;
	int totalSize = 0;
	int usedSize = 0;
};

int main()
{
	BYTE serialBuffer[4096] = { 0, };
	PKT_SK s1(1,'A',1.1, const_cast<char*>("Data1"));
	PKT_SK s2(2,'B',1.2, const_cast<char*>("Data2"));
	PKT_SK s3(3,'C',1.3, const_cast<char*>("Data3"));

	StructWriter<PKT_SK> sw(serialBuffer, sizeof(serialBuffer));
	sw << s1 << s2 << s3;

	StructList<PKT_SK> sl(reinterpret_cast<PKT_SK*>(serialBuffer), 3);

	PKT_SK test = *((PKT_SK*)(sl[1]));
	cout << test.a<< " " << test.b << " "<< test.c <<" "<< test.data << endl;

	cout << (*(sl.begin())).b<<endl;
	cout << (*(--sl.end())).b << endl;

//Range Based For 문을 지원하기 위해 operator을 오버로딩한다.
	for (auto member : sl)
	{
		cout << member.a << " " << member.b << " " << member.c << " " << member.data << endl;
	}
  
  return 0;
}
```
