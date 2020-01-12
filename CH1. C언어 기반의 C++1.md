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
