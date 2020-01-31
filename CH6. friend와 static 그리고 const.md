### 06-1. const와 관련해서 아직 못다한 이야기
#### const 객체와 const 객체의 특성들

- const함수는 const 멤버함수만 호출 가능
```c++
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num;
public:
	SoSimple(int n) :num(n)
	{}
	SoSimple& AddNum(int n)
	{
		num+=n;
		return *this;
	}
	void ShowData() const
	{
		cout << "num: " << num << endl;
	}
};
int main(void)
{
	const SoSimple obj(7);
	//obj.AddNum(20); //const 함수가 아니라서 호출 불가
	obj.ShowData();
	return 0;
}
```
- const의 선언 유무 -> 함수 오버로딩의 조건이 될 수 있음
```C++
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num;
public:
	SoSimple(int n) :num(n)
	{}
	SoSimple& AddNum(int n)
	{
		num+=n;
		return *this;
	}
	void SimpleFunc() 
	{
		cout << "SimpleFunc: " << num << endl;
	}
	void SimpleFunc() const
	{
		cout << "const SimpleFunc: " << num << endl;
	}
};

void YourFunc(const SoSimple &obj)
{
	obj.SimpleFunc();
}

int main(void)
{
	SoSimple obj1(2);
	const SoSimple obj2(7);

	obj1.SimpleFunc();
	obj2.SimpleFunc();

	YourFunc(obj1);
	YourFunc(obj2);
	return 0;
}
```

### 06-2. 클래스와 함수에 대한 friend 
- friend : private 멤버의 접근을 허용하는 선언
- 필요한 상황에서 소극적으로 사용해야함 (ex. 연산자 오버로딩)
```C++
#include <iostream>
#include <cstring>
using namespace std;

class Girl; // 없어도 컴파일 가능. friend class Girl 에서 Girl이 class임을 유추 가능

class Boy
{
private:
	int height;
	friend class Girl;  // Girl 클래스에 대한 friend 선언
public:
	Boy(int len) :height(len) {}
	void ShowYourFriendInfo(Girl &frn);
};

class Girl
{
private:
	char phNum[20];
public:
	Girl(const char * num) 
	{
		strcpy(phNum, num);
	}
	void ShowYourFriendInfo(Boy &frn);
	friend class Boy;
};

void Boy::ShowYourFriendInfo(Girl &frn)
{
	cout << "Her phone number: " << frn.phNum << endl;
}

void Girl::ShowYourFriendInfo(Boy &frn)
{
	cout << "His height: " << frn.height << endl;
}
int main(void)
{
	Boy boy(170);
	Girl girl("010-1234-5678");
	boy.ShowYourFriendInfo(girl);
	girl.ShowYourFriendInfo(boy);
	return 0;
}
```
- 함수의 friend 선언도 가능
```C++
#include <iostream>
using namespace std;

class Point; // PointOP에서 함수의 반환값을 Point로 쓰기 위해서 필요한 문구

class PointOP
{
private:
	int opcnt;
public:
	PointOP() :opcnt(0) {}
	Point PointAdd(const Point&, const Point&);
	Point PointSub(const Point&, const Point&);
	~PointOP()
	{
		cout << "Operation times: " << opcnt << endl;
	}
};

class Point
{
private:
	int x;
	int y;
public:
	Point(const int &xpos, const int &ypos):x(xpos), y(ypos)
	{}
	friend Point PointOP::PointAdd(const Point&, const Point&);
	friend Point PointOP::PointSub(const Point&, const Point&);
	friend void ShowPointPos(const Point&);
};

Point PointOP::PointAdd(const Point& pnt1, const Point& pnt2)
{
	opcnt++;
	return Point(pnt1.x + pnt2.x, pnt1.y + pnt2.y);
}

Point PointOP::PointSub(const Point& pnt1, const Point& pnt2)
{
	opcnt++;
	return Point(pnt1.x - pnt2.x, pnt1.y - pnt2.y);
}

int main(void)
{
	Point pos1(1, 2);
	Point pos2(2, 4);
	PointOP op;

	ShowPointPos(op.PointAdd(pos1, pos2));
	ShowPointPos(op.PointSub(pos2, pos1));

	return 0;
}

void ShowPointPos(const Point& pos)
{
	cout << "x: " << pos.x << ", ";
	cout << "y: " << pos.y << endl;
}
```

