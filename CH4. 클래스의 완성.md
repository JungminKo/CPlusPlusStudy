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

### 04-4. 클래스와 배열 그리고 this 포인터

