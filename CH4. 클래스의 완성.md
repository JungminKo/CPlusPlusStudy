### 04-1. 정보은닉(Information Hiding)

#### 멤버변수가 public으로 선언되었을 때 발생할 수 있는 문제점
1. 점의 좌표가 0이상 100이하가 아니어도 됨
2. 직사각형을 의미하는 Rectangle 객체의 좌 상단 좌표값이 우 하단 좌표값보다 크다.
=> 
- 제한된 방법으로의 접근만 허용을 해서 잘못된 값이 저장되지 않도록 도와야 함
- 실수를 했을 때 실수가 쉽게 발견되도록 해야 함
```C++
#include <iostream>
using namespace std;

class Point
{
public:
	int x; //x좌표의 범위는 0이상 100이하
	int y; //y좌표의 범위는 0이상 100이하
};

class Rectangle
{
public:
	Point upLeft;  // 클래스의 멤버로 객체를 둘 수 있음
	Point lowRight;

public:
	void ShowRecInfo()
	{
		cout << "좌 상단: " << '[' << upLeft.x << ", ";
		cout << upLeft.y << ']' << endl;
		cout << "우 하단: " << '[' << lowRight.x << ", ";
		cout << lowRight.y << ']' << endl;
	}
};

int main(void)
{
	Point pos1 = { -2, 4 };  //멤버변수가 public -> 구조체 변수를 초기화하듯이 초기화 가능
	Point pos2 = { 5, 9 };
	Rectangle rec = { pos2, pos1 };
	rec.ShowRecInfo();

	return 0;
}
```

#### 바뀐 코드
##### Point.h
```C++
#ifndef __POINT_H__
#define __POINT_H__

class Point
{
private:
	int x; //x좌표의 범위는 0이상 100이하
	int y; //y좌표의 범위는 0이상 100이하

public:
	bool InitMembers(int xpos, int ypos);
	int GetX() const;
	int GetY() const;
	bool SetX(int xpos);
	bool SetY(int ypos);
};
#endif // !__POINT_H__
```
##### Point.cpp
```C++
#include <iostream>
#include "Point.h"
using namespace std;

bool Point::InitMembers(int xpos, int ypos)
{
	if (xpos < 0 || ypos < 0) 
	{
		cout << "벗어난 범위의 값 전달" << endl;
		return false;
	}

	x = xpos;
	y = ypos;
	return true;
}

int Point::GetX() const //const 함수!
{
	return x;
}

int Point::GetY() const //const 함수!
{
	return y;
}

bool Point::SetX(int xpos)
{
	if (0 > xpos || xpos > 100)
	{
		cout << "벗어난 범위의 값 전달" << endl;
		return false;
	}
	x = xpos;
	return true;
}

bool Point::SetY(int ypos)
{
	if (0 > ypos || ypos > 100)
	{
		cout << "벗어난 범위의 값 전달" << endl;
		return false;
	}
	y = ypos;
	return true;
}
```
##### Rectangle.h
```C++
#ifndef  __RECTANGLE_H__
#define __RECTANGLE_H__

#include "Point.h"
class Rectangle
{
public:
	Point upLeft;  // 클래스의 멤버로 객체를 둘 수 있음
	Point lowRight;

public:
	bool InitMembers(const Point &ul, const Point &lr);
	void ShowRecInfo() const;
};
#endif // ! __RECTANGLE_H__
```
##### Rectangle.cpp
```C++
#include <iostream>
#include "Rectangle.h"
using namespace std;

bool Rectangle::InitMembers(const Point &ul, const Point &lr)
{
	if (ul.GetX() > lr.GetX() || ul.GetY() > lr.GetY())
	{
		cout << "잘못된 위치정보 전달" << endl;
		return false;
	}
	upLeft = ul;
	lowRight = lr;
	return true;
}

void Rectangle::ShowRecInfo() const
{
	cout << "좌 상단: " << '[' << upLeft.GetX() << ", ";
	cout << upLeft.GetY() << ']' << endl;
	cout << "우 하단: " << '[' << lowRight.GetX() << ", ";
	cout << lowRight.GetY() << ']' << endl;
}
```
##### RectangleFaultFind.cpp
```C++
#include <iostream>
#include "Point.h"
#include "Rectangle.h"
using namespace std;

int main(void)
{
	Point pos1;
	if (!pos1.InitMembers(-2, 4))
		cout << "초기화 실패" << endl;
	if (!pos1.InitMembers(2,4))
		cout << "초기화 실패" << endl;

	Point pos2;
	if (!pos2.InitMembers(5, 9))
		cout << "초기화 실패" << endl;

	Rectangle rec;
	if (!rec.InitMembers(pos2, pos1))
		cout << "직사각형 초기화 실패" << endl;

	if (!rec.InitMembers(pos1, pos2))
		cout << "직사각형 초기화 실패" << endl;

	rec.ShowRecInfo();
	return 0;
}
```

