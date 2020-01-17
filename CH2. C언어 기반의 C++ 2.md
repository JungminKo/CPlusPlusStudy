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
- 참조자 : 자신이 참조하는 변수를 대신할 수 있는 또 하나의 이름. 일종의 별칭
<br> 변수를 대상으로만 선언 가능. 선언됨과 동시에 누군가를 참조해야 함.
- `&` 사용
- 참조자의 수에는 제한이 없음. 참조자를 대상으로도 참조자 선언 가능.
<br> BUT 남발은 금물
- 포인터와 헷갈리지 말기. 포인터 몰라도 알 수 있음
<br> int &num2=num1; // 변수 num1에 대한 참조자 num2를 선언해라
<br> * ptr = &num1; // 변수 num1에 주소 값을 반환해서 포인터 ptr에 저장해라

```c++
#include <iostream>

using namespace std;

int main(void)
{
	int num1 = 1020;
	int &num2 = num1;

	num2 = 3047;
	cout << "VAL: " << num1 << endl;
	cout << "REF: " << num2 << endl;

	cout << "VAL: " << &num1 << endl;
	cout << "REF: " << &num2 << endl;

	return 0;
}
```
- 배열요소도 변수의 범위에 포함됨

```c++
#include <iostream>

using namespace std;

int main(void)
{
	int arr[3] = { 1,3,5 };
	int &ref1 = arr[0];
	int &ref2 = arr[1];
	int &ref3 = arr[2];

	cout << ref1 << endl;
	cout << ref2 << endl;
	cout << ref3 << endl;

	return 0;
}
```
- 포인터 변수도 변수의 범위에 포함됨
```C++
#include <iostream>

using namespace std;

int main(void)
{
	int num = 12;
	int *ptr = &num;
	int **dptr = &ptr;

	int &ref = num;
	int *(&pref) = ptr; // 포인터 변수의 참조자 선언
	int **(&dpref) = dptr; // 포인터 변수의 참조자 선언

	cout << ref << endl;
	cout << *pref << endl;
	cout << **dpref << endl;

	return 0;
}
```

### 02-4. 참조자(Reference)와 함수
- call by reference : 주소 값을 인자로 전달하는 함수의 호출방식 (함수 외부에 선언된 변수에 접근 가능)

- 주소 값을 이용한 call-by-reference : 기존 C언어에서 해옴
- 참조자를 이용한 call-by-reference : c++에서 가능해진 방식
```c++
#include <iostream>

using namespace std;

void SwapByRef2(int &ref1, int &ref2)
{
	int temp = ref1;
	ref1 = ref2;
	ref2 = temp;
}
int main(void)
{
	int val1 = 10;
	int val2 = 20;

	SwapByRef2(val1, val2);
	cout << "val1: " << val1 << endl;
	cout << "val2: " << val2 << endl;

	return 0;
}
```

- 함수 내에서 참조자를 통한 값의 변경을 원하지 않을 때 참조자를 const로 선언-> 함수의 원형만 봐도 값의 변경이 이뤄지지 않음을 확인할 수 있음
<br> 사실 참조자는 많이 이용하는 편이 아님

#### 반환형이 참조형일 수 있음
- 둘의 차이는?
<br> 반환형이 참조형인 경우 반환값이 참조형일 수도 있고, 일반 변수일수도 있음
<br> 다만, 반환형이 기본자료형인 경우(ex. int) 함수의 반환값은 반드시 변수에 저장해야 함. 반
```C++
int& RefRetFuncOne(int &ref)
{
	ref++;
	return ref;
}
```
```C++
int RefRetFuncTwo(int &ref)
{
	ref++;
	return ref;
}
```

