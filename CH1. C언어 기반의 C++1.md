## Part 01. C++로의 전환
# Chapter 01.  C언어 기반의 C++ 1
### 01-1. printf와 scanf를 대신하는 입출력 방식

#### 문자열 "Hello World"의 출력
```C++
#include <iostream>

int main(void)
{
  int num=20;
  std::cout<<"Hello World!"<<std::endl;
  std::cout<<"Hello "<<"World!"<<std::endl;
  std::cout<<num<<' '<<'A';
  std::cout<<' '<<3.14<<std::endl;
  return 0;
}
```
#### scanf를 대신하는 데이터의 입력
```C++
#include <iostream>

int main(void)
{
	int val1;
	std::cout << "첫 번째 숫자입력: ";
	std::cin >> val1;

	int val2;
	std::cout << "두 번째 숫자입력: ";
	std::cin >> val2;

	int result = val1 + val2;
	std::cout << "덧셈결과: " << result << std::endl;
	return 0;
}
```
#### <관찰결과>
#### 1. 헤더파일 선언문 #include <iostream>
  - iostream     vs               iostream.h 
  <br> 새로운 표준 라이브러리 및 헤더 의미 vs 과거의 표준 입출력 라이브러리 및 헤더 의미

#### 2. 데이터 출력 및 입력 C와 달리 별도의 포맷을 지정안해도됨!!
  - std::cout<<'출력대상';
  - std::cin>>'변수';
  <br> std::cin>>'변수1'>>'변수2'; 처럼 연속적인 데이터의 입력을 요구할 수도 있음
  <br> 이때, 데이터간 경계는 탭, 스페이스바, enter키의 입력과 같은 공백에 의해 나눠짐
   
#### 3. std::endl을 이용한 개행
#### 4. c++의 지역변수 선언
  - 예전 c언어와는 달리(요즘에는 안그럼) 지역변수의 선언 위치에 제한을 두지 않음
  - for문의 초기화 문장 내에서 변수 선언이 가능해짐
  <br> ex. for(int num=0;num><10;num++){}

```c++
#include <iostream>

int main(void)
{
	int val1, val2;
	int result = 0;

	std::cout << "두 개의 숫자입력: ";
	std::cin >> val1 >> val2;

	if (val1 < val2)
	{
		for (int i = val1 + 1; i < val2; i++)
			result += i;
	}
	else
	{
		for (int i = val2 + 1; i < val1; i++)
			result += i;
	}

	std::cout << "두 수 사이의 정수 합: " << result << std::endl;
	return 0;
}
```

#### 배열 기반의 문자열 출력
```c++
#include <iostream>

int main(void)
{
	char name[100];
	char lang[200];

	std::cout << "이름은 무엇입니까? ";
	std::cin >> name;

	std::cout << "좋아하는 프로그래밍 언어는 무엇인가요? ";
	std::cin >> lang; 
	
	
	std::cout << "내 이름은 " << name << "입니다.\n";
	std::cout << "제일 좋아하는 언어는 " << lang << "입니다."<<std::endl;
	return 0;
}
```


### 01-2. 함수 오버로딩(Function Overloading)
- C++에서는 함수호출 시 전달되는 인자를 통해서 호출하고자 하는 함수의 구분이 가능하기 때문에
<br> 매개변수의 선언형태가 다르면, 동일한 이름의 함수정의를 허용함
<br> : 함수 오버로딩(Function Overloading)

- 함수 오버로딩의 조건 : 매개변수의 `자료형` 또는 `개수`가 다르다.
<br> 주의 : 반환형이 다른 건 함수를 구분하는 기준이 될 수 없음


*C에서는 안되는 이유?*
- C++은 호출할 함수를 찾을 때 C와 달리 '함수의 이름', '매개변수의 선언'을 동시에 활용함
- 반면, C는 '함수의 이름'으로만 호출대상을 찾기 때문에 동일한 이름의 함수가 정의되는 것을 허용하지 않음

```C++
#include <iostream>

void MyFunc(void)
{
	std::cout << "MyFunc(void) called" << std::endl;
}

void MyFunc(char c)
{
	std::cout << "MyFunc(char c) called" << std::endl;
}

void MyFunc(int a, int b)
{
	std::cout << "MyFunc(int a, int b) called" << std::endl;
}

int main(void)
{
	MyFunc();
	MyFunc('A');
	MyFunc(12, 13);
		
	return 0;
}
```