#### const함수
- 의미 : 이 함수내에서는 멤버변수에 저장된 값을 변경하지 않겠다!
- 특징 : const 함수 내에서는 const가 아닌 함수의 호출이 제한됨

```c++
class SimpleClass
{
private:
	int num;
public:
	void InitNum(int n)
	{
		num=n;
	}
	int GetNum()  // const 선언이 추가되어야 아래의 컴파일 에러 소멸
	{
		return num;
	}
	void ShowNum() const
	{
		cout<<GetNum()<<endl;  // 컴파일 에러 발생. ShowNum은 const인데 GetNum은 const가 아니기 때문.
	}
};
```

```C++
class EasyClass
{
private:
	int num;
public:
	void InitNum(int n)
	{
		num=n;
	}
	int GetNum()  // const 선언이 추가되어야 아래의 컴파일 에러 소멸
	{
		return num;
	}
};

class LiveClass
{
private:
	int num;
public:
	void InitNum(const EasyClass &easy)
	{
		num=easy.GetNum();  // 컴파일 에러 발생
	}
};
```
### 04-2. 캡슐화(Encapsulation)

#### 캡슐화가 안된 코드 예시
```C++
#include <iostream>
using namespace std;

class SinivelCap //콧물 처치용 캡슐
{
public:
	void Take() const
	{
		cout << "콧물이 싹~ 납니다." << endl;
	}
};

class SneezeCap //재채기 처치용 캡슐
{
public:
	void Take() const
	{
		cout << "재채기가 멎습니다." << endl;
	}
};

class SnuffleCap //코막힘 처치용 캡슐
{
public:
	void Take() const
	{
		cout << "코가 뻥 뚫립니다." << endl;
	}
};

class ColdPatient 
{
public:
	void TakeSinivelCap(const SinivelCap &cap) const
	{
		cap.Take();
	}
	void TakeSneezeCap(const SneezeCap &cap) const
	{
		cap.Take();
	}
	void TakeSnuffleCap(const SnuffleCap &cap) const
	{
		cap.Take();
	}
};

int main(void)
{
	SinivelCap scap;
	SneezeCap zcap;
	SnuffleCap ncap;

	ColdPatient snufferer;
	snufferer.TakeSinivelCap(scap);
	snufferer.TakeSneezeCap(zcap);
	snufferer.TakeSnuffleCap(ncap);
	return 0;
}
```

