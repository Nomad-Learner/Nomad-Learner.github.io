---
layout: post
title:  "인프런 어셈블리어 1"
subtitle:   "어셈블리어1"
categories: study
tags: assembly
comments: true
---

1. 어셈블리어 실행하는 IDE가 SASM이다

2. 우리가 쓰는 file은 section. text, section .data, section .bss등으로 구성되어 있다. 구글에 실행 파일 구조 검색하면 된다.
text section에 내가 만든 코드들이 들어가고, data section에 고정된 데이터 값들이 들어간다. exe 파일이라는게 저런 많은 정볼르 담고 있는 구조로 되어 있다.

3. 하드디스크에 File 구조로 설치 -> exe 파일 실행 -> 하드디스크의 file 내용을 전부 복사해서 메모리에 올림 -> CPU, 레지스터, 메인 메모리 사이를 왔다갔다하며 프로그램 실행

4. byte 구분 : (8bit = 1byte), (16bit = 2byte = 1word), (32bit = 4byte = 2word = 1 dword), (64bit = 8byte = 4word = 1 qword)

5. 어셈블리어에서 변수에 문자열 넣어줄 때, msg db "Hello world", 0x00 이런식으로 끝에 주소를 항상 0x00으로 설정해야 한다.

6. 어셈블리어에서 주로 쓰는 레지스터는 EAX, EBX, ECX, EDX이다.(레지스터 A,B,C,D가 있는 것)

7. 구글에 레지스터 rax 검색시 레지스터들이 어떻게 쪼개지는지 나온다.
- rax = a register의 64bit 전부 사용
- eax = a register의 32bit만 사용
- ax = a register의 16bit만  사용
- ah = a register의 8bit만  사용
- al = a register의 8bit만  사용
- 포함관계상 ax = ah + al 이런식으로 쪼개서 사용한다.     

8. 메모리에 데이터를 올리고 싶다면 section .data, section .bss 영역을 사용하면 편하다.

9. section .data의 특징
- 값이 초기화된 데이터가 들어간다
- 문법 : 변수이름 변수크기 초기값  ex) a db 0x11
- 쓸 수 있는 변수크기가 정해져 있다. db(define byte 1byte) dw(define word 2byte) dd(define double-word 4byte) dq(define quad-word 8byte)

10. section .bss의 특징
- 값이 초기화 되지 않은 데이터가 들어간다
- 개수를 정해주면 값이 모두 0으로 초기화 되서 메모리에 잡힌다
- 문법 : 변수이름 변수크기 개수 ex) e resb 10
- 약속된 변수크기 : resb(1byte), resw(2byte), resd(4byte), resq(8byte)

11. data 영역과 bss 영역의 구분 이유 :
- data 영역은 내가 초기화한 값이 전부 실행 file안에 저장되어 있다. 반면에 bss 영역은 데이터 내용없이 개수만 알면 0으로 초기화 된다. 실행 file에 따로 저장할 필요가 없다.
즉 실행 file의 크기를 줄일 수 있는 역할을 한다. 실행 file의 data 영역이 메모리에 올라갈 때 data 영역과 bss 영역으로 쪼개진다.

12. mov 명령어
- mov reg1, cst => cst 값을 reg1에 복사해라. 오른쪽 -> 왼쪽으로 읽는 것

13. 메모리(내가 잡은 메모리) <-> 레지스터 왔다 갔다 하는 법
- mov rax, a ==> a가 내가 잡은 메모리, rax가 레지스터. 이렇게 하면 변수 a의 값이 아니라 변수 a의 주소값을 rax에 저장하게 된다.

- mov rax, [a] ==> 변수 a가 가진 값을 rax에 저장하고 싶다면 [ ] 표시를 해줘야 한다. 이렇게 하면 rax의 크기에 맞춰 8byte를 a로부터 꺼낸다.
(rax가 8 byte이고 a에 1byte만 값이 있어도 7byte를 옆에서 꺼내서 rax에 넣어버린다)

- mov al, [a] ==> 처음부터 1byte짜리 레지스터에 값을 받는 것도 방법이다.

- mov [a], byte 0x55 ==> 변수에 숫자 넣을 때는 숫자의 크기를 명시해야 한다.

- mov rbx, 0x12345678 ==> 레지스터에 값을 넣을 때는 이미 크기를 아니까 크기 표시 안 해도 된다.

