### 03-1. C++에서의 구조체

#### C++에서의 구조체 변수의 선언
- 다음과 같이 코드를 작성하면 함수들은 구조체 Car에 종속적이지만 다른 영역에서 이 함수를 호출할 수도 있음.

```C++
#include <iostream>
using namespace std;

#define ID_LEN 20
#define MAX_SPD	200
#define FUEL_STEP 2
#define ACC_STEP 10
#define BRK_STEP 10

struct Car
{
	char gameID[ID_LEN]; // 소유자 ID
	int fuelGauge; // 연료량
	int curSpeed; // 현재속도
};

void ShowCarState(const Car &car)
{
	cout << "소유자ID: " << car.gameID << endl;
	cout << "연료량: " << car.fuelGauge << "%" << endl;
	cout << "현재속도: " << car.curSpeed << "km/s" << endl << endl;
}

void Accel(Car &car)
{
	if (car.fuelGauge <= 0)
		return;
	else
		car.fuelGauge -= FUEL_STEP;

	if (car.curSpeed + ACC_STEP >= MAX_SPD)
	{
		car.curSpeed = MAX_SPD;
		return;
	}

	car.curSpeed += ACC_STEP;
}

void Break(Car &car)
{
	if (car.curSpeed < BRK_STEP)
	{
		car.curSpeed = 0;
		return;
	}

	car.curSpeed -= BRK_STEP;
}

int main(void)
{
	Car run99 = { "run99", 100, 0 };
	Accel(run99);
	Accel(run99);
	ShowCarState(run99);
	Break(run99);
	ShowCarState(run99);

	Car sped77 = { "sped77", 100, 0 };
	Accel(sped77);
	Break(sped77);
	ShowCarState(sped77);

	return 0;
}
```

#### 구조체 안에 함수 삽입하기
```C++
#include <iostream>
using namespace std;

#define ID_LEN 20
#define MAX_SPD	200
#define FUEL_STEP 2
#define ACC_STEP 10
#define BRK_STEP 10

struct Car
{
	char gameID[ID_LEN]; // 소유자 ID
	int fuelGauge; // 연료량
	int curSpeed; // 현재속도

	void ShowCarState()
	{
		cout << "소유자ID: " << gameID << endl;
		cout << "연료량: " << fuelGauge << "%" << endl;
		cout << "현재속도: " << curSpeed << "km/s" << endl << endl;
	}

	void Accel()
	{
		if (fuelGauge <= 0)
			return;
		else
			fuelGauge -= FUEL_STEP;

		if (curSpeed + ACC_STEP >= MAX_SPD)
		{
			curSpeed = MAX_SPD;
			return;
		}

		curSpeed += ACC_STEP;
	}

	void Break()
	{
		if (curSpeed < BRK_STEP)
		{
			curSpeed = 0;
			return;
		}

		curSpeed -= BRK_STEP;
	}
};

int main(void)
{
	Car run99 = { "run99", 100, 0 };
	run99.Accel();
	run99.Accel();
	run99.ShowCarState();
	run99.Break();
	run99.ShowCarState();

	Car sped77 = { "sped77", 100, 0 };
	sped77.Accel();
	sped77.Break();
	sped77.ShowCarState();

	return 0;
}
```
#### 구조체 안에 enum 상수의 선언
- 열거형 enum을 이용해서 구조체안에서만 유효한 상수를 정의할 수 있음.
- 이 방법대신 이름공간을 이용해서 몇몇 구조체들 사이에서만 사용하는 상수들을 선언할 수도 있음.

- 구조체 안에 모두 다 집어넣더니.. 구조체가 
```C++
#include <iostream>
using namespace std;

namespace CAR_CONST
{
	enum
	{
		ID_LEN = 20,
		MAX_SPD = 200,
		FUEL_STEP = 2,
		ACC_STEP = 10,
		BRK_STEP = 10
	};
}

struct Car
{
	char gameID[CAR_CONST::ID_LEN]; // 소유자 ID
	int fuelGauge; // 연료량
	int curSpeed; // 현재속도

	void ShowCarState()
	{
		cout << "소유자ID: " << gameID << endl;
		cout << "연료량: " << fuelGauge << "%" << endl;
		cout << "현재속도: " << curSpeed << "km/s" << endl << endl;
	}

	void Accel()
	{
		if (fuelGauge <= 0)
			return;
		else
			fuelGauge -= CAR_CONST::FUEL_STEP;

		if (curSpeed + CAR_CONST::ACC_STEP >= CAR_CONST::MAX_SPD)
		{
			curSpeed = CAR_CONST::MAX_SPD;
			return;
		}

		curSpeed += CAR_CONST::ACC_STEP;
	}

	void Break()
	{
		if (curSpeed < CAR_CONST::BRK_STEP)
		{
			curSpeed = 0;
			return;
		}

		curSpeed -= CAR_CONST::BRK_STEP;
	}
};

int main(void)
{
	Car run99 = { "run99", 100, 0 };
	run99.Accel();
	run99.Accel();
	run99.ShowCarState();
	run99.Break();
	run99.ShowCarState();

	Car sped77 = { "sped77", 100, 0 };
	sped77.Accel();
	sped77.Break();
	sped77.ShowCarState();

	return 0;
}
```

#### 함수는 외부로 뺄 수 있다.
- 함수의 원형선은을 구조체 안에 두고, 함수의 정의를 구조체 밖으로 빼낼 수 있음.
<br> 다만, 빼낸 다음에 해당 함수가 어디에 정의되어 있는지 정보만 추가해주면 됨.