### 01-3. 매개변수의 디폴트 값(Default Value)
- C++의 함수에는 '디폴트 값'이라는 것을 설정할 수 있음
- 매개변수에 디폴트 값이 설정되어 있으면 선언된 매개변수의 수보다 적은 수의 인자 전달이 가능
<br> 단, 전달되는 인자는 왼쪽에서부터 채워져 나가고, 부족한 부분은 디폴트 값으로 채워짐
- 함수의 원형을 별도로 선언하는 경우, 매개변수의 디폴트 값은 함수의 선언 부분에만 표현하면 됨

```C++
#include <iostream>

int Adder(int num1 = 1, int num2 = 2) 
{
	return num1 + num2;
}

int main(void)
{
	std::cout << Adder() << std::endl;
	std::cout << Adder(5) << std::endl; 
	std::cout << Adder(3, 5) << std::endl;
	return 0;
}
```

```C++
#include <iostream>

int Adder(int num1 = 1, int num2 = 2);

int main(void)
{
	std::cout << Adder() << std::endl;
	std::cout << Adder(5) << std::endl;
	std::cout << Adder(3, 5) << std::endl;
	return 0;
}

int Adder(int num1, int num2)
{
	return num1 + num2;
}
```

#### 부분적 디폴트 값 설정
- 부분적으로 디폴트 값을 설정할 때는 오른쪽 매개변수의 디폴트 값부터 채우는 형태로 정의해야 함
<br> WHY?
<br> 함수에 전달되는 인자는 왼쪽에서부터 오른쪽으로 채워지기 때문
```C++
#include <iostream>

int BoxVolume(int length, int width = 1, int height = 1);

int main(void)
{
	std::cout << "[3, 3, 3] : " << BoxVolume(3, 3, 3) << std::endl;
	std::cout << "[5, 5, D] : " << BoxVolume(5, 5) << std::endl;
	std::cout << "[7, D, D] : " << BoxVolume(7) << std::endl;
	return 0;
}

int BoxVolume(int length, int width, int height)
{
	return length * width * height;
}
```
- 주의 : BoxVolume()는 불가능! length에는 디폴트값이 설정되어있지 않음

### 01-4. 인라인(inline) 함수
- C 언어의 매크로 함수와 유사
<br> 장 : 일반적인 함수에 비해서 실행속도가 빠름
<br> 단 : 복잡한 함수는 매크로의 형태로 정의하기가 어려움
- 인라인 함수는 몸체 부분이 호출문을 대체
```C++
#include <iostream>

inline int SQUARE(int x)
{
	return x * x;
}

int main(void)
{
	std::cout << SQUARE(5) << std::endl;
	std::cout << SQUARE(12) << std::endl;
	return 0;
}
```
#### 참고
- 매크로를 이용한 함수의 인라인화는 전처리기에 의해 처리되지만, 키워드 inline을 이용한 함수의 인라인화는 컴파일러에 의해 처리됨
- 따라서 컴파일러는 함수의 인라인화가 오히려 성능에 안좋다고 판단되면 이 키워드를 무시하기도 함
- 또한 컴파일러는 필요한 경우 일부 함수를 임의로 인라인 처리하기도 함

#### 매크로 함수에는 있지만 인라인 함수에는 없는 장점
- 매크로 함수는 자료형에 의존적이지 않은 반면 인라인 함수는 의존적임
<br> ex. #define SQUARE(x) ((x)*(x))
<br> ex. inline int SQUARE(int x) {return x*x;) // int로 한정됨

- 사실 이렇게 하는 방법도 있다고 함(추후에 배울 예정)
<br> : `플릿`이라는 방법임
```c++
#include <iostream>

template <typename T>
inline T SQUARE(T x)
{
	return x * x;
}

int main(void)
{
	std::cout << SQUARE(5.5) << std::endl;
	std::cout << SQUARE(12) << std::endl;
	return 0;
}
```
### 01-5. 이름공간(namespace)에 대한 소개
- `::` : 범위지정 연산자(scope resolution operator)
<br> 이름공간을 지정할 때 사용하는 연산자

