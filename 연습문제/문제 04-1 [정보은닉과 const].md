```C++
#ifndef  __FRUITSELLER_H_
#define __FRUITSELLER_H_

class FruitSeller
{
private:
	int APPLE_PRICE; //사과의 가격 
	int numOfApples;
	int myMoney;
public:
	bool InitMembers(int price, int num, int money);
	int SaleApples(int money);
	void ShowSalesResult() const; 
};


#endif // ! __FRUITSELLER_H_

```

```C++
#include <iostream>
#include "FruitSeller.h"
using namespace std;

bool FruitSeller::InitMembers(int price, int num, int money) //굳이 bool로 만들필요는 딱히없음...
{
	if (price < 0 || num < 0 || money < 0)
	{
		cout << "사과의 구매를 목적으로 0보다 작은 수를 전달할 수 없다." << endl;
		return false;
	}

	APPLE_PRICE = price;
	numOfApples = num;
	myMoney = money;
	return true;
}

int FruitSeller::SaleApples(int money)
{
	if (money < 0)
	{
		cout << "음수의 돈이 들어와서 판매 취소" << endl;
		return 0;
	}
	int num = money / APPLE_PRICE;
	numOfApples -= num;
	myMoney += money;
	return num; // 판매한 과일의 수를 반환
}

void FruitSeller::ShowSalesResult() const
{
	cout << "남은 사과: " << numOfApples << endl;
	cout << "판매 수익: " << myMoney << endl;
}
```

```C++
#ifndef  __FRUITBUYER_H_
#define __FRUITBUYER_H_

#include "FruitSeller.h"

class FruitBuyer
{
private:
	int myMoney; 
	int numOfApples; 
public:
	bool InitMembers(int money);
	bool BuyApples(FruitSeller &seller, int money);
	void ShowBuyResult() const;
};

#endif // ! __FRUITBUYER_H_
```

```C++
#include <iostream>
#include "FruitBuyer.h"
using namespace std;

bool FruitBuyer::InitMembers(int money)  //굳이 bool로 만들필요는 딱히없음...
{
	if (money < 0)
	{
		cout << "돈이 부족" << endl;
		return false;  
	}
	myMoney = money;
	numOfApples = 0;
	return true;
}

bool FruitBuyer::BuyApples(FruitSeller &seller, int money) //굳이 bool로 만들필요는 딱히없음...
{
	if (myMoney < money)
	{
		cout << "돈이 부족해서 사과를 못삼" << endl;
		return false;
	}
	numOfApples += seller.SaleApples(money);
	myMoney -= money;
	return true;
}

void FruitBuyer::ShowBuyResult() const
{
	cout << "현재 잔액: " << myMoney << endl;
	cout << "사과 개수: " << numOfApples << endl;
}
```

```C++
#include <iostream>
#include "FruitSeller.h"
#include "FruitBuyer.h"
using namespace std;

int main(void)
{
	FruitSeller seller;
	seller.InitMembers(1000, 20, 0);
	FruitBuyer buyer;
	buyer.InitMembers(5000);
	buyer.BuyApples(seller, 2000);  //과일의 구매

	cout << "과일 판매자의 현황" << endl;
	seller.ShowSalesResult();
	cout << "과일 구매자의 현황" << endl;
	buyer.ShowBuyResult();
	return 0;
}
```
