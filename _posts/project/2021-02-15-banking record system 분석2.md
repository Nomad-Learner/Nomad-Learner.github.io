---
layout: post
title:  "Banking Record System Project 해석2 "
subtitle:   "프로젝트 해석 2"
categories: project
tags: analyze project
comments: true
---

### - 4-2)  void account_query::read_rec() 본문
```c++
void account_query::read_rec()
{
    ifstream infile;
    infile.open("record.bank", ios::binary);
    if (!infile)
    {
        cout << "Error in Opening! File Not Found!!" << endl;
        return;
    }
    cout << "\n****Data from file****" << endl;
    while (!infile.eof())
    {
        if (infile.read(reinterpret_cast<char*>(this), sizeof(*this)))
        {
            show_data();
        }
    }
    infile.close();
}
```

1 ) ifstream infile;를 통해 ifstream의 인스턴스 infile 생성한다
2 ) ifstream에는 ios 클래스나 istream 클래스로부터 상속받지 않은 ifstream만 가지고 있는 7가지 public 멤버 함수가 있다.
  - constructor : 생성자
  - open()
  - is_open()
  - close()
  - rdbuf()
  - operator =()
  - swap()

==> 함수들의 기능과 생김새는 ofstream의 것과 완전히 동일하다.

<br/>

####     헷갈린 부분 1
```c++
ifstream infile;
infile.open("record.bank", ios::binary);
if (!infile)
{
    cout << "Error in Opening! File Not Found!!" << endl;
    return;
}
```

이 부분에서 if문이 헷갈렸다.
```c++
if (!infile)
```
<br/>
###   위에서 헷갈린 부분을 설명하기에 앞서 ios 클래스에서 정리할 부분

- ios 클래스에는 stream 상태를 관리하는 flags 4개가 있다.
- Stream에 있는 input/output 함수가 에러 신호를 보내면 내부적으로 에러 상태를 담당하는 플래그들이(error state flags) 자동적으로 set이 된다.
- stream의 상태를 감지하는 flags들 4종류는 goodbit, eofbit, failbit, badbit이다.
- flag들이 set이 되었는지 아닌지 감지하는 4종류의 함수와, 어떤 종류의 flag가 set되었는지 알려주는 함수 1개가 있다.
- <std::ios::good(), std::ios::eof(), std::ios::fail(), std::ios::bad()>, std::ios::rdstate()가 그것이다

<br/>
```c++
ifstream ifs("test.txt");
cout << "======state bit======" << endl;
cout << ifs.goodbit << endl;    // 0이 출력
cout << ifs.eofbit << endl;     // 1이 출력
cout << ifs.failbit << endl;    // 2가 출력
cout << ifs.badbit << endl;     // 4가 출력
cout << "=======function=======" << endl;
cout << std::boolalpha << ifs.good() << endl;      // false이 출력
cout << ifs.eof() << endl;                         // false이 출력
cout << ifs.fail() << endl;                        // true가 출력
cout << ifs.bad() << endl;                         // false이 출력
cout << ifs.rdstate() << endl;;                    // 2가 출력
```
<br/>

위 코드에서 goodbit, eofbit, failbit, badbit의 출력으로 보아

|2진수로 표현|10진수 값|flag 이름|설명|
|:------:|:---:|:---:|---|
|0 0 0|0|goodbit|에러 없음|
|0 0 1|1|eofbit|input 과정에서 파일의 끝에 도착|
|0 1 0|2|failbit|input/output 연산 과정에서 논리적인 오류가 발생했다|
|1 0 0|4|badbit|input/output 연산 과정에서 읽기/쓰기에 오류가 발생했다|

3개의 bit를 활용해서 4개의 상태를 표현하고 있는 것 같다.
.good(), .eof(), .fail(), .bad()는 위 3bit의 상태를 보고 true/ false를 판단하는 것 같다.
그리고 .rdstate()는 10진수로 error 상태가 어떤지를 알려준다. 위의 경우 없는 파일에 접근하려고 시도 했으므로 failbit가 set되었을 것이다.
이를 2진수로 표현하면 (0 1 0)이고 10진수로는 4이다. 따라서 cout << ifs.rdstate() << endl; 결과 2가 출력된다.
<br/>

```c++
// std::ios::good
bool ios::good() const {
    return rdstate() == goodbit;
}

// std::ios::eof
bool eof() const;

// std::ios::fail
bool fail() const;

// std::ios::bad
bool bad() const;

// std::ios::rdstate
iostate rdstate() const;

```
위 코드가 .good(), .eof(), .fail(), .bad(), .rdstate() 함수의 정의이다. .rdstate()를 제외하고 나머지 함수들은 반환형이 bool 타입이다. 따라서 error bit set 여부를 확인해서 true/false를 반환해준다.
.rdstate()의 경우 반환형이 iostate이다. iostate가 뭔지는 잘 모르겠지만 error bit의 set 상태를 int로 반환해준다.

