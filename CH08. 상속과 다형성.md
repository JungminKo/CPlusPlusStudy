### 08-1. 객체 포인터의 참조관계
- C++에서 AAA형 포인터 변수는 AAA 객체 또는 AAA를 직접 또는 간접적으로 상속하는
<br> 모든 객체를 가리킬수 있다.

```C++
#include <iostream>
using namespace std;

class Person 
{
public:
	void Sleep() { cout << "Sleep" << endl; }
};

class Student : public Person
{
public:
	void Study() { cout << "Study" << endl; }
};

class PartTimeStudent :public Student
{
public: 
	void Work() { cout << "Work" << endl; }
};

int main(void)
{
	Person * ptr1 = new Student();
	Person * ptr2 = new PartTimeStudent();
	Student * ptr3 = new PartTimeStudent();
	ptr1->Sleep();
	ptr2->Sleep();
	ptr3->Study();
	delete ptr1;
	delete ptr2;
	delete ptr3;
	return 0;
}
```


- - 함수 오버라이딩(function overriding) : 기초클래스와 유도클래스에 동일한 이름과 형태로 두 함수를 정의할 때를 
<br> 오버라이딩된 기초 클래스의 함수는 오버라이딩을 한 유도클래스의 함수에 가려짐 
```C++
#include <iostream>
#include <cstring>
using namespace std;

class Employee
{
private:
	char name[100];
public:
	Employee(const char * name)
	{
		strcpy(this->name, name);
	}
	void ShowYourName() const
	{
		cout << "name: " << name << endl;
	}
};

class PermanentWorker : public Employee
{
private:
	int salary; //급여
public:
	PermanentWorker(const char * name, int money)
		: Employee(name), salary(money) {}
	int GetPay() const
	{
		return salary;
	}
	void ShowSalaryInfo() const
	{
		ShowYourName();
		cout << "salary: " << GetPay() << endl << endl;
	}
};

class TemporaryWorker : public Employee
{
private:
	int workTime; // 이 달에 일한 시간의 합계
	int payPerHour; // 시간 당 급여
public:
	TemporaryWorker(const char * name, int pay)
		:Employee(name), workTime(0), payPerHour(pay) {}
	void AddWorkTime(int time)// 일한 시간의 추가
	{
		workTime += time;
	}
	int GetPay() const // 이달의 급여
	{
		return workTime * payPerHour;
	}
	void ShowSalaryInfo() const
	{
		ShowYourName();
		cout << "salary: " << GetPay() << endl << endl;
	}
};

class SalesWorker : public PermanentWorker
{
private:
	int salesResult; //월 판매실적
	double bonusRatio; // 상여금 비율
public:
	SalesWorker(const char * name, int money, double ratio)
		: PermanentWorker(name, money), salesResult(0), bonusRatio(ratio) 
	{}
	void AddSalesResult(int value)
	{
		salesResult += value;
	}
	int GetPay() const
	{
		return PermanentWorker::GetPay() // PermanentWorker의 GetPay 함수 호출
			+ (int)(salesResult*bonusRatio);
	}
	void ShowSalaryInfo() const
	{
		ShowYourName();
		cout << "salary: " << GetPay() << endl << endl; // SalesWorker의 GetPay 함수 호출
	}
};
class EmployeeHandler
{
private:
	Employee * empList[50];
	int empNum;
public:
	EmployeeHandler() :empNum(0) {}
	void AddEmployee(Employee * emp)
	{
		empList[empNum++] = emp;
	}
	void ShowAllSalaryInfo() const
	{
		/*
		오류발생하므로 주석처리진행
		for (int i = 0; i < empNum; i++)
			empList[i]->ShowSalaryInfo();
		*/
	}
	void ShowTotalSalary() const
	{
		int sum = 0;
		/*
		오류발생하므로 주석처리진행
		for (int i = 0; i < empNum; i++)
			sum+=empList[i]->GetPay();
		*/
		cout << "salary sum: " << sum << endl;
	}
	~EmployeeHandler()
	{
		for (int i = 0; i < empNum; i++)
			delete empList[i];
	}
};

int main(void)
{
	// 직원관리를 목적으로 설계된 컨트롤 클래스의 객체 생성
	EmployeeHandler handler;

	// 직원 등록
	handler.AddEmployee(new PermanentWorker("KIM", 1000));
	handler.AddEmployee(new PermanentWorker("LEE", 1500));

	// 임시직 등록
	TemporaryWorker * alba = new TemporaryWorker("Jung", 700);
	alba->AddWorkTime(5); // 5시간 일한 결과 등록
	handler.AddEmployee(alba);

	// 영업직 등록
	SalesWorker * seller = new SalesWorker("Hong", 1000, 0.1);
	seller->AddSalesResult(7000); // 영업실적등록
	handler.AddEmployee(seller);

	// 이번 달에 지불해야 할 급여의 정보
	handler.ShowAllSalaryInfo();

	// 이번 달에 지불해야 할 급여의 총합
	handler.ShowTotalSalary();
	return 0;
}
```

