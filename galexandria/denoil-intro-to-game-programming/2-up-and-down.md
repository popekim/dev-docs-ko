---
title: "2. UP & DOWN 게임 만들기"
date: 2022-06-06
---

## 들어가며
안녕하세요. Denoil입니다. 죄송합니다. 조금 오래되었네요. 양해 부탁드립니다. ^^ 제가 집에 잠시 올라온 바람에 놀다가보니 쿨럭;; 그래도 귀차니즘을 이겨내고자 이렇게 쓰게 됬습니다. 이번 2편은 전번 가위바위보 게임보다는 난이고다 낮은 게임이지요. 일명 UP & DOWN 게임~! 술자리에 가면 병뚜껑의 꼬다리 부분 꼬아가지고 돌아가면서 치는 게임 하지 않습니까 ^^ 고거 떼어내면 그사람이 술래가 되어 그 병뚜껑안에 있는 번호를 맞추게 하지요 ^^ 그런 게임입니다. 대신 여기선 10번의 기회밖엔 주어지지 않습니다.

## 전체소스코드
자 풀소스를 볼까요? 

```c
#include <stdio.h> 
#include <conio.h> 
#include <stdlib.h> 
#include <time.h> 

void main() 
{ 
   int Random = 0 ; 
   int User = 0 ; 
   int Select = 0 ; 
   int PlayingCount = 0 ; 

   srand( time( NULL ) ) ; 

   printf("이게임은 10번의 기회를 주어 랜덤하게 나온 숫자를 맞추는 게임입니다.\n" ); 
   printf("술 먹을때 소주뚜껑 쳐가지고 끝나면 Up & Down 게임하지 않습니까? ^^\n" ); 
   printf("그 게임이라고 생각하시면 됩니다. ^^\n" ) ; 
   printf("자 준비가 되셨으면 아무키나 눌러주세요 ^^\n" ) ; 
   getch() ; 
   system( "cls" ) ; 

   do 
   { 
   printf("[ Start : 1 ] [ End : 0 ] : " ) ; 
   scanf("%d", &Select ) ; 
   } while( Select < 0 || Select > 1 ) ; 


   Random = rand() % 100 ; 
   PlayingCount = 10 ; 

   while( PlayingCount != 0 ) 
   { 
       printf("0~99까지 숫자를 입력하세요: " ) ; 
       scanf("%d", &User ) ; 

       if( User == Random ) 
       { 
           printf( "딩동댕~\n" ) ; 
           break ; 
       } 
       else if( User > Random ) printf("DOWN\n" ) ; 
       else if( User < Random ) printf("UP\n" ) ; 
   } 
}
```



자~여기 까지입니다. 너무나 쉽게 짜여졌죠. C처음 하시는 분도 금방 따라할수 있는 코드입니다. 별로 중요하지 않는 부분은 그냥 넘어가도록 하겠습니다. 

자 그럼 코드 설명에 빠져 봅시다~ 

### 소스 분석

```c
int Random = 0 ; 
int User = 0 ; 
int Select = 0 ; 
int PlayingCount = 0 ; 
```

* Random -> 우리가 맞출 숫자를 저장할 변수 
* User -> 사용자가 입력할 숫자 입니다. 이숫자와 Random숫자와 같다면 Clear 
* Select -> 메뉴에서 Select를 관리하는 변수입니다. 1은 Start, 2는 Exit 
* PlayingCount -> 입력을 제한하는 변수입니다. 여기서는 10번의 기회를 주었죠. 

```c
srand( time( NULL ) ) ;
```

#### srand함수의 설명

```c
void srand( usigned seed ) ;
```

난수 발생기는 예측할 수 없는 수를 발생시키기는 하나 발생되는 수가 일정하다. 그래서 srand로 난수 발생기를 초기화 시킨다. seed가 난수 발생의 초기점이 된다. 

##### http://www.winapi.co.kr 발췌
```
seed 값을 넣어주어 난수 발생기를 초기화해주어 항상 틀린 난수를 발생하게 합니다.
time 을 넣어주는 이유는 시간은 계속 변하지요? 그럼 난수의 초기값도 항상 바뀌게 되죠.  
그럼 난수도 바뀌게 되는것입니다.  
```

```c
do 
{ 
   printf("[ Start : 1 ] [ End : 0 ] : " ) ; 
   scanf("%d", &Select ) ; 
}while( Select < 0 || Select > 1 ) ; 
```

시작할 것인지 게임을 종료할것인지 메뉴를 선택하게 되지요. do..while문은 일단 첫문장은 실행되고 while의 비교문에서 참이라면 다시 도는것. 아시죠?^^ 

```c
Random = rand() % 100 ; 
PlayingCount = 10 ; 
```

자.. 이제 우리가 맞출 숫자를 뽑아 와야하죠? 

Random = rand() % 100 ; 이것을 보면 0~99까지 구합니다. 어째서냐! 라고 물으시면 1편에서 확인하세요-_-/ PlayingCount 에 10을 넣어준이유는 10번의 기회를 주겠다는 소립니다. 

```c
while( PlayingCount != 0 ) 
{ 
   . 
   . 
   . 
   PlayingCount-- ; 
} ;
```


여기의 코드를 보면 PlayingCount 변수가 0 이 아니라면 계속 루프를 돕니다. 그래서 한번 루프를 돌때마다 PlayingCount 를 -- 시켜주었죠? 10번 실행되면 PlayingCount는 0이 되니 while문을 빠져나오게 됩니다. 간단하죠? 

```c
printf("0~99까지 숫자를 입력하세요: " ) ; 
scanf("%d", &User ) ; 
```

0~99까지의 숫자를 입력합니다. 

```c
if( User == Random ) 
{ 
   printf( "딩동댕~\n" ) ; 
   break ; 
} 
else if( User > Random ) printf("DOWN\n" ) ; 
else if( User < Random ) printf("UP\n" ) ; 
```

User 변수와 Random 이 같다면 맞췄다는 말이 되므로 딩동댕이라는 글씨를 찍어준 후 while문을 빠져나오기 위해 break;를 해주는것입니다. 

그리고 만약 User > Random 이라면 랜덤숫자보다 더 크게 입력한 것이므로 Down이라고 힌트를 주어 다음 입력할때는 현재 입력한 숫자보다 적은 숫자를 입력하도록 하는것입니다. 

User < Random 도 위와 마찬가지로 위의 숫자를 입력하도록 힌트를 주는것입니다. 


자~코드 설명은 여기까지 입니다. 그다지 어렵지 않죠? ^^ 저번편보다 난이도가 내려간것에 대해 양해를 구합니다. (-_-)(_ _)(-_-) 꾸벅~ 

## 숙제
자 숙제를 내드리도록 하겠습니다. 

유저가 숫자를 입력할때 0~99까지 입력해야 하는데 예외처리를 해두지 않았습니다. 만약 0~99의 범위를 벗어나지 못하게 하는 예외처리를 추가해 보십시오. 
숫자를 맞추게 되면 프로그램은 자동으로 종료되게 되어있습니다. 지속적으로 플레이 되게 해보십시오. 

두가지 숙제 한번 해보시고 다르게 개조도 해보십시오. ^^ 그럼 Denoil 이만 물러가겠습니다.

## 저술정보
* 저자: Denoil
* 저술일: 2005년 7월 17일