핵심 point
1. 우리가 잡아준 변수들의 공간이 메모리에 실질적으로 할당된다
2. mov 명령어를 이용해서 레지스터, 레지스터 <-> 메모리 사이에 값을 전달 할 수 있다
3. 메모리에 올라간 모든 것들은 주소를 가지고 있다.

14. 저장된 데이터는 2진수로 똑같이 표현되어 있다. 다만 저장된 데이터를 어떻게 해석하느냐의 차이
ex) 10로 저장된 걸 2진수로 봐서 2로 출력할 것인지 16진수로 봐서 16으로 출력한 것인지 이런 차이뿐이다.

15. b db 0x11, 0x12, 0x13, 0x14 => 이런식으로 데이터를 연속적으로 저장할 수도 있다.

16. 데이터를 연속적으로 저장할 때 Little-Endian(앞<-뒤) Big-Endian(앞->뒤 사람들이 쓰듯이) 엔디안 이슈가 있다.
- 리틀 엔디안은 intel 기반 프로세서에서 쓰인다. 캐스팅(데이터 사이즈 줄이는 거)에 유리하다
- 빅 엔디안은 숫자 비교에 유리하다(맨 앞에 주소에 저장된 값만 비교하면 되니까)

17. 어셈블리어의 연산들
- add a,b ( a = a + b , 오른쪽 <- 왼쪽으로 읽는 것)
- sub a,b ( a = a - b )
- mul reg ( 경우마다 레지스터가 미리 지정되어 있다.)
--> mul bl == al * bl 하고 연산결과는 ax에 저장
--> mul bx == ax * bx 하고 연산결과는 dx에 저장

- div reg (이것도 이미 레지스터가 지정되어 있다)
--> div bl == ax / bl 계산하고 연산결과는 al(몫) , ah(나머지)에 저장

18. shift 연산
- shl eax, 8 ; 왼쪽 쉬프트 8칸
- shr eax, 8 ; 오른쪽 쉬프트 8칸
- 장점 : 양수라는 전제하에 x2(왼쪽 쉬프트) // %2(오른쪽 쉬프트) 하기 쉽다
         게임서버에서 objectID를 만들어 줄 때 => 숫자를 이용해서 id에 정보(몬스터면 1 플레이어면 0)인지 끼워 넣을 때가 있다. 이때 정보를 비트 사이에서 움직일때 쉬프트 연산자 쓰면
         쉽고 빠르다

 19. 논리 연산
 - A xor B : 둘이 같으면 0, 다르면 1이 나온다.
 - xor 응용 : bitflag -> 비트에 의미 부여해서 정보 저장시 사용 가능
 - 똑같은 값을 이용해서 xor 2번 하면 원래 값이 나온다
 ex) xor al, bl
     xor al, bl
     xor al, bl
     PRINT_HEX 1, al 원래 al이 나오게 된다.
 - 암호학에서 유효(value xor key)
 원래 값 value에 key를 xor 시키고 key랑 다시 한 번 더 xor 시키면 원래값 value가 나오게 된다.

 - 자기 자신과 xor하면 항상 0이 나오게 된다. (xor 성질상 둘이 같으면 0이 되니까)
 - 0 대입시 0을 대입 안하고 xor a,a 이런식으로 자기 자신과 xor 시키는 pattern도 있다

 20. if문
 - 어셈블리어에는 명시적인 if문이 없다
 - CMP dst, src (dst가 기준이고 src가 비교 대상)
    ; 비교를 한 결과물을 Flag Register에 저장(이건 약속된 방법)
    ; JMP [label] 시리즈
    ; flag register를 기준으로 움직인다.
    ; JMP : 무조건 jump
    ; JE : JumpEquals 같으면 jump
    ; JNE : JumpNotEquals 다르면 jump
    ; JG : JumpGreater 크면 jump
    ; JGE : JumpGreaterEquals 크거나 같으면 jump
    ; JL
    ; JLE
 - 컴구에서 처럼 진짜로 라벨을 붙인다.

 21. 반복문
 - 어셈블리어에는 명시적인 반복문이 없다.

    mov ecx, 10 ;몇번 반복할건지 세는용

LABEL_LOOP:
    PRINT_STRING msg
    NEWLINE
    dec ecx ; sub ecx, 1과 동일하다 1만 줄이는 문법
    cmp ecx, 0
    jne LABEL_LOOP ; ecx가 0이 아니라면 == 아직 반복수가 남았다면 돌아라

    xor rax, rax
    ret

이런식으로 레지스터에 카운터를 뜻하는 값을 따로 저장하고 레이블을 이용해서 반복문을 구현한다.
