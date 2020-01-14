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


### 01-2.함수 오버로딩(Function Overloading)
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

### 01-3.
