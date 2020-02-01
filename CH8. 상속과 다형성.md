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
- EmployeeHandler의 실마리 
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
	handler.AddEmployee(new PermanentWorker("JUN", 2000));

	// 이번 달에 지불해야 할 급여의 정보
	handler.ShowAllSalaryInfo();

	// 이번 달에 지불해야 할 급여의 총합
	handler.ShowTotalSalary();
	return 0;
}
```