### 06-3. C++에서의 static
#### 전역변수가 필요한 상황
```C++
#include <iostream>
using namespace std;

int simObjCnt = 0;
int cmxObjCnt = 0;

class SoSimple
{
public:
	SoSimple()
	{
		simObjCnt++;
		cout << simObjCnt << "번째 SoSimple 객체" << endl;
	}
};

class SoComplex
{
public:
	SoComplex()
	{
		cmxObjCnt++;
		cout << cmxObjCnt << "번째 SoComplex 객체" << endl;
	}
	SoComplex(SoComplex &copy)
	{
		cmxObjCnt++;
		cout << cmxObjCnt << "번째 SoComplex 객체" << endl;
	}
};

int main(void)
{
	SoSimple sim1;
	SoSimple sim2;

	SoComplex com1;
	SoComplex com2 = com1;
	SoComplex();

	return 0;
}
```
- 단점 : 전역변수이므로 외부에서 접근가능

#### static 멤버변수(클래스 변수)
- 클래스당 하나씩 생성되기 때문에 클래스 변수라고도 함

```c++
#include <iostream>
using namespace std;


class SoSimple
{
private:
	static int simObjCnt;
public:
	SoSimple()
	{
		simObjCnt++;
		cout << simObjCnt << "번째 SoSimple 객체" << endl;
	}
};
/*
simobjcnt는 이미 메모리 공간에 할당이 이루어진 변수이기 때문에 
이런식으로 적어서 메모리 공간에 저장될 때 0으로 초기화하라고 해야함
*/
int SoSimple::simObjCnt = 0; 

class SoComplex
{
private:
	static int cmxObjCnt;
public:
	SoComplex()
	{
		cmxObjCnt++;
		cout << cmxObjCnt << "번째 SoComplex 객체" << endl;
	}
	SoComplex(SoComplex &copy)
	{
		cmxObjCnt++;
		cout << cmxObjCnt << "번째 SoComplex 객체" << endl;
	}
};
int SoComplex::cmxObjCnt = 0;

int main(void)
{
	SoSimple sim1;
	SoSimple sim2;

	SoComplex com1;
	SoComplex com2 = com1;
	SoComplex();

	return 0;
}
```

#### static 멤버변수의 또 다른 접근방법
- `cout<<SoSimple::simObjCnt;`, `cout<<sim1.simObjCnt;`, `cout<<sim2.simObjCnt;` 모두 가능
- 하지만 두 세번째 방법은 sim1, sim2의 멤버변수에 접근하는 것처럼 보이므로 비추
```C++
#include <iostream>
using namespace std;


class SoSimple
{
public:
	static int simObjCnt;
public:  // 불필요하지만 변수와 함수의 구분을 목적으로 삽입하기도 함
	SoSimple()
	{
		simObjCnt++;
	}
};
int SoSimple::simObjCnt = 0; 

int main(void)
{
	//SoSimple 객체를 하나도 생성하지 않은 상태에서도 접근 가능
	cout << SoSimple::simObjCnt << "번째 SoSimple 객체" << endl;
	SoSimple sim1;
	SoSimple sim2;

	cout << SoSimple::simObjCnt << "번째 SoSimple 객체" << endl;
	cout << sim1.simObjCnt << "번째 SoSimple 객체" << endl;
	cout << sim2.simObjCnt << "번째 SoSimple 객체" << endl;
	return 0;
}

```
#### static 멤버함수
- 선언된 클래스의 모든 객체가 공유
- public으로 선언되면 클래스의 이름을 이용해서 호출가능
- 객체의 멤버로 존재하는 것은 아님
- static 멤버함수내에서는 static 멤버변수와 static 멤버함수만 호출 가능
```C++
#include <iostream>
using namespace std;


class CountryArea 
{
public:
	const static int RUSSIA = 1707540;
	const static int CANADA = 998467;
	const static int CHINA = 957290;
	const static int SOUTH_KOREA = 9922;
};

int main(void)
{
	cout << "러시아 면적: " << CountryArea::RUSSIA << "km" << endl;
	cout << "캐나다 면적: " << CountryArea::CANADA << "km" << endl;
	cout << "중국 면적: " << CountryArea::CHINA << "km" << endl;
	cout << "한국 면적: " << CountryArea::SOUTH_KOREA << "km" << endl;

	return 0;
}
```

#### 키워드 mutable
- const 함수 내에서 값의 변경을 예외적으로 허용
- but 최대한 사용을 자제해야함
```C++
#include <iostream>
using namespace std;


class SoSimple 
{
private:
	int num1;
	mutable int num2; // const 함수에 대해 예외를 둔다!
public:
	SoSimple(int n1, int n2) : num1(n1), num2(n2)
	{}
	void ShowSimpleData() const
	{
		cout << num1 << ", " << num2 << endl;
	}
	void CopyToNum2() const
	{
		num2 = num1;
	}
};

int main(void)
{
	SoSimple sm(1, 2);
	sm.ShowSimpleData();
	sm.CopyToNum2();
	sm.ShowSimpleData();
	return 0;
}
```
