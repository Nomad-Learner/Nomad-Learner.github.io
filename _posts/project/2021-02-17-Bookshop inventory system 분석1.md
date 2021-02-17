---
layout: post
title:  "Bookshop inventory system 해석1 "
subtitle:   "프로젝트 해석 1"
categories: project
tags: analyze project
comments: true
---
##  원본 코드
```c++
// 출처 https://omkarnathsingh.wordpress.com/2016/02/09/c-program-for-book-shop/


#include<iostream>
#include<string.h>
#include<stdlib.h>

using namespace std;

class book {
private:
	char* author, * title, * publisher;
	float* price;
	int* stock;
public:
	book() {
		author = new char[20];
		title = new char[20];
		publisher = new char[20];
		price = new float;
		stock = new int;
	}
	void feeddata();
	void editdata();
	void showdata();
	int search(char[], char[]);
	void buybook();

};

void book::feeddata() {
	cin.ignore();
	cout << "\nEnter Author Name: ";      cin.getline(author, 20);
	cout << "Enter Title Name: ";       cin.getline(title, 20);
	cout << "Enter Publisher Name: ";   cin.getline(publisher, 20);
	cout << "Enter Price: ";            cin >> *price;
	cout << "Enter Stock Position: ";   cin >> *stock;

}

void book::editdata() {

	cout << "\nEnter Author Name: ";      cin.getline(author, 20);
	cout << "Enter Title Name: ";       cin.getline(title, 20);
	cout << "Enter Publisher Name: ";   cin.getline(publisher, 20);
	cout << "Enter Price: ";            cin >> *price;
	cout << "Enter Stock Position: ";   cin >> *stock;

}

void book::showdata() {
	cout << "\nAuthor Name: " << author;
	cout << "\nTitle Name: " << title;
	cout << "\nPublisher Name: " << publisher;
	cout << "\nPrice: " << *price;
	cout << "\nStock Position: " << *stock;

}

int book::search(char tbuy[20], char abuy[20]) {
	if (strcmp(tbuy, title) == 0 && strcmp(abuy, author) == 0)
		return 1;
	else return 0;

}

void book::buybook() {
	int count;
	cout << "\nEnter Number Of Books to buy: ";
	cin >> count;
	if (count <= *stock) {
		*stock = *stock - count;
		cout << "\nBooks Bought Sucessfully";
		cout << "\nAmount: Rs. " << (*price) * count;
	}
	else
		cout << "\nRequired Copies not in Stock";
}

int main() {
	book* B[20];
	int i = 0, r, t, choice;
	char titlebuy[20], authorbuy[20];
	while (1) {
		cout << "\n\n\t\tMENU"
			<< "\n1. Entry of New Book"
			<< "\n2. Buy Book"
			<< "\n3. Search For Book"
			<< "\n4. Edit Details Of Book"
			<< "\n5. Exit"
			<< "\n\nEnter your Choice: ";
		cin >> choice;

		switch (choice) {
		case 1:	B[i] = new book;
			B[i]->feeddata();
			i++;	break;

		case 2: cin.ignore();
			cout << "\nEnter Title Of Book: "; cin.getline(titlebuy, 20);
			cout << "Enter Author Of Book: ";  cin.getline(authorbuy, 20);
			for (t = 0; t < i; t++) {
				if (B[t]->search(titlebuy, authorbuy)) {
					B[t]->buybook();
					break;
				}
			}
			if (t == 1)
				cout << "\nThis Book is Not in Stock";

			break;
		case 3: cin.ignore();
			cout << "\nEnter Title Of Book: "; cin.getline(titlebuy, 20);
			cout << "Enter Author Of Book: ";  cin.getline(authorbuy, 20);

			for (t = 0; t < i; t++) {
				if (B[t]->search(titlebuy, authorbuy)) {
					cout << "\nBook Found Successfully";
					B[t]->showdata();
					break;
				}
			}
			if (t == i)
				cout << "\nThis Book is Not in Stock";
			break;

		case 4: cin.ignore();
			cout << "\nEnter Title Of Book: "; cin.getline(titlebuy, 20);
			cout << "Enter Author Of Book: ";  cin.getline(authorbuy, 20);

			for (t = 0; t < i; t++) {
				if (B[t]->search(titlebuy, authorbuy)) {
					cout << "\nBook Found Successfully";
					B[t]->editdata();
					break;
				}
			}
			if (t == i)
				cout << "\nThis Book is Not in Stock";
			break;

		case 5: exit(0);
		default: cout << "\nInvalid Choice Entered";

		}
	}


	return 0;
}
```