<br/>
####   원래대로 돌아와서
```c++
infile.open("record.bank", ios::binary);
```
위 코드에서 .open() 함수는 open에 성공하면 goodbit를 set하고 open에 실패하면 failbit를 set 한다.
```c++
infile.open("record.bank", ios::binary);
if (!infile)
```
open에 의해 바뀐 infile의 error state flag를 if가 감지하니까 if(!infile)로 쓴 것 같다. if는 goodbit만을 감지하는 것인지 궁금하다.
논리상 (infile의 goodbit가 아니라면) == (에러가나면) 에러가 났다고 cout 하라는 문장이다. 표현이 너무 모호하게 느껴진다.
차라리
#####     1번

```c++
ifstream infile;
infile.open("record.bank", ios::binary);
if (infile.fail())
{
    cout << "Error in Opening! File Not Found!!" << endl;
    return;
}
```
#####      2번
```c++
ifstream infile;
infile.open("record.bank", ios::binary);
if (!infile.good())
{
    cout << "Error in Opening! File Not Found!!" << endl;
    return;
}
```
1번 혹은 2번처럼 함수로 표현하는 것이 더 명확했을 것이다.

<br/>
####     헷갈린 부분 2
```c++
cout << "\n****Data from file****" << endl;
while (!infile.eof())
{
    if (infile.read(reinterpret_cast<char*>(this), sizeof(*this)) )
    {
        show_data();
    }
}
infile.close();
```
이 코드에서

```c++
if (infile.read(reinterpret_cast<char*>(this), sizeof(*this)) )
{
    show_data();
}
```
이 부분이 헷갈렸다. while (!infile.eof())를 통해 파일이 끝날 때까지(eofbit가 set될 때까지) 계속 읽어 들인다.
여기서 read 함수는 3가지 error 조건에 따라 eofbit, failbit, badbit를 set한다.

-  eofbit : The function stopped extracting characters because the input sequence has no more characters available (end-of-file  reached).
-  failbit : Either the function could not extract n characters or the construction of sentry failed.
-  badbit :Error on stream (such as when this function catches an exception thrown by an internal operation),
When set, the integrity of the stream may have been affected.
출처 : cplusplus.com


infile.read(reinterpret_cast<char*>(this), sizeof(*this)) 코드를 통해 읽어 들이고     show_data();로 읽어들인 데이터를 보여준다.
이 과정을 while로 반복하다 읽어들일 내용이 없으면 read함수가 eofbit를 set한다. while (!infile.eof())에 따라 while문을 빠져나온다.
그 후 infile.close()로 파일을 닫아준다.


### - 4-3)  void account_query::search_rec() 본문
```c++
void account_query::search_rec()
{
    int n;
    ifstream infile;
    infile.open("record.bank", ios::binary);
    if (!infile)
    {
        cout << "\nError in opening! File Not Found!!" << endl;
        return;
    }
    infile.seekg(0, ios::end);
    int count = infile.tellg() / sizeof(*this);
    cout << "\n There are " << count << " record in the file";
    cout << "\n Enter Record Number to Search: ";
    cin >> n;
    infile.seekg((n - 1) * sizeof(*this));
    infile.read(reinterpret_cast<char*>(this), sizeof(*this));
    show_data();
}
```


####   search_rec()에서 헷갈리는 부분
- .seekg() 함수, .tellg() 함수

1 ) .seekg() 함수 : std::istream::seekg
```c++
istream& seekg (streampos pos);
istream& seekg (streamoff off, ios_base::seekdir way);
```
특징
- 오버로딩된 .seekg() 함수의 종류는 2가지이다.
- .seekg() 함수가 호출되기 이전에 eofbit가 set되어 있다면 efobit를 초기화시켜서 없애준다.

-매개변수가 한개인 .seekg()인 경우
- 파일에서 데이터가 있는 시작 지점으로부터 일정한 위치로 커서가 이동
- .seekg(시작으로부터 얼만큼 이동할지 넣기)
```c++
ex) infile.seekg(5); -> 커서가 맨 앞에서부터 5번째 뒤에 위치에 있는 상태
ex2) infile.seekg(0); -> 커서가 맨 앞에서부터 0번째 뒤에 위치에 있는 상태 == 맨 앞에 있는 상태
```
<br/>
-매개변수가 두 개인 .seekg()인 경우

- .seekg(얼만큼 이동할지, 기준점)
- 얼만큼 이동할지는 일반적으로 부호가 있는 정수라고 되어 있다. 즉 양수이면 기준점을 기준으로 뒤로가고, 음수이면 기준점을 기준으로 앞으로 간다는 뜻이다.
- ios_base::seekdir 자료형을 가지는 변수 way에 들어갈 수 있는 형태가 3가지 있다.

|쓸 수 있는 것|설명|
|------|-----|
|ios_base::beg|파일의 맨 처음에서 기준점이 시작|
|ios_base::cur|파일에서 현재 커서 위치에서 기준점이 시작|
|ios_base::end|파일의 가장 끝에서 기준점이 시작|