##### 참조자 반환하고 다시 참조자에 저장
```C++
#include <iostream>

using namespace std;

int& RefRetFuncOne(int &ref)
{
	ref++;
	return ref;
}

int main(void)
{
	int num1 = 1;
	int &num2 = RefRetFuncOne(num1); // 참조자 반환하고 다시 참조자에 저장

	num1++;
	num2++;
	cout << "num1: " << num1 << endl;
	cout << "num2: " << num2 << endl;
	return 0;
}
```
##### 참조자 반환하고 일반변수에 저장. 별개의 변수로 됨.
```C++
#include <iostream>

using namespace std;

int& RefRetFuncOne(int &ref) 
{
	ref++;
	return ref;
}

int main(void)
{
	int num1 = 1;
	int num2 = RefRetFuncOne(num1); // 참조자 반환하고 일반변수에 저장. 별개의 변수로 됨.

	num1+=1;
	num2+=100;
	cout << "num1: " << num1 << endl;
	cout << "num2: " << num2 << endl;
	return 0;
}
```
##### 일반변수에만 저장 가능
```C++
#include <iostream>

using namespace std;

int RefRetFuncOne(int &ref) //반환형이 기본자료형 int이다.
{
	ref++;
	return ref;
}

int main(void)
{
	int num1 = 1;
	int num2 = RefRetFuncOne(num1); // 참조자 반환하고 일반변수에 저장. 별개의 변수로 됨.

	num1+=1;
	num2+=100;
	cout << "num1: " << num1 << endl;
	cout << "num2: " << num2 << endl;
	return 0;
}
```

#### 잘못된 참조의 반환
- 밑의 예시에서 `int &ref=RetuRefFunc(10);`이라고 하면, 지역변수 num은 사라졌으나 ref가 참조하는 이상한 형태가 되어버림!
```C++
int& RetuRefFunc(int n)
{
	int num=20;
	num+=n;
	return num;
}
```

#### const 참조자의 또 다른 특징
- 상수화된 변수에 대한 참조자 선언은 다음과 같이 진행
<br> ref를 통한 값의 변경이 불가능하기 때문에 상수화에 대한 논리적인 문제점 발생하지 않음
```C++
const int num=20;
const int &ref=num;
```
- const 참조자는 상수도 참조 가능. 이 때의 변수는 '상수화된 변수'라고 함.
```C++
const int &ref=50;
```
인자의 전달을 목적으로 변수를 선언한다는건 번거롭지만, 참조자를 이용하면 다음과 같이 간단하게 할 수 있다.
```C++
int Adder(const int &num1, const int &num2)
{
	return num1+num2;
}
```


### 02-5. malloc & free를 대신하는 new & delete

#### mew & delete
- new : malloc 대신
- delete : free 대신
```C++
#include <iostream>
#include <string.h>
#include <stdlib.h>

using namespace std;

char * MakeStrAdr(int len)
{
	char * str = new char[len];
	return str;
}

int main(void)
{
	char * str = MakeStrAdr(20);
	strcpy(str, "I am so happy~");
	cout << str << endl;
	delete[]str;
	return 0;
}
```


#### cf) C언어에서 보이던 방식
- 단점1) 할당할 대사의 정보는 무조건 바이트 크기단위로 전달
- 단점2) 반환형이 void형 포인터이기 때문에 적절한 형 변환 필요
```C++
#include <iostream>
#include <string.h>
#include <stdlib.h>

using namespace std;

char * MakeStrAdr(int len)
{
	char * str = (char *)malloc(sizeof(char)*len);
	return str;
}

int main(void)
{
	char * str = MakeStrAdr(20);
	strcpy(str, "I am so happy~");
	cout << str << endl;
	free(str);
	return 0;
}
```

#### 객체의 생성에는 반드시 new & delete
- new를 이용해야만 객체가 생성됨... 이유는 추후 클래스를 배우면서!!
```C++
#include <iostream>
#include <stdlib.h>

using namespace std;

class Simple
{
public:
	Simple()
	{
		cout << "I'm simple constructor!" << endl;
	}
};

int main(void)
{
	cout << "case 1: ";
	Simple * sp1 = new Simple; // new 연산자를 이용해 힙 영역에 변수 할당

	cout << "case2: ";
	Simple * sp2 = (Simple *)malloc(sizeof(Simple) * 1); // malloc 함수호출을 통해 힙 영역에 변수 할당

	cout << endl << "end of main" << endl;
	delete sp1;
	free(sp2);

	return 0;
}
```

#### 참조자로도 힙에 할당된 변수 접근 가능
```C++
int *ptr=new int;
int &ref=*ptr;
ref=20;
cout<<*ptr<<endl;
```