<br/>
<br/>
###    들어가기 앞서 - 배열에서 헷갈렸던 부분
```c++
int a[5]; // 1번
int* aPtr[5]; // 2번
```

- 배열은 같은 타입의 변수들로 이루어진 유한 집합이다.
- int a[5];는 배열안에 5개의 int형 데이터가 들어간다. |1번 int|2번 int|3번 int|4번 int|5번 int|
- int* aPtr[5]; 에는 뭐가 들어갈까?

####    포인터는 크기가 고정된(x86에서는 4byte, x64에서는 8byte) 주소를 저장하는 바구니이다
####    int* 의미 => (*의 의미 == 포인터입니다) => (int의 의미 == 포인터가 담고 있는 주소를 따라 가보면 int형 데이터가 있을 겁니다.)
##### int* aPtr[5]; => aPtr은 배열 -> 배열안에 뭐가 들어가? -> (*표시가 있으므로)포인터가 들어간다(==주소가 들어간다). -> 배열안에 들어가 있는 주소를 따라가 보면 int형 데이터가 있을 것이다.
##### |1번 주소|2번 주소|3번 주소|4번 주소|5번 주소| 이렇게 주소가 들어가 있다. 주소를 따라가 보면 int형 데이터가 있을 것이다.

<br/>
```c++
int* aptr[5];
int a1 = 1;
int a2 = 2;
int a3 = 3;
int a4 = 4;
int a5 = 5;
aptr[0] = &a1;
cout << aptr[0] << endl; // a1의 주소가 나오고
cout << *aptr << endl; //  a1의 주소가 나오고
cout << *aptr[0] << endl; // a1의 값이 나온다
```
- aptr에는 |a1의주소|a2의주소|a3의주소|a4의주소|a5의주소| 가 들어가 있다.
- *aptr[0] 하면 *(a1의 주소) => a1의 주소를 타고 슝 가면 a1의 값이 나온다.
<br/>
```c++
book* B[20];
```


####  본문의 main에서 위와 같이 선언했다는 것은 B라는 배열에 주소가 20개 들어간다. "그 주소를 따라가보면 자료형이 book인 데이터가 나올 것이다" 라고 선언해 준 것. 동적할당과는 관계없이 주소가 들어가는 배열을 선언해 준 것 뿐이다.
####  int* a = new int[30]; delete[] a; 이거랑은 다른 상황이다

<br/>
#### 본문시작 -> class 선언부
```c++
class book {
private:
	char* author, * title, * publisher;
	float* price;
	int* stock;
public:
	book() {
		author = new char[20];
		title = new char[20];
		publisher = new char[20];
		price = new float;
		stock = new int;
	}
	void feeddata();
	void editdata();
	void showdata();
	int search(char[], char[]);
	void buybook();

};
```

- private로 멤버 변수 잠궜다. 포인터로 멤버변수들을 선언했다.
- book() 생성자에서 멤버 변수들을 동적할당 받았다.
- 멤버함수들 선언했다.
- 아쉬운 점은 소멸자도 정의해서 컴파일러에게 맡기는 것이 아닌 동적할당 받은 것을 소멸시켜주는게 좋지 않았나 하는 생각이든다.

#####  수정본

