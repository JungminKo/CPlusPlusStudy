### 05-1. '복사 생성자'와의 첫만남
#### 디폴트 복사 생성자
- 복사 생성자를 정의하지 않으면 멤버 대 멤버의 복사를 진행하는 디폴트 복사 생성자가 자동으로 삽입됨
- 그렇지만.. 복사생성자를 정의해야 하는 경우도 있음!!
```C++
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num1;
	int num2;
public:
	SoSimple(int n1, int n2) :num1(n1), num2(n2)
	{
		//empty
	}
	SoSimple(const SoSimple &copy) :num1(copy.num1), num2(copy.num2) // 원본을 변경시키지 않도록
	{
		cout << "Called SoSimple(SoSimple &copy)" << endl;
	}
	void ShowSimpleData()
	{
		cout << num1 << endl;
		cout << num2 << endl;
	}
};

int main(void)
{
	SoSimple sim1(15, 30);
	cout << "생성 및 초기화 직전" << endl;
	SoSimple sim2 = sim1; // SoSimple sim2(sim1); 로 변환!
	cout << "생성 및 초기화 직후" << endl;
	sim2.ShowSimpleData();
	return 0;
}
```

### 05-2. '깊은 복사'와 '얕은 복사'
#### 디폴트 복사 생성자의 문제점
- 이미 소멸된 문자열을 다시 소멸하려고 하니깐 문제 발생!!!
<br> ==> 따라서 문자를 복사할 때 다른 방식으로 복사해야함 
```C++
#include <iostream>
#include <cstring>
using namespace std;

class Person
{
private:
	char * name;
	int age;
public:
	Person(const char * myname, int myage)
		:age(myage)
	{
		int len = strlen(myname) + 1;
		name = new char[len];
		strcpy(name, myname);
	}
	void ShowPersonInfo() const
	{
		cout << "이름: " << name << endl;
		cout << "나이: " << age << endl;
	}
	~Person()
	{
		delete[]name;
		cout << "called destructor!" << endl;
	}
};

int main(void)
{
	Person man1("Lee dong woo", 29);
	Person man2 = man1;
	man1.ShowPersonInfo();
	man2.ShowPersonInfo();

	return 0;
}
```
#### 깊은 복사
```C++
Person(const Person& copy)
		:age(copy.age)
	{
		name = new char[strlen(copy.name)+1];
		strcpy(name, copy.name);
	}
```

### 05-3. 복사 생성자의 호출시점
1. 기존에 생성된 객체를 이용해서 새로운 개체를 초기화하는 경우
2. call-by-value 방식의 함수호출 과정에서 객체를 전달하는 경우
3. 객체를 반환하되, 참조형으로 반환하지 않는 경우
```C++
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num;
public:
	SoSimple(int n) :num(n) {}
	SoSimple(const SoSimple &copy) :num(copy.num)
	{
		cout << "Called SoSimple(const SoSimple &copy)" << endl;
	}
	void ShowData()
	{
		cout << "num: " << num << endl;
	}
};

void SimpleFuncObjc(SoSimple ob)
{
	ob.ShowData();
}
int main(void)
{
	SoSimple obj(7);
	cout << "함수 호출 전" << endl;
	SimpleFuncObjc(obj);//함수에 obj 인자를 전달하는 과정에서 복사생성자 호출됨
	cout << "함수 호출 후" << endl;
	return 0;
}
```

```C++
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num;
public:
	SoSimple(int n) :num(n) {}
	SoSimple(const SoSimple &copy) :num(copy.num)
	{
		cout << "Called SoSimple(const SoSimple &copy)" << endl;
	}
	SoSimple & AddNum(int n)
	{
		num += n;
		return *this;
	}
	void ShowData()
	{
		cout << "num: " << num << endl;
	}
};

SoSimple SimpleFuncObj(SoSimple ob)
{
	cout << "return 이전" << endl;
	return ob;
}
int main(void)
{
	SoSimple obj(7);
	SimpleFuncObj(obj).AddNum(30).ShowData();
	obj.ShowData();
	return 0;
}
```
