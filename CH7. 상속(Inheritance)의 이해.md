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

### 07-3. protected 선언과 세 가지 형태의 상속
- protected는 private과 클래스의 외부에서 접근이 불가능하다는 점에서 유사
- 하지만 protected로 선언된 멤버변수는 이를 상속하는 유도 클래스에서 접근이 가능하다는 차이점이 있음

- 상속은 public 상속만 거의 사용!

### 07-4. 상속을 위한 조건
- 유도 클래스 is a 기초 클래스
```C++
#include <iostream>
#include <cstring>
using namespace std;

class Computer
{
private:
	char owner[50];
public:
	Computer(const char * name)
	{
		strcpy(owner, name);
	}
	void Calculate()
	{
		cout << "요청 내용을 계산합니다." << endl;
	}
};

class NotebookComp : public Computer
{
private:
	int Battery;
public:
	NotebookComp(const char * name, int initChag)
		:Computer(name), Battery(initChag){}
	void Charging() { Battery += 5; }
	void UseBattery() { Battery -= 1; }
	void MovingCal()
	{
		if (GetBatteryInfo() < 1)
		{
			cout << "충전이 필요합니다." << endl;
			return;
		}
		cout << "이동하면서 ";
		Calculate();
		UseBattery();
	}
	int GetBatteryInfo() { return Battery; }
};

class TabletNotebook : public NotebookComp
{
private:
	char regstPenModel[50];
public:
	TabletNotebook(const char * name, int initChag, const char * pen)
		:NotebookComp(name, initChag)
	{
		strcpy(regstPenModel, pen);
	}
	void write(const char * penInfo)
	{
		if (GetBatteryInfo() < 1)
		{
			cout << "충전이 필요합니다." << endl;
			return;
		}
		if (strcmp(regstPenModel, penInfo) != 0)
		{
			cout << "등록된 펜이 아닙니다.";
			return;
		}
		cout << "필기 내용을 처리합니다." << endl;
		UseBattery();
	}
};
int main(void)
{
	NotebookComp nc("이수종", 5);
	TabletNotebook tn("정수영", 5, "ISE-241-242");
	nc.MovingCal();
	tn.write("ISE-241-242");
	return 0;
}
```
- has a 관계도 상속의 조건은 되지만 복합관계로 이를 대신하는 것이 일반적임

상속관계일때
```C++
#include <iostream>
#include <cstring>
using namespace std;

class Gun
{
private:
	int bullet; //장전된 총알의 수
public:
	Gun(int bnum) :bullet(bnum) {}
	void Shot()
	{
		cout << "BBANG!" << endl;
		bullet--;
	}
};

class Police : public Gun
{
private:
	int handcuffs; // 소유한 수갑의 수
public:
	Police(int bnum, int bcuff)
		:Gun(bnum), handcuffs(bcuff) {}
	void PutHandcuff()
	{
		cout << "SNAP!" << endl;
		handcuffs--;
	}
};
int main(void)
{
	Police pman(5, 3); //총알 5 수갑 3
	pman.Shot();
	pman.PutHandcuff();
	return 0;
}
```

복합관계일때
```c++
#include <iostream>
#include <cstring>
using namespace std;

class Gun
{
private:
	int bullet; //장전된 총알의 수
public:
	Gun(int bnum) :bullet(bnum) {}
	void Shot()
	{
		cout << "BBANG!" << endl;
		bullet--;
	}
};

class Police 
{
private:
	int handcuffs; // 소유한 수갑의 수
	Gun * pistol;  // 소유하고 있는 권총
public:
	Police(int bnum, int bcuff)
		: handcuffs(bcuff)
	{
		if (bnum > 0)
			pistol = new Gun(bnum);
		else
			pistol = NULL;
	}
	void PutHandcuff()
	{
		cout << "SNAP!" << endl;
		handcuffs--;
	}
	void Shot()
	{
		if (pistol == NULL)
			cout << "Hut BBANG!" << endl;
		else
			pistol->Shot();
	}
	~Police()
	{
		if (pistol != NULL)
			delete pistol;
	}
};
int main(void)
{
	Police pman1(5, 3); //총알 5 수갑 3
	pman1.Shot();
	pman1.PutHandcuff();

	Police pman2(0, 3); //권총을 소유하지 않은 경찰
	pman2.Shot();
	pman2.PutHandcuff();
	return 0;
}
```