```c++
class book {
private:
	char* author, * title, * publisher;
	float* price;
	int* stock;
public:
	book() {
		author = new char[20];
		title = new char[20];
		publisher = new char[20];
		price = new float;
		stock = new int;
	}

	~book()
	{
		delete[] author;  // 배열로 할당 받은 것은 delete[]
		delete[] title;
		delete[] publisher;
		delete price; // 변수 한개로 할당 받은 것은 delete
		delete stock;
	}

	void feeddata();
	void editdata();
	void showdata();
	int search(char[], char[]);
	void buybook();
};
```


- (new - delete), (new [ ] - delete [ ]) 한 쌍의 pair을 이룬다는 것을 기억하자.

<br/>
<br/>

####     멤버함수 1 -> 콘솔에서 입력 받는부분. feeddata()
```c++
void book::feeddata() {
	cin.ignore();
	cout << "\nEnter Author Name: ";      cin.getline(author, 20);
	cout << "Enter Title Name: ";       cin.getline(title, 20);
	cout << "Enter Publisher Name: ";   cin.getline(publisher, 20);
	cout << "Enter Price: ";            cin >> *price;
	cout << "Enter Stock Position: ";   cin >> *stock;
}
```
<br/>

###### 특징적인 부분 1 .ignore() 함수 사용 -> std::istream::ignore


```c++
istream& ignore (streamsize n = 1, int delim = EOF);
```


- 기능 : 내가 입력한 값(input sequence)에서 문자들을(characters) 추출해서 제거하는 용도로 쓰는 함수이다.
- delim의 뜻은 구분 문자라는 뜻이다. 입력한 값 중에서 내가 지정한 구분 문자를 만날때 까지 n개의 문자들을 추출해서 제거하겠다는 함수이다.
- 디폴트 값으로 EOF를 만날 때까지 1개의 문자를 제거하도록 되어있다.
- n 값에는 제한이 없다고 되어 있다. c++ 내부적으로 n 값이 numeric_limits< streamsize >::max() 까지 허용된다고 하니 사실상 제한이 없다는 표현이 쓰였다.
- delim의 자료형이 int인 부분이 궁금할 수 있다. 구분문자로 쓰이는 것들이 아스키 코드에서 int로 정의되어 있기 때문에 구분 문자의 아스키 코드를 인식하기 때문에 delim의 자료형이 int인 것으로 추정된다.
- 본문에서는 cin.ignore()로 ignore함수를 쓸 때 디폴트 값을 사용했다. 아마 값을 입력하고 버퍼에 남아있는 '/n'(엔터키)을 제거하기 위해서 함수의 맨 앞에 cin.ignore() 함수를 쓴 것 같다.


<br/>
```c++
// 출처 cplusplus.com
#include <iostream>     // std::cin, std::cout

int main () {
  char first, last;

  std::cout << "Please, enter your first name followed by your surname: ";

  first = std::cin.get();     // get one character
  std::cin.ignore(256,' ');   // ignore until space

  last = std::cin.get();      // get one character

  std::cout << "Your initials are " << first << last << '\n';

  return 0;
}
```
```
Please, enter your first name followed by your surname: John Smith
Your initials are JS
```
==> 위 코드에 John Smith를 치면 JS라고 나온다고 한다. J가 cin.get()를 통해 first에 들어가고 cin.ignore()에 의해 ohn이 제거되어서 그렇다. John과 Smith 사이에 스페이스 ' '가 있는데 이  스페이스 ' '를 만나기 전까지 만나는 256개의 문자를 입력 sequence에서 제거한다는 뜻이다. 만약 입력을 Johnfkldfjlkjasdflk Smith라고 적어도 JS라는 출력이 정상적으로 나온다. 스페이스를 만나기 전에 입력되었던 ohnfkldfjlkjasdflk이 문자들이 입력에서 모두 제거되기 때문이다.


