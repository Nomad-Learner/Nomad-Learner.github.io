---
layout: post
title:  "인프런 어셈블리어 2"
subtitle:   "어셈블리어2"
categories: study
tags: assembly
comments: true
---

1. xor eax eax ; mov eax, 0과 동일한 뜻 값이 0이 들어간다

2. add eax, 1 ; 1 더하는 명령어 중에 inc 명령어도 있다.

3. Loop문을 돌 때 ecx 같이 레지스터 하나를 정해서 루프를 몇번 돌았는지 미리 정해서 LOOP를 돈다.

4. 일반적인 반복문과 똑같이 키워드 중에 loop라는 키워드가 있다.
    ; loop [라벨]
    ; ecx에 반복 횟수가 들어가고(약속이 된거다 이것도)
    ; loop 할때마다 ecx 1 감소, ecx가 0이되면 빠져나감 아니면 라벨로 이동

    mov ecx, 100
    xor ebx, ebx ; ebx를 0으로 바꾸고 결과물을 저장

LABEL_LOOP_SUM:
    ;loop 안에서 ecx 값을 바꾸면 무한 루프에 빠져버릴 수 있다.
    add ebx, ecx
    loop LABEL_LOOP_SUM ; 내부적으로 ecx를 1 줄이를 과정이 포함되어 있다.

    add ebx. ecx ; 이건 100부터 1까지 합 구하려고 내가 넣은 어셈블리어

