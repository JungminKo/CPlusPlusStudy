문제 02-3.

typedef struct __Point
{
  int xpos;
  int ypost;
 } Point;
 
 위의 구조체를 기반으로 두 점의 합을 계산하는 함수를 다음의 형태로 정의하고, 
 
 Point& PntAdder(const Point &p1, const Point &p2);
 
 임의의 두 점을 선언하여, 위 함수를 이용한 덧셈연산을 진행하는 main 함수를 정의해보자.
 
 #### 내 코드
 ```C++
 #include <iostream>
#include <stdlib.h>

using namespace std;

typedef struct __Point
{
  int xpos;
  int ypos;
} Point;

Point& PntAdder(const Point &p1, const Point &p2)
{
	Point addpnt; // main함수에서 new로 할당하는게 아니라 여기서 할당해야 했음
	addpnt.xpos = p1.xpos + p2.xpos;
	addpnt.ypos = p1.ypos + p2.ypos;
	return addpnt;
}


int main(void)
{
	
	Point * p1 = new Point; 
	Point * p2 = new Point;
	p1->xpos = 1;
	p1->ypos = 2;
	p2->xpos = 3;
	p2->ypos = 4;

	Point * point = new Point;

	*point = PntAdder(*p1, *p2);

	delete(p1);
	delete(p2);
	delete(point);
	return 0;
}
 ```
 
 #### 답지 코드
 ```C++
 #include <iostream>
#include <stdlib.h>

using namespace std;

typedef struct __Point
{
  int xpos;
  int ypos;
} Point;

Point& PntAdder(const Point &p1, const Point &p2)
{
	Point * pptr=new Point;
	pptr->xpos = p1.xpos + p2.xpos;
	pptr->ypos = p1.ypos + p2.ypos;
	return * pptr;
}


int main(void)
{
	
	Point * p1 = new Point; 
	Point * p2 = new Point;
	p1->xpos = 1;
	p1->ypos = 2;
	p2->xpos = 3;
	p2->ypos = 4;

	Point &pref = PntAdder(*p1, *p2);
	cout << "[" << pref.xpos << ", " << pref.ypos << "]" << endl;

	delete(p1);
	delete(p2);
	delete(&pref);
	return 0;
}
 ```