<br/>
```c++
int main () {
  char first, last;

  std::cout << "Please, enter your first name followed by your surname: ";

  first = std::cin.get();     // get one character
  std::cin.ignore(2,' ');   // ignore until space

  last = std::cin.get();      // get one character

  std::cout << "Your initials are " << first << last << '\n';

  return 0;
}
```
```
Please, enter your first name followed by your surname: John Smith
Your initials are Jn
```
=> 반면에 ignore안에 무시할 값을 2로 설정하고 John Smith를 친다면 결과값은 Jn이 나오게 된다.
cin.get()으로 J를 first에 입력받고,   std::cin.ignore(2,' ');에 의해 스페이스를 만나기 전까지 입력된 값중 2개의 문자를 제거한다.
이때, 스페이스를 만나기전 ohn가 입력되었으므로 앞에서부터 2개를 제거하면 n이 남는다. 남은 n이 last에 들어가
Jn이 출력된다.

=> 결국 .ignore()안에 첫번째로 넣는 무시할 문자들의 개수는 적당히 큰 값을 넣어주면 된다.

<br/>
###### 특징적인 부분 2 .getline() 함수 사용 -> std::istream::getline  != std::getline (string) 와는 다르다


```c++
istream& getline (char* s, streamsize n );
istream& getline (char* s, streamsize n, char delim );
```

- getline 함수는 2종류의 오버로딩이 되어있다.

- 파라미터 s는 입력받은 문자들 중 n개만큼 추출한 문자들의 array를(여기선 배열보다 연속된 문자들이라는 의미로 쓰인 것 같다) 저장할 공간의 주소(Pointer)를 뜻한다. 쉽게 말해서 내가 입력한 문자들이 저장될 장소의 주소를 적으면 된다. 자료형은 char*이다. 저장하는 형태는 C-string 형태로 저장한다.

- 파라미터 n은 파라미터 s가 알려주는 주소에 가서 몇개까지 문자들을 넣을지 정해주는 값이다. cin을 통해 입력장치에서 스트림으로 입력받은 문자들 중 n개 만큼을 s가 알려주는 주소에 가서 저장하겠다는 뜻이다.

- 파라미터 delim은 제한자 역할을 한다. 연속적으로 문자들이 추출되다가 이 제한자를 만나면 멈춘다. 제한자는 파라미터 s가 지정하는 주소에 가서 저장되지 않으며 버려진다. 파라미터를 입력하지 않았을시 엔터 '\n'를 제한자로 정한다.(파라미터를 2개만 입력했을시). 파라미터를 3개를 입력해서 내가 임의로 정하면 임의로 정한 구분 문자가 제한자가 된다.


#####    수정본 2


```c++
class book {
private:
	char* author, * title, * publisher;
	float* price;
	int* stock;
	static const int SIZE = 20;
public:
	book() {
		author = new char[SIZE];
		title = new char[SIZE];
		publisher = new char[SIZE];
		price = new float;
		stock = new int;
	}

	~book()
	{
		delete[] author;
		delete[] title;
		delete[] publisher;
		delete price;
		delete stock;
	}

	void feeddata();
	void editdata();
	void showdata();
	int search(char[], char[]);
	void buybook();
};

void book::feeddata() {
	cin.ignore();
	cout << "\nEnter Author Name: ";      cin.getline(author, SIZE);
	cout << "Enter Title Name: ";       cin.getline(title, SIZE);
	cout << "Enter Publisher Name: ";   cin.getline(publisher, SIZE);
	cout << "Enter Price: ";            cin >> *price;
	cout << "Enter Stock Position: ";   cin >> *stock;

}
```


==> 본문에서는 동적배열을 할 때나 .getline()을 할 때 값을 20으로 하드 코딩했었다.
하지만 class만의 상수로 static const 변수를 선언해서 사용하는 것이 더 좋다고 생각한다.


==> editdata() 멤버함수나, showdata() 멤버함수나 원리는 동일하다.


<br/>

####     멤버함수 2 -> 자료 검색하는 부분 .search()

```c++
int book::search(char tbuy[20], char abuy[20]) {
	if (strcmp(tbuy, title) == 0 && strcmp(abuy, author) == 0)
		return 1;
	else return 0;

}
```

<br/>
###### 특징적인 부분 1 .strcmp() 함수 사용



```c++
int strcmp ( const char * str1, const char * str2 );
```