#### 캡슐화를 잘 한 결과
```C++
#include <iostream>
using namespace std;

class SinivelCap //콧물 처치용 캡슐
{
public:
	void Take() const
	{
		cout << "콧물이 싹~ 납니다." << endl;
	}
};

class SneezeCap //재채기 처치용 캡슐
{
public:
	void Take() const
	{
		cout << "재채기가 멎습니다." << endl;
	}
};

class SnuffleCap //코막힘 처치용 캡슐
{
public:
	void Take() const
	{
		cout << "코가 뻥 뚫립니다." << endl;
	}
};

class CONTAC600
{
private:
	SinivelCap sin;
	SneezeCap sne;
	SnuffleCap snu;

public:
	void Take() const
	{
		sin.Take();
		sne.Take();
		snu.Take();
	}
};

class ColdPatient
{
public:
	void TakeCONTAC600(const CONTAC600 &cap) const
	{
		cap.Take();
	}
};

int main(void)
{
	CONTAC600 cap;
	ColdPatient sufferer;
	sufferer.TakeCONTAC600(cap);
	return 0;
}
```
### 04-3. 생성자(Constructor)와 소멸자(Destructor)
#### 생성자의 이해
- 클래스 이름=함수이름
- 반환형선언X
- 객체 생성시 딱 한번만 호출
```c++
#include <iostream>
using namespace std;

class SimpleClass
{
private:
	int num1;
	int num2;
public:
	SimpleClass() // 생성자(constructor)
	{
		num1 = 0;
		num2 = 0;
	}
	SimpleClass(int n) // 생성자(constructor)
	{
		num1 = n;
		num2 = 0;
	}
	SimpleClass(int n1, int n2) // 생성자(constructor)
	{
		num1 = n1;
		num2 = n2;
	}
	/*
	SimpleClass(int n1=0, int n2=0) // 위에꺼 3개를 하나로 통합가능
	{
		num1 = n1;
		num2 = n2;
	}
	*/
	void ShowData() const
	{
		cout << num1 << ' ' << num2 << endl;
	}
};

int main(void)
{
	SimpleClass sc1;
	sc1.ShowData();

	SimpleClass sc2(100);
	sc2.ShowData();

	SimpleClass sc3(100, 200);
	sc3.ShowData();

	return 0;
}
```
```c++
#include <iostream>
using namespace std;

class SimpleClass
{
private:
	int num1;
	int num2;
public:
	SimpleClass(int n1=0, int n2=0) // 위에꺼 3개를 하나로 통합가능
	{
		num1 = n1;
		num2 = n2;
	}
	void ShowData() const
	{
		cout << num1 << ' ' << num2 << endl;
	}
};

int main(void)
{
	SimpleClass sc1();
	SimpleClass mysc = sc1();
	mysc.ShowData();
	return 0;
}

SimpleClass sc1()
{
	SimpleClass sc(20, 30);
	return sc;
}
```

