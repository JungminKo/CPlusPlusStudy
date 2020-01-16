문제1. 참조자를 이용해서 다음 요구사항에 부합하는 함수를 각각 정의하여라.
-> 인자로 전달된 int형 변수의 값을 1씩 증가시키는 함수
-> 인자로 전달된 int형 변수의 부호를 바꾸는 함수

그리고 위의 각 함수를 호출하여 그 결과를 확인하는 main 함수까지 작성하여라.

```C++
#include <iostream>

using namespace std;

void SwapByRefPlusOne(int &ref1)
{
	ref1 += 1;  // 답지에는 ref1++;
}

void SwapByRefSign(int &ref1)
{
	ref1 = -ref1;  // 답지에는 num*=-1;
}

int main(void)
{
	int val = 10;

	SwapByRefPlusOne(val);
	cout << "IncreaseOne: " << val << endl;

	SwapByRefSign(val);
	cout << "ChangeSign: " << val << endl;

	return 0;
}
```

문제3. SwapPointer함수 정의하기.
- 가리키는 대상이 바뀌도록 SwapPointer 함수 정의하기
```C++
#include <iostream>

using namespace std;

void SwapPointer(int *(&pre1), int *(&pre2))
{
	int * ptr = pre1;
	pre1 = pre2;
	pre2 = ptr;
}

int main(void)
{
	int num1 = 5;
	int *ptr1 = &num1;
	int num2 = 10;
	int *ptr2 = &num2;

	SwapPointer(ptr1, ptr2);

	return 0;
}
```
