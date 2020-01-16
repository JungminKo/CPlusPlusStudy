# Chapter 02.  C언어 기반의 C++ 2
### 02-2. 새로운 자료형 bool
- True : 1, False: 0
- 단, True와 False는 참과 거짓을 표현하기 위한 `1바이트` 크기의 데이터
- 최근에는 C에서도 기본자료형에 추가했으나, 상당수의 C 컴파일러가 bool을 지원하지 않기 때문에 C++의 자료형으로 많이 인식함

```C++
#include <iostream>

using namespace std;

int main(void)
{
	int num = 10;
	int i = 0;

	cout << "true: " << true << endl;
	cout << "false: " << false << endl;

	while (true)
	{
		cout << i++ << ' ';
		if (i > num)
			break;
	}
	cout << endl;

	cout << "sizeof 1: " << sizeof(1) << endl;
	cout << "sizeof 0: " << sizeof(0) << endl;
	cout << "sizeof true: " << sizeof(true) << endl;
	cout << "sizeof false: " << sizeof(false) << endl;

	return 0;
}

```

#### 자료형 bool
- bool형 데이터 : true와 false
- 기본자료형이므로 bool형 변수 선언이 가능
<br> bool isTrueOne=true;

```C++
#include <iostream>

using namespace std;

bool IsPositive(int num)
{
	if (num <= 0)
		return false;
	else
		return true;
}

int main(void)
{
	bool isPos;
	int num;
	cout << "Input number: ";
	cin >> num;

	isPos = IsPositive(num);
	if (isPos)
		cout << "Positive number" << endl;
	else
		cout << "Negative numbber" << endl;

	return 0;
}
```

### 02-3. 참조자(Reference)의 이해
- 포인터와 헷갈리지 말기. 포인터 몰라도 알 수 있음
