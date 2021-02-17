---
layout: post
title:  "Banking Record System Project 해석3 "
subtitle:   "프로젝트 해석 3"
categories: project
tags: analyze project
comments: true
---


### - 4-4)  void account_query::edit_rec() 본문
```c++
void account_query::edit_rec()
{
    int n;
    fstream iofile;
    iofile.open("record.bank", ios::in|ios::binary);
    if(!iofile)
    {
        cout<<"\nError in opening! File Not Found!!"<<endl;
        return;
    }
    iofile.seekg(0, ios::end); // 1번 부분
    int count = iofile.tellg()/sizeof(*this);
    cout<<"\n There are "<<count<<" record in the file";
    cout<<"\n Enter Record Number to edit: ";
    cin>>n;
    iofile.seekg((n-1)*sizeof(*this));
    iofile.read(reinterpret_cast<char*>(this), sizeof(*this));
    cout<<"Record "<<n<<" has following data"<<endl;
    show_data();
    iofile.close();
    iofile.open("record.bank", ios::out|ios::in|ios::binary); // 2번 부분
    iofile.seekp((n-1)*sizeof(*this));
    cout<<"\nEnter data to Modify "<<endl;
    read_data();
    iofile.write(reinterpret_cast<char*>(this), sizeof(*this));
}
```

- 이번에는 파일을 fstream 클래스를 이용해서 열었다. ifstream과 ofstream과 다르게 fstream으로 열면 읽고/쓰기를 다 할 수 있다.

```c++
iofile.open("record.bank", ios::in|ios::binary);
```
위 코드를 통해 .open() 함수에서 openmode를 지정할 때 | 를 이용해서 여러개를 지정할 수 있음을 알 수 있다. 파일을 읽어들이는 mode(in)과
파일을 바이너리 모드로 연다고 되어 있다.

<br/>

#### 1번 부분

```c++
iofile.seekg(0, ios::end); // 1번
int count = iofile.tellg()/sizeof(*this); // 2번
cout<<"\n There are "<<count<<" record in the file";
cout<<"\n Enter Record Number to edit: ";
cin>>n; // 3번
iofile.seekg((n-1)*sizeof(*this)); // 4번
iofile.read(reinterpret_cast<char*>(this), sizeof(*this)); // 5번
cout<<"Record "<<n<<" has following data"<<endl;
show_data(); // 6번
iofile.close(); // 7번
```
- 1번 : .seekg 함수를 이용해서 커서를 파일의 맨 끝으로 이동(파라미터 2개)
- 2번 : .tellg()함수를 이용해서 현재 커서의 위치를 구한다. 그 위치를  edit_rec() 함수를 호출한 객체(*this)의 크기로 나눠주면 이 파일에
몇개의 내용이 들어가 있는지 알 수 있다.
- 3번 : 이 함수도 수정할 내용을 찾을 때 account_number로 찾는 것이 아니고 파일의 몇번째에 입력 되었는지를 기준으로 검색한다. 파일에 3개의 정보가 입력되어 있다면, n = 2를 입력하면 2번째로 입력한 정보를 수정하는 방식이다.
- 4번 : .seekg 함수를 이용해서 커서를 앞에서부터 (n-1)*sizeof(*this) 만큼의 위치에 이동. 파일에 12byte||12byte||12byte 이렇게 정보가 있다하자(객체의 크기가 12byte이고 3번 파일에 입력을 했다 가정했을시). n = 2를 입력하면 1 * sizeof(*this) = 12byte가 된다. 커서는 두번째 12byte 맨 앞을 가리키고 있을 것이다.
- 5번 : .read 함수를 이용해서 sizeof(*this) (= 12byte) 만큼 파일에서 긁어와서 첫번째 파라미터로 넣어준 주소에 데이터를 입력해준다.
- 6번 : show_data()는 this -> show_data()이므로 위의 .read() 함수를 이용해서 파일에서 긁어온 데이터가 저장된 A 객체의 정보를 show_data()를 통해 콘솔로 출력한다
- 7번 : 파일을 닫아 준다

<br/>

#### 2번 부분

```c++
iofile.open("record.bank", ios::out|ios::in|ios::binary);
iofile.seekp((n-1)*sizeof(*this));
cout<<"\nEnter data to Modify "<<endl;
read_data();
iofile.write(reinterpret_cast<char*>(this), sizeof(*this));
```

####   .seekp() 함수 => std::ostream::seekp
```c++
(1)
ostream& seekp (streampos pos);
(2)
ostream& seekp (streamoff off, ios_base::seekdir way);
```
```c++
(1)
istream& seekg (streampos pos);
(2)
istream& seekg (streamoff off, ios_base::seekdir way);
```
출처 : cplusplus.com

위 코드가 seekp 선언이고, 아래가 seekg 선언이다. 반환형을 제외하면 전혀 차이가 없다. 둘 다 파라미터를 1개만 쓰면 파일에서 맨 앞을 기준으로 커서를 이동시켜주고, 파라미터를 2개 쓰면 way에 오는 값을 기준으로 off의 값이 양수면 뒤로, off의 값이 음수면 앞으로 커서를 이동시켜준다.
way에 올 수 있는 값도 동일하다.
차이점은 파일을 읽을 때 커서를 이동시키는 함수로 seekg를 쓰고, 파일에 쓸 때 커서를 이동시키는 함수로 seekp를 쓸 수 있다는 것 같다.


- 본문에서 iofile이 파일을 읽기/쓰기/바이너리 모드로 연다.

- 위에서 n = 2일 경우 iofile.seekp(12byte)에 의해 앞에서부터 12byte인 곳에 커서가 위치한다(2번째 자료가 시작하는 부분)