### 08-2. 가상함수(Virtual Function)
#### 함수의 오버라이딩과 포인터 형
- 포인터 변수의 자료형에 따라서 호출되는 함수의 종류가 달라짐
```C++
#include <iostream>
using namespace std;

class First
{
public:
	void MyFunc() { cout << "FirstFunc" << endl; }
};

class Second : public First
{
public:
	void MyFunc() { cout << "SecondFunc" << endl; }
};

class Third : public Second
{
public:
	void MyFunc() { cout << "ThirdFunc" << endl; }
};

int main(void)
{
	Third * tptr = new Third();
	Second * sptr = tptr;
	First * fptr = sptr;

	fptr->MyFunc();
	sptr->MyFunc();
	tptr->MyFunc();
	delete tptr;
	return 0;
}
```


#### 가상함수(Virtual Function)
- 포인터 변수가 실제로 가리키는 객체를 참조하여 호출 대상 결정
- 포인터 변수 자료형에 따라 호출되는 함수의 종류가 달라지는 걸 막기 위해 사용
- 객체지향의 개념!
```C++
#include <iostream>
using namespace std;

class First
{
public:
	virtual void MyFunc() { cout << "FirstFunc" << endl; }
};

class Second : public First
{
public:
	virtual void MyFunc() { cout << "SecondFunc" << endl; }
};

class Third : public Second
{
public:
	virtual void MyFunc() { cout << "ThirdFunc" << endl; }
};

int main(void)
{
	Third * tptr = new Third();
	Second * sptr = tptr;
	First * fptr = sptr;

	fptr->MyFunc();
	sptr->MyFunc();
	tptr->MyFunc();
	delete tptr;
	return 0;
}
```

```C++
#include <iostream>
#include <cstring>
using namespace std;

class Employee
{
private:
	char name[100];
public:
	Employee(const char * name)
	{
		strcpy(this->name, name);
	}
	void ShowYourName() const
	{
		cout << "name: " << name << endl;
	}
	virtual int GetPay() const
	{
		return 0;
	}
	virtual void ShowSalaryInfo() const
	{}
};

class PermanentWorker : public Employee
{
private:
	int salary; //급여
public:
	PermanentWorker(const char * name, int money)
		: Employee(name), salary(money) {}
	int GetPay() const
	{
		return salary;
	}
	void ShowSalaryInfo() const
	{
		ShowYourName();
		cout << "salary: " << GetPay() << endl << endl;
	}
};

class TemporaryWorker : public Employee
{
private:
	int workTime; // 이 달에 일한 시간의 합계
	int payPerHour; // 시간 당 급여
public:
	TemporaryWorker(const char * name, int pay)
		:Employee(name), workTime(0), payPerHour(pay) {}
	void AddWorkTime(int time)// 일한 시간의 추가
	{
		workTime += time;
	}
	int GetPay() const // 이달의 급여
	{
		return workTime * payPerHour;
	}
	void ShowSalaryInfo() const
	{
		ShowYourName();
		cout << "salary: " << GetPay() << endl << endl;
	}
};

class SalesWorker : public PermanentWorker
{
private:
	int salesResult; //월 판매실적
	double bonusRatio; // 상여금 비율
public:
	SalesWorker(const char * name, int money, double ratio)
		: PermanentWorker(name, money), salesResult(0), bonusRatio(ratio) 
	{}
	void AddSalesResult(int value)
	{
		salesResult += value;
	}
	int GetPay() const
	{
		return PermanentWorker::GetPay() // PermanentWorker의 GetPay 함수 호출
			+ (int)(salesResult*bonusRatio);
	}
	void ShowSalaryInfo() const
	{
		ShowYourName();
		cout << "salary: " << GetPay() << endl << endl; // SalesWorker의 GetPay 함수 호출
	}
};
class EmployeeHandler
{
private:
	Employee * empList[50];
	int empNum;
public:
	EmployeeHandler() :empNum(0) {}
	void AddEmployee(Employee * emp)
	{
		empList[empNum++] = emp;
	}
	void ShowAllSalaryInfo() const
	{
		for (int i = 0; i < empNum; i++)
			empList[i]->ShowSalaryInfo();	
	}
	void ShowTotalSalary() const
	{
		int sum = 0;
		for (int i = 0; i < empNum; i++)
			sum+=empList[i]->GetPay();

		cout << "salary sum: " << sum << endl;
	}
	~EmployeeHandler()
	{
		for (int i = 0; i < empNum; i++)
			delete empList[i];
	}
};

int main(void)
{
	// 직원관리를 목적으로 설계된 컨트롤 클래스의 객체 생성
	EmployeeHandler handler;

	// 직원 등록
	handler.AddEmployee(new PermanentWorker("KIM", 1000));
	handler.AddEmployee(new PermanentWorker("LEE", 1500));

	// 임시직 등록
	TemporaryWorker * alba = new TemporaryWorker("Jung", 700);
	alba->AddWorkTime(5); // 5시간 일한 결과 등록
	handler.AddEmployee(alba);

	// 영업직 등록
	SalesWorker * seller = new SalesWorker("Hong", 1000, 0.1);
	seller->AddSalesResult(7000); // 영업실적등록
	handler.AddEmployee(seller);

	// 이번 달에 지불해야 할 급여의 정보
	handler.ShowAllSalaryInfo();

	// 이번 달에 지불해야 할 급여의 총합
	handler.ShowTotalSalary();
	return 0;
}
```