```c++
#include <iostream>

namespace BestComImpl
{
	void SimpleFunc(void)
	{
		std::cout << "BestCom이 정의한 함수" << std::endl;
	}
}

namespace ProgComImpl
{
	void SimpleFunc(void)
	{
		std::cout << "ProgCom이 정의한 함수" << std::endl;
	}
}

int main(void)
{
	BestComImpl::SimpleFunc();
	ProgComImpl::SimpleFunc();
	return 0;
}
```
#### 이름공간 기반의 함수 선언과 정의의 구분
```C++
#include <iostream>

namespace BestComImpl
{
	void SimpleFunc(void);
}

namespace ProgComImpl
{
	void SimpleFunc(void);
}

int main(void)
{
	BestComImpl::SimpleFunc();
	ProgComImpl::SimpleFunc();
	return 0;
}

void BestComImpl::SimpleFunc(void)
{
	std::cout << "BestCom이 정의한 함수" << std::endl;
}

void ProgComImpl::SimpleFunc(void)
{
	std::cout << "ProgCom이 정의한 함수" << std::endl;
}
```
```C++
#include <iostream>

namespace BestComImpl
{
	void SimpleFunc(void);
}

namespace BestComImpl
{
	void PrettyFunc(void);
}

namespace ProgComImpl
{
	void SimpleFunc(void);
}

int main(void)
{
	BestComImpl::SimpleFunc();
	return 0;
}

void BestComImpl::SimpleFunc(void)
{
	std::cout << "BestCom이 정의한 함수" << std::endl;
	PrettyFunc();             // 동일 이름공간
	ProgComImpl::SimpleFunc(); // 다른 이름공간
}

void BestComImpl::PrettyFunc(void)
{
	std::cout << "So Pretty!" << std::endl;
}

void ProgComImpl::SimpleFunc(void)
{
	std::cout << "Progcom이 정의한 함수" << std::endl;
}
```

#### 이름공간의 중첩
- 이름공간안에 다른 이름공간이 삽입될 수 있음
```C++
namespace Parent
{
	int num=2;
	namespace SubOne
	{
		int num=3;
	}
	
	namespace SubTwo
	{
		int num=4;
	}
}

std::cout<<Parent::num<<std::endl;
std::cout<<Parent::SubOne::num<<std::endl;
std::cout<<Parent::SubTwo::num<<std::endl;
```
#### using을 이용한 이름공간의 명시
- 함수의 이름이든 변수의 이름이든 using을 써서 사용 가능
<br> `using <이름공간>::함수/변수 이름`
- 혹은, 이름공간만 적을 수도 있음 -> 이름공간에 선언된 모든 것에 대해 이름공간 지정의 생략 명령어
<br> `using namespace <이름공간>`
<br> 단, 이렇게 할 때는 이름충돌이 발생활 확률이 높아짐!!
- 지역변수의 선언과 유사함. 따라서 선언된 지역을 벗어나면 효력을 잃게 됨

```C++
#include <iostream>

namespace Hybrid
{
	void HybFunc(void)
	{
		std::cout << "So simple function!" << std::endl;
		std::cout << "In namespace Hybrid!" << std::endl;
	}
}

int main(void)
{
	using Hybrid::HybFunc;
	HybFunc();
	return 0;
}
```

```C++
#include <iostream>

using namespace std;

int main(void)
{
	int num = 20;
	cout << "Hello World!" << endl;
	cout << "Hello " << "World!" << endl;
	cout << num << ' ' << 'A';
	cout << ' ' << 3.14 << endl;
	return 0;
}
```
#### 이름공간의 별칭 지정
- 이름공간이 중첩되면서까지 과도하게 사용되었을 때, 별칭 사용
```C++
#include <iostream>

using namespace std;

namespace AAA
{
	namespace BBB
	{
		namespace CCC
		{
			int num1;
			int num2;
		}
	}
}

int main(void)
{
	AAA::BBB::CCC::num1 = 20;
	AAA::BBB::CCC::num2 = 30;

	namespace ABC = AAA::BBB::CCC;
	cout << ABC::num1 << endl;
	cout << ABC::num2 << endl;
	return 0;
}
```
#### 범위지정 연산자를 사용하면 전역변수에 접근 가능
```C++
int val=100; //전역변수
int SimpleFunc(void)
{
	int val=20; // 지역변수
	val+=30; // 지역변수 val의 값 3 증가
	::val+=7 // 전역변수 val의 값 7 증가
}
```
