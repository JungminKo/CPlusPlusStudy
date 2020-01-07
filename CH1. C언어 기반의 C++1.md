## Part 01. C++로의 전환
# Chapter01.  C언어 기반의 C++ 1
01-1. printf와 scanf를 대신하는 입출력 방식

```C++
# include <iostream>

int main(void)
{
  int num=20;
  std::cout<<"Hello World!"<<std::endl;
  std::cout<<"Hello "<<"Word!"<<std::endl;
  std::cout<<num<<' '<<'A';
  std::cout<<' '<<3.14<<std::endl;
  return0;
}
```

<주의 깊게 봐야할 것>
- 헤더파일 선언문 #include <iostream>
  <br> - iostream     vs               iostream.h 
  <br> 새로운 표준 라이브러리 및 헤더 의미 vs 과거의 표준 입출력 라이브러리 및 헤더 의미
- std::cout과 <<을 이용한 출력
- std::endl을 이용한 개행
