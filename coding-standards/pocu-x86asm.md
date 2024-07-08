---
title: "POCU 아카데미용 x86 어셈블리 코딩 표준"
date: 2024-07-08
---

## 머리말
### 기본 원칙

1. 가독성을 최우선으로 삼는다. (대부분의 경우 코드 그 자체가 문서의 역할을 해야 함) 
2. 본 코딩표준을 따라 잘 작성된 기존의 코드에서 배운다.

### 참조문서

이 코딩 표준은 아래의 코딩 표준들에서 영감을 얻었음.

* [POCU 아카데미용 6502 어셈블리 코딩 표준](https://docs.popekim.com/ko/coding-standards/pocu-6502)
* Microsoft® MASM Assembly-Language Development System Version 6.1 Reference
* Microsoft® MASM Assembly-Language Development System Version 6.1 Programmer's Guide

## I. 메인 코딩 표준

1. 옵코드 니모닉은 모두 소문자로 표기한다.
    ```masm
    mov ah, 09h
    ```

2. 16진수에 사용하는 문자(A-F)는 대문자로 표기한다.

    ```masm
    mov ah, 4Ch
    ```

3. 라벨명은 모두 소문자로 표기한다.

    ```masm
    next_char:
    ```
   
4. 전역 라벨 대신 지역 레벨을 사용하여 이름 충돌을 피한다.

5. 숫자 상수는 `EQU` 지시어를 사용하여 정의한다.

    ```masm
    BUFSIZE EQU 255
    ```

6. 변수명은 모두 소문자로 표기한다.

    ```masm
    .DATA
    buffer  DB BUFSIZE
            DB BUFSIZE+1 DUP(?)
    ```

7. 어셈블리 지시문은 모두 대문자로 표기한다.

    ```masm
    .DATA
    ```

8. 사용자가 만든 매크로는 모두 소문자로 표기한다.

    ```masm
    EXIT0 MACRO ;() <al,ah,flags>
        mov ah, 4Ch
        xor al, al
        int 21h
    ENDM
    ```

8. 상수, 라벨, 매크로 이름은 구체적으로 지으려 노력한다. 예: `i`나 `doit`보다 `stu_idx`나 `EXIT0`가 더 좋은 이름

10. 주석은 세미콜론을 사용한 한 줄짜리 주석만 허용한다.

    ```masm
    ; return value is 0
    xor al, al 
    ```

11. 코드와 같은 줄, 즉 코드 오른쪽에 주석을 달아도 된다.

    ```masm
    xor al, al  ; return value is 0
    ```

12. 코드를 다음과 같은 순서를 따라 여러 섹션으로 정리한다.
    1. 상수
    2. 변수
    3. 매크로
    4. 명령어

13. 상수를 사용할 때는 매직 넘버를 사용하는 대신 언제나 이름 달린 상수를 정의해 사용한다.

    **틀린 방식:**
    ```masm
    mov cx, 255
    ```

    **올바른 방식:**
    ```masm
    BUFSIZE EQU 255
    ...
    mov cx, BUFSIZE
    ```

14. 마찬가지로 점프를 할 때도 직접 메모리 주소를 적지 않는다.

15. 모든 어셈블러 경고를 고친다.

16. 모든 함수/매크로 위에 다음의 정보를 포함한 주석을 단다.
    1. 그 함수/매크로가 하는 일에 대한 요약
    2. 인자 목록
    4. 그 함수/매크로의 반환값(들)
    5. 함수/매크로가 변경하는 것들

    예:
  
    ```masm
    sum PROC ; (s[0]) -> ax | <cx,flags>
    ;==========================================
    ; summary:   Calculates the sum from 1 to n
    ;
    ; arguments: s[0]   n
    ;
    ; returns:   ax     The sum from 1 to n
    ;
    ; modifies:  cx
    ;            flags
    ;==========================================
    ```

## II. 소스 코드 포맷팅
1. 들여 쓰기는 스페이스 4칸으로 한다.

2. 피연산자 여럿일 경우, 쉼표(,)와 스페이스 한 칸으로 구분한다.

    ```masm
    xor al, al
    ```
    
3. 라벨은 들여 쓰지 **않는다**.

    ```masm
    next_char:
        xor al, al
    ````

4. 라벨은 한 줄을 오롯이 차지한다. 같은 줄에 주석을 적는 것은 허용한다.

    **틀린 방식:**
    ```masm
    accum:    add di, 4
    ```

    **올바른 방식:**
    ```masm
    accum: ; optional comment
        add di, 4
    ```

5. 지시문이나 매크로 안에 들어있는 지시문이나 매크로는 한 번 더 들여 쓴다.

6. 지시문이나 매크로의 인자는 쉼표(,)와 스페이스 한 칸으로 구분한다.