=> cplusplus.com에서는 stream이라는 표현을 사용했지만 일단 파일로 생각했다. ios_base :: 에서 시작하라고 나와 있지만
ios가 ios_base를 상속했으므로 본문 코드에서도 ios::end로 했다.

<br/>
```c++
infile.seekg(0, ios::end);
```
본문에서 이 코드는 커서를 파일의 가장 마지막 데이터가 있는 곳(쉽게 말해서 마지막 글자가 있는 곳)을 기준으로 0만큼 뒤에 떨어진 곳에 두라는 뜻.
즉 파일에서 가장 마지막 위치(마지막 글자가 있는 곳)에 두라는 뜻이다.


2 ) .tellg() 함수 : std::istream::tellg (.seekg() 함수와 같이 잘 쓰인다)
```c++
streampos tellg();
```

특징
- 매개변수가 없다.
- .seekg() 함수가 eofbit를 초기화시키면서 호출되는 반면에, .tellg() 함수는 호출되기 이전에 efobit flag가 set이 되어 있어도 작동한다.
- input stream에 들어가 있는(현재 ifstream의 객체와 연결된 파일에서) 커서의 현재 위치를 리턴해준다(파일의 앞에서부터 현재 커서가 있는 까지의 있는 글자수를 반환해준다.)
- 현재 커서 위치를 정수값으로 리턴해준다.
- 결과적으로 커서가 파일의 시작 위치에 있으면 0이고, 커서가 파일 끝으로 이동했으면 리턴된 값이 파일의 글자수와 같다.

<br/>
####   본문 코드 해석
```c++
infile.seekg(0, ios::end);
int count = infile.tellg() / sizeof(*this);
```
1 ) 파라미터가 2개인 seekg 함수를 사용해서 커서를 파일의 끝으로 이동시키고

2 ) *this는 search_rec() 함수를 호출한 객체 자신이다. 편의상 A라는 객체가 12byte라 가정하자(멤버 변수로 int 3개를 가지고 있다 가정)

2-1 ) ofstream을 통해서 A객체의 내용을 3번 바이너리 파일에 작성했다.

2-2 ) 파일안에는             (12byte | 12byte | 12byte) 이렇게 내용이 있을 것

2-3 ) seekg 함수를 통해 커서는                        | 여기에 위치

3 ) infile.tellg()를 통해서 커서의 현재 위치를 정수로 반환 받는다. 위 예시에서는 48이라는 정수값이 리턴

4 ) 이걸 sizeof(*this) 즉 A객체의 크기(12byte)로 나누면

5 ) 3이 나온다. 그리고 3은 파일에 적혀 있는 A객체 값의 개수이다.

6 ) 위 코드로 현재 파일에 몇개의 정보가 들어가 있는지 계산 한 것

<br/>
####   본문 코드 해석2
```c++
cout << "\n Enter Record Number to Search: ";
cin >> n;
infile.seekg((n - 1) * sizeof(*this));
infile.read(reinterpret_cast<char*>(this), sizeof(*this));
show_data();
```
1 ) 여기 코드는 검색을 account_number로 하지 않고 저장된 순서로 한다.

2 ) 위의 예시에서 파일에 12byte | 12byte | 12byte 이렇게 있는 상황에 n = 2를 입력했다 가정하자. 2번째 계좌를 보려는 것이 목적

```c++
infile.seekg((n - 1) * sizeof(*this));
```

3 ) 이 코드에서 커서를 첫번째 12byte의 마지막으로 이동시켜 놓는다. 12byte |(커서여기) 12byte | 12byte

4 ) std::istream::read의 read() 함수가 std::ostream::write이 write() 함수랑 설명이 약간 다르다.
```c++
istream& read (char* s, streamsize n);
ostream& write (const char* s, streamsize n);
```

4 -1 ) write 함수는 첫번째 파라미터로 stream에 연결된 파일에 쓸 내용을 가진 객체의 시작 주소를 담고, 두번째 파라미터로 그 주소(객체의 주소)에 접근해서몇 바이트 만큼 메모리를 긁어 올지 number of characters를 넣는다.

반면에 read 함수는 첫번째 파라미터로 파일에서 긁어온 정보를 저장할 수 있는 객체의 시작주소를 받고, 두 번째 파라미터로 파일에 접근해서 몇 바이트만큼의 정보를 파일로부터 긁어올 건지를 받는다.

5 ) 3번에서 커서를 두 번째 정보 시작점으로 옮겨 놓았다. read의 첫번째 파라미터로 A의 객체 주소를 넣어놨고, A 객체의 크기를 두 번째 파라미터로 넣어줬다(A객체의 크기만큼을 파일로부터 긁어오겠다고 지정한셈).
read 함수는 파일에 접근해서 -> 커서 위치부터 A 객체의 크기만큼(위에서 12byte로 가정) 12byte만큼 정보를 긁어온다 -> 첫번째 파라미터에 적혀 있던 주소로 접근해서 12byte를 입력해준다.

6) show_data() 함수 이용해서 파일로부터 긁어 온 정보를 콘솔에 출력해준다.