```C++
#include <iostream>
using namespace std;

namespace CAR_CONST
{
	enum
	{
		ID_LEN = 20,
		MAX_SPD = 200,
		FUEL_STEP = 2,
		ACC_STEP = 10,
		BRK_STEP = 10
	};
}

struct Car
{
	char gameID[CAR_CONST::ID_LEN]; // 소유자 ID
	int fuelGauge; // 연료량
	int curSpeed; // 현재속도
	void ShowCarState();  // 상태정보 출력
	void Accel();  // 엑셀, 속도증가
	void Break();  // 브레이크, 속도감소
};

void Car::ShowCarState()
{
	cout << "소유자ID: " << gameID << endl;
	cout << "연료량: " << fuelGauge << "%" << endl;
	cout << "현재속도: " << curSpeed << "km/s" << endl << endl;
}

void Car::Accel()
{
	if (fuelGauge <= 0)
		return;
	else
		fuelGauge -= CAR_CONST::FUEL_STEP;

	if (curSpeed + CAR_CONST::ACC_STEP >= CAR_CONST::MAX_SPD)
	{
		curSpeed = CAR_CONST::MAX_SPD;
		return;
	}

	curSpeed += CAR_CONST::ACC_STEP;
}

void Car::Break()
{
	if (curSpeed < CAR_CONST::BRK_STEP)
	{
		curSpeed = 0;
		return;
	}

	curSpeed -= CAR_CONST::BRK_STEP;
}



int main(void)
{
	Car run99 = { "run99", 100, 0 };
	run99.Accel();
	run99.Accel();
	run99.ShowCarState();
	run99.Break();
	run99.ShowCarState();

	return 0;
}
```

- 사실, 구조체 안에 함수가 정의되어 있으면 함수를 인라인으로 처리하라는 것이 내포되어 있음
- 하지만, 함수를 구조체 밖으로 빼내면 의미가 사라짐
- 따라서, 인라인의 의미를 유지하려면 inline을 이용해서 인라인 처리를 명시적으로 지시해야 함
```C++
inline void Car::ShowCarState(){.....}
inline void Car::Accel(){.....}
inline void Car::Break(){.....}
```

### 03-2. 클래스(Class)와 객체(Object)
#### 클래스와 구조체의 유일한 차이점
- 접근제어 제시자를 선언하지 않았을 때. 클래스는 private으로, 구조체는 public으로 선언함.

#### 접근제어 지시자(접근제어 레이블)
- public : 어디서든 접근허용
- protected : 상속관계에 놓여있을 때, 유도 클래스에서의 접근허용
- private : 클래스 내(클래스 내에 정의된 함수)에서만 접근허용

```c++
#include <iostream>
#include <cstring>
using namespace std;

namespace CAR_CONST
{
	enum
	{
		ID_LEN = 20,
		MAX_SPD = 200,
		FUEL_STEP = 2,
		ACC_STEP = 10,
		BRK_STEP = 10
	};
}

class Car
{
private:
	char gameID[CAR_CONST::ID_LEN]; // 소유자 ID
	int fuelGauge; // 연료량
	int curSpeed; // 현재속도
public:
	void InitMembers(const char * ID, int fuel);
	void ShowCarState();  // 상태정보 출력
	void Accel();  // 엑셀, 속도증가
	void Break();  // 브레이크, 속도감소
};

void Car::InitMembers(const char * ID, int fuel)
{
	strcpy(gameID, ID);
	fuelGauge = fuel;
	curSpeed = 0;
}

void Car::ShowCarState()
{
	cout << "소유자ID: " << gameID << endl;
	cout << "연료량: " << fuelGauge << "%" << endl;
	cout << "현재속도: " << curSpeed << "km/s" << endl << endl;
}

void Car::Accel()
{
	if (fuelGauge <= 0)
		return;
	else
		fuelGauge -= CAR_CONST::FUEL_STEP;

	if (curSpeed + CAR_CONST::ACC_STEP >= CAR_CONST::MAX_SPD)
	{
		curSpeed = CAR_CONST::MAX_SPD;
		return;
	}

	curSpeed += CAR_CONST::ACC_STEP;
}

void Car::Break()
{
	if (curSpeed < CAR_CONST::BRK_STEP)
	{
		curSpeed = 0;
		return;
	}

	curSpeed -= CAR_CONST::BRK_STEP;
}

int main(void)
{
	Car run99;
	run99.InitMembers("run99", 100);
	run99.Accel();
	run99.Accel();
	run99.ShowCarState();
	run99.Break();
	run99.ShowCarState();

	return 0;
}
```

#### 알 수 있는 사실
- 접근제어 지시자 A가 선언되면, 그 이후에 등장하는 변수나 함수는 A에 해당하는 범위 내에서 접근 가능
- 그러나 새로운 접근제어 지시가 B가 선언되면, 그 이후로 등장하는 변수나 함수는 B에 해당하는 범위 내에서 접근 가능
- 함수의 정의를 클래스 밖으로 빼도, 이는 클래스의 일부이기 때문에, 함수 내에서는 private로 선언된 변수에 접근 가능
- 키워드 struct를 이용해서 정의한 구조체(클래스)에 선언된 변수와 함수에 별도의 접근제어 지시자를 선언하지 않으면, 
<br> 모든 변수와 함수는 public으로 선언됨
- 키워드 class를 이용해서 정의한 클래스에 선언된 변수와 함수에 별도의 접근제어 지시자를 선언하지 않으면,
<br> 모든 변수와 함수는 private으로 선언됨

#### 객체(object), 멤버변수, 멤버함수
- 객체
- 멤버변수 : 클래스 내에 선언된 변수
- 멤버함수 : 클래스 내에 정의된 함수
