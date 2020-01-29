### 06-1. const와 관련해서 아직 못다한 이야기
#### const 객체와 const 객체의 특성들

- const함수는 const 멤버함수만 호출 가능
```c++
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num;
public:
	SoSimple(int n) :num(n)
	{}
	SoSimple& AddNum(int n)
	{
		num+=n;
		return *this;
	}
	void ShowData() const
	{
		cout << "num: " << num << endl;
	}
};
int main(void)
{
	const SoSimple obj(7);
	//obj.AddNum(20); //const 함수가 아니라서 호출 불가
	obj.ShowData();
	return 0;
}
```
