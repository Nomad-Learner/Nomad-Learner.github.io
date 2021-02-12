---
layout: post
title:  "effective C++ 정리1"
subtitle:   "const, enum"
categories: study
tags: c++
comments: true
---


# 1. C++에서는 #define이 아니라 const, enum을 먼저 써야한다.

<br/>

## #define을 사용한 경우
```c++
#include<iostream>
using namespace std;

#define PI 3.141592

void Add(double a) { }
void Sub(double a) { }

int main()
{

	Add(PI);
	Sub(PI);

	return 0;
}
```
<br/>
## const를 사용한 경우

```c++
#include<iostream>
using namespace std;

void Add(double a) { }
void Sub(double a) { }

int main()
{
	const double pi = 3.141592;
	Add(pi);
	Sub(pi);
	return 0;
}

```

==> 상수가 부동소수점 실수 타입일 경우에는 컴파일을 거친 최종 코드의 크기가 #define이 const보다 더 크게 나올 수 있다.
const의 사본은 딱 한 번만 생기기 때문이다.

<br/>
## class에서 상수가 필요한 경우
```c++
#include <iostream>
using namespace std;

class Mage
{
public:
	static const int s_number = 5;
	int _number[s_number];

	static int s_attack;
};

int Mage::s_attack = 10;

int main()
{
	Mage m1;
	cout << m1.s_number << endl;
	cout << m1.s_attack << endl;

	return 0;
}

```

```c++
static const int s_number = 5;
int _number[s_number];
```
=> 이 코드가 class에서 상수를 선언하고 사용하는 방법이다. s_number의 유효범위는 Mage class 이다.
<br/>

```c++
	static int s_attack;
```
=> 이 코드는 클래스의 정적(static) 멤버로서 모든 객체가 이 정보를 공통적으로 가지고 있을 때 사용하기 유용하다.
const를 붙이지 않았기 때문에 class 안쪽에서 선언과 정의를 동시에 할 수가 없다.


<br/>
## class에서 상수가 선언과 동시에 정의되지 않을 때
위와 같은 방법이 통하지 않을 때는
헤더 파일에 선언만 하고
```c++
class Mage
{
public:
	static const int s_number;
};

```
실행 파일(.cpp)에 정의하면 된다.
```c++
const int Mage::s_number = 5;
```


<br/>
## enum을 이용해 class 상수 선언하기
```c++
#include <iostream>
using namespace std;

class Mage
{
public:
	enum { enum_number = 5};
	int _numbers[enum_number];
};

```
이런식으로 enum을 활용해서 static const int s_number = 5; 와 같은 효과를 낼 수 있다.
