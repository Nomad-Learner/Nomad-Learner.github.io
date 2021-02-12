---
layout: post
title:  "effective C++ 정리2"
subtitle:   "const의 쓰임"
categories: study
tags: c++
comments: true
---


#포인터에서 const의 쓰임
<br/>
## 포인터 경우의 수 1 - 포인터 오른쪽에 오는 대상이 상수인 경우
```c++
#include <iostream>
using namespace std;

int main()
{
	const char* ptr = "Hello";
	char const* ptr2 = "Hello2";
	const char* pt = "world";
	ptr = pt;
	cout << ptr << endl;
	return 0;
}

```
=> 위 코드의 경우 포인터 오른쪽에 오는 대상이 상수인 경우이다. 즉 오른쪽에 오는 값은 변경할 수 없는 값임을 의미한다.
=> 단, 포인터가 자신의 바구니에 다른 주소를 넣을 수 있다.
=> ptr이 자신의 바구니에 "Hello"의 시작주소를 담았다.(편의상 10번지라 하자)
=> 하지만 ptr = pt를 통해 ptr은 자신의 바구니에 "world"의 시작주소(15번지라 하자)를 담고 있다(10번지 -> 15번지로 주소가 교체)
=> 따라서 ptr에 cout을 진행하면 Hello가 아닌 World가 출력된다.

<br/>
## 포인터 경우의 수 2 - 포인터 자체가 상수인 경우
```c++
#include <iostream>
using namespace std;

int main()
{
	char a[5] = { };
	char b[5] = { };
	char* const ptr3 = a;
	ptr3 = b;

	return 0;
}
```
=> 포인터는 '크기가 고정 된'  '주소를 담는' 바구니이다.
=> 배열 a가 10번지, 배열 b가 15번지부터 시작한다 하자
=> char* const ptr3 = a; 에 의해 ptr3에는 10번지가 들어가 있다.
=> 이때, 포인터 자체가 상수이므로 ptr3에는 10번지를 제외한 다른 주소가 들어올 수 없는 상태가 된다
=> 따라서 	ptr3 = b;가 실행되지 않고 오류가 발생한다(ptr3는 15번지를 담을 수 없기 때문)

<br/>

#일반 함수에서 const의 쓰임
<br/>
## 일반 함수 경우의 수 1 - 매개변수에 const가 있는 경우
```c++
#include <iostream>
using namespace std;

int Add(const int a)
{
	a += 1;
	return a;
}

int main()
{
	int a = 5;
	Add(a);

	return 0;
}

```
=> 매개변수에 const가 붙을 경우 함수 내부에서 인자로 받은 값을 변경할 수 없다
=> 따라서 a += 1; 같은 동작이 허용되지 않는다.

<br/>
## 일반 함수의 경우의 수 2 - 반환형에 const가 있는 경우
```c++
#include <iostream>
using namespace std;

const int Add(int a)
{
	return a;
}

int main()
{
	int a = 5;
	int b = Add(a);
	b++;
	return 0;
}
```
=> 반환형에 const가 붙으면 반환값을 바꿀 수 없다는 뜻이다.
=> 하지만 b++는 잘 작동한다. const형 반환값을 받았는데 왜 잘 작동하는가?
=> int b = Add(a);를 통해 5라는 반환값이 (복사)대입 된 후에 b++을 했기 때문이다.
=> b++가 실행되기 이전에 b에 대입될 때는 5로 대입되었다. 따라서 반환값 자체가 바뀌지 않았다는 점은 변함이 없다.


<br/>
```c++
#include <iostream>
using namespace std;

const int Add(int a)
{
	return a;
}

int main()
{
	int a = 5;
	int b = Add(a)++;
	b++;
	return 0;
}
```
=> 이런 경우에도 const 반환형이 영향을 끼치는 것이 아니다
=> 원래 return된 값이 r-value이기 때문에 반환형에 const가 없어도 "식이 수정할 수 있는 lvalue여야 합니다"라는 오류가 발생한다.

####=> 결국 const 리턴값은 일반적인 경우 아무 의미도 없다. 함수를 작성한 사람이 리턴값을 바꾸면 안된다는 의미를 전달하기 위해 작성한 것이다. const 반환형을 받는 변수도 되도록 const로 선언해주는 것이 좋다.(위에서는 const int b = Add(a))

####=>하지만 const int&, const int& 같이 반환형이 포인터나 레퍼런스일 때 const 반환형이 의미를 가진다.


<br/>
#class에서 const의 쓰임
<br/>

## class 멤버함수에 const가 있는 경우의 수 1 - 중간에 const
```c++
class Mage
{
public:

	void outAttack() const
	{
		//_attack = 60; // 1번
		cout << _attack << endl; // 2번
	}

	void outAttack()
	{

	}

public:

	int _attack = 50;
};

```
=> 멤버함수 outAttack()에 const가 붙어 상수 멤버 함수가 되었다. 상수 멤버함수가 되면 클래스의 멤버 변수를
멤버함수 안에서 수정할 수가 없다. 즉 1번의 경우 "식이 수정할 수 있어야 하는 lvalue여야 한다"라는 오류가 발생한다.
=> 상수 멤버 함수는 클래스의 멤버 변수를 자신의 함수 내에서 수정할 수 없다.
#### => const 키워드 존재 유무로 멤버 함수들은 오버로딩이 가능하다.
#### => 이런 성질을 이용해서 비상수 멤버함수는 상수 멤버함수의 캐스팅을 통해 구현해야 한다.(p68)

### cf) 상수 객체 <-> 상수 멤버함수는 서로가 필요하다. 상수 객체가 생기는 경우는 1) 상수 객체에 대한 포인터 2) 상수 객체에 대한 참조자로 객체가 전달될 때이다.

<br/>

## class 멤버함수에 const가 있는 경우의 수 2 - 반환형이 const
```c++
class Mage { };

Mage operator*(const Mage& m1, const Mage& m2);

int main()
{
	Mage m1, m2, m3;

	(m1 * m2) = m3;

	return 0;
}
```
=> operaotr*의 반환형이 const가 아닌 경우 (m1 * m2)에 의해 반환된 임시객체에 m3를 대입할 수 있다는 이상한 연산이 성립할 수 있다.
따라서 const Mage operator*(const Mage& m1, const Mage& m2);를 통해 상수 객체를 반환함으로써 대입 연산을 원천적으로 막아야 한다.