#### 순수 가상함수(Pure Virtual Function)와 추상 클래스(Abstract Class)
- 객체생성을 목적으로 정의되지 않은 클래스도 존재 / 기초클래스로의 의미만 가짐
<br> -> 이러한 가상함수는 '순수 가상함수'라 선언하여 객체의 생성을 문법적으로 막는 것이 좋다.

- 순수 가상함수 : 함수의 몸체가 저장되지 않은 함수
<br> virtual 함수이름() const=0;
<br> 이 모습은 0의 대입을 의미하는 것이 아니라 몸체가 정의되지 않았음을 알리는 것

ex. 	virtual int GetPay() const = 0;
	virtual void ShowSalaryInfo() const = 0;

장점 1) 잘못된 객체의 생성 막을 수 있음
장점 2) 실행되지 않는 함수임을 보다 명확히 명시

- 추상클래스 : 하나 이상의 멤버함수를 순수 가상함수로 선언한 클래스
<br> 객체생성이 불가능한 클래스


#### 다형성(Polymorphism)
- 문장은 같은데 결과는 다르다.
```C++
#include <iostream>
using namespace std;

class First
{
public:
	virtual void SimpleFunc() { cout << "First" << endl; }
};

class Second : public First
{
public:
	virtual void SimpleFunc() { cout << "Second" << endl; }
};

int main(void)
{
	First * ptr = new First();
	ptr->SimpleFunc(); // 아래에 동일한 문장이 존재한다.
	delete ptr;

	ptr = new Second();
	ptr->SimpleFunc(); // 아래에 동일한 문장이 존재한다.
	delete ptr;
	return 0;
}
```

### 08-3. 가상 소멸자와 참조자의 참조 가능성
- 가상소멸자 : virtual로 선언된 소멸자
- 가상소멸자가 호출되면 순서에 맞게 유도클래스의 소멸자부터 기초 클래스의 소멸자까지 차례로 호출됨!
```C++
#include <iostream>
using namespace std;

class First
{
private:
	char * strOne;
public:
	First(const char * str)
	{
		strOne = new char[strlen(str) + 1];
	}
	virtual ~First()
	{
		cout << "~First()" << endl;
		delete[]strOne;
	}
};

class Second: public First
{
private:
	char * strTwo;
public:
	Second(const char * str1, const char * str2)
		:First(str1)
	{
		strTwo = new char[strlen(str2) + 1];
	}
	~Second()
	{
		cout << "~Second()" << endl;
		delete[]strTwo;
	}
};


int main(void)
{
	First * ptr = new Second("simple", "complex");
	delete ptr;
	return 0;
}
```

#### 참조자의 참조 가능성
- 참조자 형태에 따라 맞는형태의 함수가 호출된다(오버라이딩된 함수중에서)
```C++
#include <iostream>
using namespace std;

class First
{
public:
	void FirstFunc() { cout << "FirstFunc()" << endl; }
	virtual void SimpleFunc() { cout << "First's SimpleFunc()" << endl; }
};

class Second: public First
{
public:
	void SecondFunc() { cout << "SecondFunc()" << endl; }
	virtual void SimpleFunc() { cout << "Second's SimpleFunc()" << endl; }
};

class Third : public Second
{
public:
	void ThirdFunc() { cout << "ThirdFunc()" << endl; }
	virtual void SimpleFunc() { cout << "Third's SimpleFunc()" << endl; }
};


int main(void)
{
	Third obj;
	obj.FirstFunc();
	obj.SecondFunc();
	obj.ThirdFunc();
	obj.SimpleFunc();

	Second & sref = obj;
	sref.FirstFunc();
	sref.SecondFunc();
	sref.SimpleFunc();

	First & fref = obj;
	fref.FirstFunc();
	fref.SimpleFunc();
	return 0;
}
```