``` C++
#include <iostream>
using namespace std;

class FruitSeller
{
private:
	int APPLE_PRICE; //사과의 가격 
	int numOfApples;
	int myMoney;
public:
	FruitSeller(int price, int num, int money)
	{
		APPLE_PRICE = price;
		numOfApples = num;
		myMoney = money;
	}

	int SaleApples(int money)
	{
		int num = money / APPLE_PRICE;
		numOfApples -= num;
		myMoney += money;
		return num; // 판매한 과일의 수를 반환
	}

	void ShowSalesResult()
	{
		cout << "남은 사과: " << numOfApples << endl;
		cout << "판매 수익: " << myMoney << endl;
	}
};

class FruitBuyer
{
	int myMoney; //private:
	int numOfApples; //private:
public:
	FruitBuyer(int money)
	{
		myMoney = money;
		numOfApples = 0; //사과구매 이전이므로
	}

	void BuyApples(FruitSeller &seller, int money)
	{
		numOfApples += seller.SaleApples(money);
		myMoney -= money;
	}

	void ShowBuyResult()
	{
		cout << "현재 잔액: " << myMoney << endl;
		cout << "사과 개수: " << numOfApples << endl;
	}
};

int main(void)
{
	FruitSeller seller(1000, 20, 0);
	FruitBuyer buyer(5000);
	buyer.BuyApples(seller, 2000);  //과일의 구매

	cout << "과일 판매자의 현황" << endl;
	seller.ShowSalesResult();
	cout << "과일 구매자의 현황" << endl;
	buyer.ShowBuyResult();
	return 0;
}
```
#### 멤버 이니셜라이저
##### Point.h
```C++
#ifndef __POINT_H__
#define __POINT_H__

class Point
{
private:
	int x; //x좌표의 범위는 0이상 100이하
	int y; //y좌표의 범위는 0이상 100이하

public:
	Point(const int &xpos, const int &ypos);
	int GetX() const;
	int GetY() const;
	bool SetX(int xpos);
	bool SetY(int ypos);
};
#endif // !__POINT_H__
```
##### Point.cpp
```C++
#include <iostream>
#include "Point.h"
using namespace std;

Point::Point(const int &xpos, const int &ypos)
{
	x = xpos;
	y = ypos;

}

int Point::GetX() const //const 함수!
{
	return x;
}

int Point::GetY() const //const 함수!
{
	return y;
}

bool Point::SetX(int xpos)
{
	if (0 > xpos || xpos > 100)
	{
		cout << "벗어난 범위의 값 전달" << endl;
		return false;
	}
	x = xpos;
	return true;
}

bool Point::SetY(int ypos)
{
	if (0 > ypos || ypos > 100)
	{
		cout << "벗어난 범위의 값 전달" << endl;
		return false;
	}
	y = ypos;
	return true;
}
```
##### Rectangle.h
```C++
#ifndef  __RECTANGLE_H__
#define __RECTANGLE_H__

#include "Point.h"
class Rectangle
{
public:
	Point upLeft;  // 클래스의 멤버로 객체를 둘 수 있음
	Point lowRight;

public:
	Rectangle(const int &x1, const int &y1, const int &x2, const int &y2);
	void ShowRecInfo() const;
};
#endif // ! __RECTANGLE_H__
```
##### Rectangle.cpp
```C++
#include <iostream>
#include "Rectangle.h"
using namespace std;

Rectangle::Rectangle(const int &x1, const int &y1, const int &x2, const int &y2)
			:upLeft(x1, y1), lowRight(x2, y2) // 멤버이니셜라이져
{
	//empty
}

void Rectangle::ShowRecInfo() const
{
	cout << "좌 상단: " << '[' << upLeft.GetX() << ", ";
	cout << upLeft.GetY() << ']' << endl;
	cout << "우 하단: " << '[' << lowRight.GetX() << ", ";
	cout << lowRight.GetY() << ']' << endl;
}
```
##### RectangleFaultFind.cpp
```C++
#include <iostream>
#include "Point.h"
#include "Rectangle.h"
using namespace std;

int main(void)
{
	Rectangle rec(1, 1, 5, 5);
	rec.ShowRecInfo();
	return 0;
}
```
- const 멤버변수도 이니셜라이저를 이용하여 초기화 가능
```C++
class FruitSeller
{
private:
	const int APPLE_PRICE; //사과의 가격 
	int numOfApples;
	int myMoney;
public:
	FruitSeller(int price, int num, int money)
		:APPLE_PRICE(price), numOfApples(num), myMoney(money)
	{
	}
	...
};
```
- 멤버변수로 참조자 선언가능
```C++
#include <iostream>
using namespace std;

class AAA
{
public:
	AAA()
	{
		cout << "empty object" << endl;
	}
	void ShowYourName()
	{
		cout << "I'm class AAA" << endl;
	}
};

class BBB
{
private:
	AAA &ref;
	const int &num;
public:
	BBB(AAA &r, const int &n)
		:ref(r), num(n)
	{
		//empty constructor body
	}
	void ShowYourName()
	{
		ref.ShowYourName();
		cout << "and" << endl;
		cout << "I ref num " << num << endl;
	}
};

int main(void)
{
	AAA obj1;
	BBB obj2(obj1, 20);
	obj2.ShowYourName();
	return 0;
}
```
#### 디폴트 생성자
- 객체가 되기 위해서는 반드시 하나의 생성자가 호출되어야 하기 떄문에 생성자를 정의하지 않더라도 자동으로 삽입됨.
#### private 생성자
```C++
#include <iostream>
using namespace std;

class AAA
{
private:
	int num;
public:
	AAA() :num(0) {}
	AAA& CreateInitObj(int n) const
	{
		AAA *ptr = new AAA(n);
		return *ptr;
	}
	void ShowNum() const
	{
		cout << num << endl;
	}
private:
	AAA(int n) :num(n) {}
};

int main(void)
{
	AAA base;
	base.ShowNum();

	AAA &obj1 = base.CreateInitObj(3);  // 힙 영역에 생성된 객체(new로)를 참조의 형태로 반환!
	obj1.ShowNum();

	AAA &obj2 = base.CreateInitObj(12);
	obj2.ShowNum();

	delete &obj1;
	delete &obj2;
	return 0;
}
```
#### 소멸자
```C++
#include <iostream>
#include <cstring>
using namespace std;

class Person
{
private:
	char *name;
	int age;
public:
	Person(char const *myname, int myage)
	{
		int len = strlen(myname) + 1;
		name = new char[len];
		strcpy(name, myname);
		age = myage;
	}
	void ShowPersonInfo() const
	{
		cout << "이름: " << name << endl;
		cout << "나이: " << age << endl;
	}
	~Person()
	{
		delete []name;
		cout << "called destrcutor!" << endl;
	}
};

int main(void)
{
	Person man1("Lee dong woo", 29);
	Person man2("Jang dong gun", 41);
	man1.ShowPersonInfo();
	man2.ShowPersonInfo();
	return 0;
}
```
### 04-4. 클래스와 배열 그리고 this 포인터

#### 클래스
