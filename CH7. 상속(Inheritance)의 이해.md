### 07-1. 상속에 들어가기에 앞서
```C++
#include <iostream>
#include <cstring>
using namespace std;


class PermanentWorker  //이름과 급여정보를 저장하는 클래스
{
private:
	char name[100];
	int salary; // 매달 지불해야 하는 급여액
public:
	PermanentWorker(const char* name, int money)
		:salary(money)
	{
		strcpy(this->name, name);
	}
	int GetPay() const
	{
		return salary;
	}
	void ShowSalaryInfo() const
	{
		cout << "name: " << name << endl;
		cout << "salary: " << GetPay() << endl << endl;
	}
};

class EmployeeHandler // PermanentWorker의 객체를 저장 및 관리하기 위한 클래스
{
private:
	PermanentWorker* empList[50];
	int empNum;
public:
	EmployeeHandler():empNum(0)
	{}
	void AddEmployee(PermanentWorker* emp)
	{
		empList[empNum++] = emp;
	}
	void ShowAllSalaryInfo() const
	{
		for (int i = 0; i < empNum; i++)
		{
			empList[i]->ShowSalaryInfo();
		}
	}
	void ShowTotalSalary() const
	{
		int sum = 0;
		for (int i = 0; i < empNum; i++)
		{
			sum += empList[i]->GetPay();
		}
		cout << "salary sum: " << sum << endl;
	}
	~EmployeeHandler()
	{
		for (int i = 0; i < empNum; i++)
		{
			delete empList[i];
		}
	}
};
int main(void)
{
	// 직원관리를 목적으로 설계된 컨트롤 클래스의 객체생성
	EmployeeHandler handler;

	// 직원 등록
	handler.AddEmployee(new PermanentWorker("KIM", 1000));
	handler.AddEmployee(new PermanentWorker("LEE", 1500));
	handler.AddEmployee(new PermanentWorker("JUN", 2000));

	// 이번 달에 지불해야 할 급여의 정보
	handler.ShowAllSalaryInfo();

	// 이번 달에 지불해야 할 급여의 총합
	handler.ShowTotalSalary();
	return 0;
}
```
- 코드에 유연성/확장성이 없음
- 클래스가 추가되어도 handler코드가 많이 안바뀌면 유연성/확장성이 있는거임!!


### 07-2. 상속의 문법적인 이해
```C++
#include <iostream>
#include <cstring>
using namespace std;


class Person 
{
private:
	int age;   // 나이
	char name[50];  // 이름
public:
	Person(int myage, const char *myname) :age(myage)
	{
		strcpy(name, myname);
	}
	void WhatYourName() const
	{
		cout << "My name is " << name << endl;
	}
	void HowOldAreYou() const
	{
		cout << "I'm " << age << " years old" << endl;
	}
};

class UnivStudent : public Person
{
private:
	char major[50]; // 전공과목
public:
	UnivStudent(const char *myname, int myage, const char* mymajor)
		: Person(myage, myname)
	{
		strcpy(major, mymajor);
	}
	void WhoAreYou() const
	{
		WhatYourName();
		HowOldAreYou();
		cout << "My major is " << major << endl << endl;
	}
};


int main(void)
{
	UnivStudent ustd1("Lee", 22, "Computer eng.");
	ustd1.WhoAreYou();

	UnivStudent ustd2("Yoon", 21, "Electronic eng.");
	ustd2.WhoAreYou();

	return 0;
}
```
#### 유도 클래스의 객체 생성과정
- 유도 클래스의 객체 생성 과정에서 기초 클래스의 생성자는 100퍼센트 호출됨
- 유도 클래스의 생성자에서 기초 클래스의 생성자 호출을 명시하지 않으면 기초 클래스의 void 생성자가 호출됨
```C++
#include <iostream>
using namespace std;


class SoBase 
{
private:
	int baseNum;
public:
	SoBase() : baseNum(20) 
	{
		cout << "SoBase()" << endl;
	}
	SoBase(int n) :baseNum(n)
	{
		cout << "SoBase(int n)" << endl;
	}
	void ShowBaseData()
	{
		cout << baseNum << endl;
	}
};

class SoDerived :public SoBase
{
private:
	int derivNum;
public:
	SoDerived() :derivNum(30)
	{
		cout << "SoDerived()" << endl;
	}
	SoDerived(int n) :derivNum(n)
	{
		cout << "SoDerived(int n)" << endl;
	}
	SoDerived(int n1, int n2) :SoBase(n1), derivNum(n2)
	{
		cout << "SoDerived(int n1, int n2)" << endl;
	}
	void ShowDerivData()
	{
		ShowBaseData();
		cout << derivNum << endl;
	}
};


int main(void)
{
	cout << "case1....." << endl;
	SoDerived dr1;
	cout << "--------------" << endl;
	cout << "case2....." << endl;
	SoDerived dr2(12);
	dr2.ShowDerivData();
	cout << "--------------" << endl;
	SoDerived dr3(23, 24);
	dr3.ShowDerivData();
	return 0;
}

```

#### 유도 클래스 객체의 소멸과정
- 유도클래스 먼저, 기초클래스 나중에
- 생성자와 완전 반대의 순서
```C++
#include <iostream>
using namespace std;


class SoBase 
{
private:
	int baseNum;
public:
	SoBase(int n) : baseNum(n) 
	{
		cout << "SoBase() : " << baseNum << endl;
	}
	~SoBase()
	{
		cout << "~SoBase() : " << baseNum << endl;
	}
};

class SoDerived :public SoBase
{
private:
	int derivNum;
public:
	SoDerived(int n) : SoBase(n), derivNum(n)
	{
		cout << "SoDerived() : " << derivNum << endl;
	}
	~SoDerived()
	{
		cout << "~SoDerived() : " << derivNum << endl;
	}
};


int main(void)
{
	SoDerived drv1(15);
	SoDerived drv2(27);
	return 0;
}
```

- 생성자에서 동적할당한 메모리공간은 소멸자에서 해제
```c++
#include <iostream>
#include <cstring>
using namespace std;


class Person
{
private:
	char * name;
public:
	Person(const char * myname)
	{
		name = new char[strlen(myname) + 1];
		strcpy(name, myname);
	}
	~Person()
	{
		delete[]name;
	}
	void WhatYourName() const
	{
		cout << "My name is " << name << endl;
	}
};

class UnivStudent :public Person
{
private:
	char * major;
public:
	UnivStudent(const char *myname, const char *mymajor)
		: Person(myname)
	{
		major = new char[strlen(mymajor) + 1];
		strcpy(major, mymajor);
	}
	~UnivStudent()
	{
		delete[]major;
	}
	void WhoAreYou() const
	{
		WhatYourName();
		cout << "My major is " << major << endl << endl;
	}
};


int main(void)
{
	UnivStudent st1("Kim", "Math");
	st1.WhoAreYou();
	UnivStudent st2("Hong", "Physics");
	st2.WhoAreYou();
	return 0;
}
```
