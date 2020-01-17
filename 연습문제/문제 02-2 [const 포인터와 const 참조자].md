문제 02-2.

const int num=12;

포인터 변수를 선언해서 위 변수를 가리키게 하자.
그리고 이 포인터 변수를 참조하는 참조자를 하나 선언하자.
마지막으로 선언된 포인터 변수와 참조자를 이용해서 num에 저장된 값을 출력하자.

```C++
#include <iostream>

using namespace std;

int Ref(const int * (&ref))
{
	return *ref;
}

int main(void)
{
	const int num = 12;
	const int * ptr = &num;
	int numRef = Ref(ptr);

	cout << "num: " << num << endl;
	cout << "numRef: " << numRef << endl;
	return 0;
}

```