- 그 후 read.data() 함수를 호출하여 객체 A에 변경하고 싶은 정보를 입력한다.

```c++
iofile.write(reinterpret_cast<char*>(this), sizeof(*this));
```
- 마지막으로 .write() 함수를 이용해서 첫번째 파라미터로 입력한 주소에 접근해서(여기선 객체 A의 시작주소), 두 번째 파라미터에 입력한 크기만큼(여기선 객체 A의 크기만큼) 파일에 정보를 입력한다. 이 때 앞서 실행된 iofile.seekp((n-1)*sizeof(*this)); 이 코드의 역할로 커서가 2번째(내가 수정하고자 했던 정보) 정보 앞에 위치하므로 2번째 위치한 정보가 내가 새로 입력한 정보로 수정된다.

- 커서 이동 -> 수정할 정보 입력 받기(객체로 입력 받기) -> 이동한 커서부터 내용을 덮어쓰기



<br/>
### - 4-5)  void account_query::delete_rec() 본문
<br/>
```c++
void account_query::delete_rec()
{
    int n;
    ifstream infile;
    infile.open("record.bank", ios::binary);
    if(!infile)
    {
        cout<<"\nError in opening! File Not Found!!"<<endl;
        return;
    }
    infile.seekg(0,ios::end);
    int count = infile.tellg()/sizeof(*this);
    cout<<"\n There are "<<count<<" record in the file";
    cout<<"\n Enter Record Number to Delete: ";
    // .seekg() 랑 .tellg() 함수가 약간 형제처럼 사용되는 함수들.
    // .seekg(얼만큼 이동할지, 기준점) .seekg(0,ios::end);는 ios::end를 기준으로 0만큼 이동 즉 파일의 가장 끝으로 이동 - 매개변수 2개
    // .seekg(얼만큼 이동할지) --> 시작 지점으로부터 일정 위치로 워프 - 매개변수 1개
    // .tellg() -> 현재 위치를 정수값으로 리턴 : 시작 위치일 경우 0, 파일 끝으로 이동했으면 파일의 글자수가 리턴
    // file의 글자 수를 객체의 크기로 나눠서 객체 안에 몇개가 들어가 있는지 계산하는 코드


    cin>>n; // 이거 n이 순서다. account_number를 가지고 지우는게 아니라 파일에 저장된 순서를 가지고 지우게 되어있다.
    fstream tmpfile;
    tmpfile.open("tmpfile.bank", ios::out|ios::binary);

    // 중간에 거르는거 넣어보기
    if (tmpfile.fail())
    {
        cout << "\nError in opening! File Not Found!!" << endl;
        return;
    }

    infile.seekg(0); // infile의 커서를 파일의 맨 앞에 위치시킴

    for(int i=0; i<count; i++)
    {
        infile.read(reinterpret_cast<char*>(this),sizeof(*this)); // 왜 읽는건지?
        if(i==(n-1))
            continue; // 파일에 데이터가 (count = )5개가 적혀있고 내가 (n=)2번째걸 지운다하면 i == 1가 되면(2번째가 되면) 밑에 tmpfile.write을 실행 안하고 for문 처음으로 가버린다.
                      // 임시파일에 내가 지우려고 했던 내용이 적히지 않는 것
        tmpfile.write(reinterpret_cast<char*>(this), sizeof(*this));
    }
    infile.close();
    tmpfile.close();
    remove("record.bank"); // cstdio에 remove 함수가 존재한다. -> 글자 그대로 파일을 없앤다. 반환형은 int : 파일이 없어지면 int 0, 못 없애면 0이 아닌 값이 반환된다.
    rename("tmpfile.bank", "record.bank"); // rename은 왼쪽 글자를 -> 오른쪽 글자의 파일명으로 파일 명을 바꿔준다.
}
```

- 커서 이동하고, 파일 개수 구하는 과정은 동일

- 임시파일을 연다 -> 커서를 처음으로 이동시킨다 -> 원래 파일에서 정보를 읽는다 -> 단 내가 지우려고 했던 데이터는 continue를 통해 건너 뛰고 읽는다. -> 읽은 정보를 임시파일에 쓴다. :: 파일에서 정보를 삭제하는 방법이 내가 원하는 내용을 원래 파일에서 삭제하는 방식이 아니다. 대신 원래 파일에서 삭제하고 싶은 부분만 빼고 읽어서 임시파일에 다시 적는 방식. A | B | C -> A 읽고(원래파일) A 쓰고(임시파일) B 건너뛰고 C 읽고 C 쓰고 이 방식.

- 그 후에 remove와 rename을 이용해서 정보가 적힌 파일은 결국 1개만 존재하게 된다.


<br/>
####   1. remove() 함수 -> cstdio에 있는 함수
```c++
int remove ( const char * filename );
```
- 기능은 단순하다. filename과 동일한 이름을 찾아서 제거한다. 제거에 성공하면 int 0을 반환하고 파일 제거에 실패하면 0이 아닌 값을 반환한다.


#### 2. rename() 함수 -> cstdio에 있는 함수
```c++
int rename ( const char * oldname, const char * newname );
```

- 왼쪽 파라미터에 적힌 이름을(oldname) 오른쪽 파라미터에 적힌 이름(newname)으로 바꾼다. 파일명이 바뀌었으면 0을 반환한다. 파일명을 바꾸는데 실패하면 0이 아닌 값을 반환한다.

- 파일명을 바꾸는 연산(operation)은 파일에 직접적으로 작동한다고 되어 있다. streams는 연산에 포함되지 않는다는 설명이다.