=> 2개의 문자열을 비교하는 함수. C-string의 문자열을 비교한다. istream에 있는 getline이 입력받은 문자들을 C-string으로 처리해서 여기서도 strcmp 함수를 쓴 것 같다.

=> 처음부터 끝까지 문자열 하나하나를 비교한다. 둘 문자열이 다른 것을 발견 할때까지 혹은 '\0'(문자열의 끝에는 널이 항상 있으므로)에 도달할 때까지 비교한다.

=> 두 문자열이 동일하면 0을 반환한다. 반환값이  > 0 일때와 < 0 일때 차이가 생기지만 크게 쓰지 않는다. != 0 이 가장 많이 쓰이는 형태

=> title 하고 author는 내가 이미 자료에 넣어논 값, tbuy[20], abuy[20] 배열 사용해서 값 입력 받아서 비교하는 형태


<br/>

####     멤버함수 3 -> 책 파는 부분 .buybook()


```c++
void book::buybook() {
	int count;
	cout << "\nEnter Number Of Books to buy: ";
	cin >> count;
	if (count <= *stock) {
		*stock = *stock - count;
		cout << "\nBooks Bought Sucessfully";
		cout << "\nAmount: Rs. " << (*price) * count;
	}
	else
		cout << "\nRequired Copies not in Stock";
}
```


=> buybook() 멤버함수 자체는 굉장히 단순하게 되어 있다. feeddata() 멤버함수에서 책 정보를 입력할 때 책 재고량(stock)을 입력한다. 여기서 내가 주문할 권수 count로 입력 받고 재고량(*stock -> stock 변수 자체는 지금 포인터로 선언)이랑 비교해서 빼주는 역할이 끝.


<br/>
<br/>

####   메인함수 해석


```c++
book* B[20]; // 자료형이 book인 것들 주소를 20개 저장가능한 배열.
int i = 0, r, t, choice;
char titlebuy[20], authorbuy[20]; // 책 찾을 때 검색할 title하고 author 저장할 배열
```

<br/>
###### 내가 추가한 부분 cin 함수오류 제거


```c++
// 추가
if (cin.fail())
{
  cin.clear();
  cin.ignore(1026,'\n');
}
```

1 ) cin이 입력장치에서 입력스트림으로 들어온 값을 변수에 넣는데 실패할 경우 error state flags에서 eofbit가 set이 된다.
이걸 .fail() 함수를 이용해서 감지한다. cin.fail()이 true == failbit가 set == 입력 실패

2 ) 입력 실패가 되면 failbit가 set된대로 유지가 된다. 따라서 .clear() 함수(std::ios::clear)로 모든 error state flags를 초기화 시켜준다.

3) .ignore() 함수를 이용해서 엔터('\n')가 나올 때까지 입력 받은 1024 streamsize를 무시한다. 본문에서  choice는 int이다.
내가 정수가 아닌 다른 값(abcde)를 입력하고 엔터를 입력했다면 cin 내부에는 abcde\n이 입력되어 있고 failbit는 set 되어 있을 것이다.
이를 .ignore() 함수를 이용해서 abcde를 내부에서 제거해 주는 것이다. cin은 입력장치로부터 새로운 값을 입력 받을 수 있게 된다.


<br/>
###### 추가 해석

```c++
switch (choice) {
case 1:	B[i] = new book;
  B[i]->feeddata();
  i++;	break;
```


- 1번을 고르면 배열 B의 i번째에 book 객체 주소가 들어간다. 정확히 말하면 동적할당을 통해 메모리의 힙 영역에 book 객체가 잡히고 그 객체의 시작주소가 배열 B의 i번째에 들어가게 된다.

- (-> 연산자)를 활용해서 주소->멤버함수 접근을 하고 있다.

- 그 이후에 i++을 해서 B 배열의 index를 증가시켜준다.


- 끝으로 switch 문을 썼는데, 시작하는 부분에 항상 cin.ignore()가 있다. 아마 콘솔에서 2번 치고 엔터를 치는데
cin의 내부에 남아있는 '\n'를 제거하기 위해서 적어준 것 같다.