5. section 10에서 알 수 있었던 것들 : 배열과 주소에 관한 내용
    ; 배열과 주소

    ; 배열 : 동일한 타입의 데이터 묶음
    ; 배열을 구성하는 각 값을 배열 요소(element)라고 함
    ; 배열의 위치를 가리키는 숫자를 인덱스(index)라고 함

    ;주소
    ;[시작 주소 + 인덱스 * 크기 ] 아주 빈번한 접근법

    또다른 것

     section .data
    a db 0x01, 0x02, 0x03, 0x04, 0x05 ;이런식으로 나타내는 것도 배열의 일종 5 * 1 = 5byte

    b times 5 dw 1 ; b라는 이름의 배열은 data가 5개 있고(times) 각 데이터의 크기는 2byte다
                   ; 초기값은 1이다. 5 * 2 = 10byte 배열
   ;b 볼때 2byte니까 두 개씩 묶어서 봐야 한다.
   ;b의 경우 0x1, 0x0인데 인텔기반 프로세서에서는 거꾸로봐야 하니까
   ;0x0001로 봐야한다.
   ;type을 w(word)로 바꾸면 자동으로 한 개로 합쳐준다.
   ;b(byte)로 봐야 어떻게 들어가나 볼 수 있어서 좋다.
   ;메모리찍어보면 a 뒤에 b 내용이 있다. 즉 메모리가 연속으로 할당됨을 의미
   ;배열의 변수명은 배열의 데이터가 시작하는 첫번째 위치의 주소값이다.   
   ;a는 0x01이 저장되어 있는 곳의 주소를 가리킨다는 뜻.     


    배열에서 element 값 꺼낼 때 [ ] 이걸 쓴다는 것과

        PRINT_HEX 1, [a+ecx]
    ;배열이나 class도 어셈블리어로 까면 이렇게 주소를 기반으로 덧셈을 해서 표현되어 있다. ==> 주소 기반 덧셈을 하고 있다는 점


    6. F10과 F11의 차이

    -;함수 (어셈블리어에서는 프로시저 procedure or 서브루틴 subroutine) 이라한다.
    ;main도 일종의 함수이다

    - call PRINT_MSG
    ; call 명령어를 통해 함수를 호출한다.
    ; 디버깅 할 때 f5 눌러서 디버깅 들어가고 f10 누르면 함수 안으로 들어가지는 않고
    ; 함수 실행 결과만 알려준다.(f5 한 번더 누른 경우에는 break point까지 한 번에 들어가고)
    ; 반대로 f11을 눌르면 step into 해서 함수 안으로 들어간다.
    ; 함수안에 들어가서도 f11 누르면서 함수 작동하는거 보면된다.

    - ret ; ret 명령어를 만나면 마지막으로 내가 call 했던 곳으로 돌아간다.

    - 함수는 stack 이라는 메모리 구조를 사용한다. 함수가 호출되면 "스택 프레임" 이라는 특별한 구조가 형성된다.

    - ; [!] 스택(stack)이라는 메모리 영역을 이용
      ; 함수가 사용하는 일종의 메모장
      ; - 매개 변수 전달
      ; - 돌아갈 주소 관리


      7. Stack 구조에 관련된 내용들

      - mov rbp, rsp
    ;이 rbp, rsp가 base pointer와 stack pointer를 의미 r이 붙은건
    ; 64bit로 연산을 한다는 의미

    -     ; 스택 메모리, 스택 프레임

    ; 구글에 스택 메모리 치면 이미지 많이 나온다

    ; exe 파일 실행 -> 복사되서 메모리(RAM)에 올라감 -> RAM에 있는 데이터가 여러가지 영역으로 구분
    ; -> CODE영역은 진짜 내가 작성한 코드가 2진어(기계어)로 번역되어 들어가 있고
    ; -> DATA, BSS 영역은 전역변수 영역
    ; -> HEAP(동적할당 영역), STACK(지역변수 영역)

    ; 구글에 스택 프레임 검색
    ; 스택은 높은 주소(숫자 큰거) -> 낮은 주소(숫자 작은거) 순으로 채워진다.

    ; 레지스터는 다양한 용도로 사용
    ; - a b c d 범용 레지스터
    ; - 포인터 레지스터 (포인터 = 위치를 가리키는~)
    ; -- ip (Instruction Pointer) : 다음 수행 명령어의 위치
    ; -- sp (Stack Pointer) : 현재 스택 top 위치 (일종의 cursor)
    ; -- bp (Base Pointer) : 스택 상대주소 계산용

    ; rsp 레지스터 보면 현재 스택의 위치를 알 수 있다.\
    ; 처음 프로그램을 실행할 때 한번에 크게 스택 메모리 할당 받고
    ; 미리 할당 받은 스택에서 알아서 쪼개서 쓴다.


    - ; 스택에서 pop한다고 메모리가 변화하지는 않는다.
    ; 그냥 쪼개서 쓰는 유효범위가 왔다갔다 하는거지 실질적으로 pop한다해서
    ; 굳이 메모리를 해제하거나 값을 바꿔주는 것은 아니다.


    8. 상대주소 라는 개념이 나온다. 어떤 기준점을 잡고 그 기준점으로부터의 주소를 뜻한다.

    9. bp는 sp의 값을 저장해 스택에서 상대 주소 계산에 유리하다

    10. stack에 이전 BP의 값을 넣는 이유는 함수 스택 프레임에서 함수의 고유 영역을 집어주기 위함이다

    11. 10번과 같은 과정으로 이전 BP 값을 이용해서 함수가 함수를 호출할 떄를 대비하는 것이다.

    12. return할 주소는 ip register의 값을 이용한다. ip 주소의 값을 stack에 넣으면 된다.

    13. 왜 BP를 스택에 넣는가???
    - 함수마다 자신의 스택 프레임을 갖는다
    - 스택 메모리는 어떤 함수를 어떤 순서로 호출 했는가에 따라 사용하는 부분이 유동적이다
    - 전역 변수 였다면 고정 주소지만 스택에 들어가는 지역 변수들은 아니다
    - 스택에 들어가는 지역 변수들의 스택 주소는 상황에 따라 달라지므로 BP를 기준으로 BP - 16 같은 상대 주소로 만들어진다.
    - 이때, 문제점이 발생한다. 함수1이 함수2를 호출한다고 가정하자. 함수1에서 BP가 10이었고 10을 기준으로 변수 값들이 (10-4), (10-8) 위치에 있다고 가정하자
    또한 함수2는 30을 기준으로 변수 값들이 (30-4) (30-8) 이렇게 있다고 하자.
    만약 BP를 스택에 넣어두지 않고 CALL에 끌려 RETURN 주소만 넣어두고 함수 2로 BP가 가버리면 함수 2의 작업은 끝낼 수 있으나 함수 2가 RETURN 된 후 함수 1의 변수들은 찾을 수가
    없을 것이다. 함수 1에서는 10을 기준으로 변수들이 들어가 있는데 기준이 될 10이라는 값을 잃어버렸고 지금 BP는 30이기 때문이다.

    - 이런 문제를 막기 위해서는 기존 함수에서 사용하던 BP의 위치를 안전하게 보존해서 함수2가 끝나면 다시 원래대로 BP 값을 함수1에 맞게 복원시켜야 한다.
    - 결과적으로 함수마다 스택 프레임이 다르고 자신이 사용하는 BP 값이 다르기 때문에 이를 잠시 스택에 보관하고 추후 복원하는 것이다.

  - 이런 패턴을 표현한 것이
  ; push rbp
  ; mov rbp, rsp
  ; pop rbp
  ; ret

  이것이다. 여기선 64bit bp니까 rbp고 32bit면 ebp가 된다.
  rbp 넣고, rsp 값 rbp에 넣어서 함수2의 기준점 가주고 작업 끝내면 pop rbp로 아까 저장했던 rbp 값을 다시 불러온다.



    14. 스택 유의 사항

    push 1
    push 2
    call MAX
    PRINT_DEC 8, rax
    NEWLINE

    add rsp, 16
    ;내가 push 한 번 할때마다 8byte 움직이므로 push 2번 한거에서
    ;다시 원래 주소로 돌아오기 위해 16을 rsp에 더한다.
    ;내가 push 이용해서 stack을 임의로 조작한 후 rsp를 원래 위치에 두지 않으면
    ;프로그램이 작동하고 crashed! 오류가 발생한다
    ;아니면 pop 2 pop 1로 빼주던가
    ;값을 임시저장하고 싶으면 push rax push rbx로 미리 저장하는 방법이 있다.
