---
title: "3. 숫자야구 게임 만들기"
date: 2022-06-06
---

안녕하십니까? :) 오랜만에 뵙습니다. 흑흑 제가 바쁘다는 핑계로 계속 미루고 미루다가 
지금에서야 글을 올리게 됩니다. 꾸준히 써 나가야 하는데 걱정입니다. :) 그래도 이해해 주실꺼죠? *-_-*

이번 게임은 숫자야구 게임입니다. 숫자 야구 아시죠? 모르시는분 손~ 


네~모두 안들으셨군요? 그럼 게임 설명은 넘어가겠습니다. ㅎㅎㅎ

```
pope : 이봐 어디 그냥 술렁 술렁 넘어가려고해 ?  - _-+++
denoil : 아무도 손 안들었는데욘? -_-)a
pope : lol 나 두손 다 들었는데?
denoil : -_-;; 만쉐이 하지마3
pope : 시러 흥!
```

네~ 포프씨가 설명을 부탁하는군요. [ 가상대화입니다. -_-)a ] 숫자야구 게임이란..랜덤한 0~9까지의 숫자 3개에서 그 숫자를 순서에 맞게 맞춰야 승리하는 게임입니다. 예를 들어 1 3 5 라는 랜덤한 숫자가 있다고 가정하에 게이머가 1 2 3 를 입력했다고 가정해 봅시다. 1과 3이라는 숫자가 랜덤한 숫자가 나오죠? 엇? 그런데 1은 같은 자리에 있네요? 그럼 네놈은 스트라이크로 임명합니다. 그럼 3은? 예상 하셨듯이 볼 입니다.

이제 게임이 이해가 가시나요? ^^; 모르시면..미워요..-_ㅠㅠㅠㅠㅠ 그럼 소스설명 들어가겠습니다. 일단 전체 풀소스 코드 등장이요~


## 전체소스코드
```c
#include    <stdio.h>
#include    <conio.h>
#include    <stdlib.h>
#include    <time.h>

void    main()
{
   int    Computer[3]            =    { 0, };
   int    User[3]                =    { 0, };
   int    NumberTable[10]        =    { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
   int    UserInputTable[20][3]  =    { 0, };
   int    UserInputCount         =    0;

   char*    StrikeString[3]      =    { " ", "원 스트라이크", "투 스트라이크"};
   char*    BallString[4]        =    { " ", "원볼", "투볼", "쓰리볼" };
   
   printf("숫자야구를 시작합니다.\n");
   printf("...Press Any Key..." );
   getch();

   srand( time( NULL ) );
   system( "cls" );

   int Count = 3;

   while( Count > 0 )
   {
       int    Random    =    rand() % 10;

       if( NumberTable[ Random ] == -1 )    continue;

       Computer[ 3 - Count ]    =    NumberTable[ Random ];        
       NumberTable[ Random ]    =    -1;

       Count--;
   }

   while( 1 )
   {
       int    Strike  =    0;
       int    Ball    =    0;
       int    i , j;

       system( "cls" );

       for( i = 0; i < UserInputCount; ++i )
       {
           printf("%d %d %d\n", UserInputTable[i][0], UserInputTable[i][1], UserInputTable[i][2] );
       }
       
       printf("0~9까지의 숫자를 3개 입력해 주십시오 ex) 2 9 4\n" );
       printf("종료는 -1을 눌러주세요.\n" );
       printf("기회는 20번. 남은기회 : %d번\n", 20 - UserInputCount );
       printf("입력 : ");

       for( i = 0; i < 3; ++i )
       {
           scanf("%d", &User[i] );    if( User[i] == -1 )    break;
       }

       for( i = 0; i < 3; ++i )
       {
           for( j = 0; j < 3; ++j )
           {
               if( Computer[i] == User[j] )    
               {
                   if( i == j )
                       Strike++;
                   else
                       Ball++;
               }
               
           }
       }

       if( Strike == 3 )
       {
           printf("아웃! 게임종료\n" );
           break;
       }
       else
       {
           printf("%s %s\n", StrikeString[ Strike ], BallString[ Ball ] );
       }

       UserInputTable[UserInputCount][0]    =    User[0];
       UserInputTable[UserInputCount][1]    =    User[1];
       UserInputTable[UserInputCount][2]    =    User[2];
       
       UserInputCount++;

       if( UserInputCount >= 20 )
       {
           printf("모든 기회를 놓치셨습니다.\n" );
           printf("정답 : %d %d %d\n", Computer[0], Computer[1], Computer[2] );
           break;
       }

       printf("Press Any Key" );
       getch();
   }
}
```

네~소스가 참 야무지게 길죠? ^^;;; 방금 조금 날림으로 짰는데-_-제머리에선 저것보다 좋은게 안나오더군요. 더 보기좋은소스 있으시면 연락주세요 -_ㅠ

## 소스 분석

자~앞에 include부분 생략하고

```c
int    Computer[3]            =    { 0, };
int    User[3]                =    { 0, };
int    NumberTable[10]        =    { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
int    UserInputTable[20][3]  =    { 0, };
int    UserInputCount         =    0;

char*    StrikeString[3]      =    { " ", "원 스트라이크", "투 스트라이크"};
char*    BallString[4]        =    { " ", "원볼", "투볼", "쓰리볼" };
```

변수 설명에 빠져 봅시다~ 자~하~ 가는거야~( 나름대로 노박사 흉내-_-;;)

* Computer       -> 랜덤한 숫자 3개를 저장할 배열
* User           -> 사용자가 입력한 숫자 3개를 저장할 배열
* NumberTable    -> 랜덤하게 뽑아올 숫자들의 배열
* UserInputTable -> 유저가 입력한 숫자들을 모아서 보여주기 위한 배열
* UserInputCount -> 유저가 입력을 한 회수를 저장할 변수
* StrikeString   -> 스트라이크 문자를 저장해 놓은 배열 포인터
* BallString     -> 볼 문자를 저장해 놓은 배열 포인터

이상한 변수들이 참많죠? 그래도 다 쓸데가 있는놈들입니다~^^

```c
int Count = 3;

while( Count > 0 )
{
   int Random = rand() % 10;

   if( NumberTable[ Random ] == -1 )    continue;

   Computer[ 3 - Count ]    =    NumberTable[ Random ];
   NumberTable[ Random ]    =    -1;

   Count--;
}
```

자~이부분을 보도록하겠습니다. 이것은 랜덤한 숫자 3개를 뽑아내기 위한 루틴입니다. 제가 NumberTable이라는것을 저장해 놓은 이유는요? 같은 숫자가 안나오게 하기 위해서 입니다. 일단 0~9까지라는 랜덤한 숫자를 뽑게 되겠죠? ( rand()함수는 제1강을 참고 하세요 )

그럼 일단 0~9까지의 숫자가 들어있는 테이블이 -1이 아닌지를 체크하게 됩니다. 그 이유는 한번 뽑은 숫자는 -1로 처리하여 동일한 숫자가 나오지 않게 하는것이지요. 만약 -1이 라면? while문은 다시 시작하게 되지요?

자 3 - Count ! 이부분은 Count는 3부터 시작합니다.  그래서 첫번째는 3 - 3 = 0 !  0번 배열에 들어가겠죠? 두번째 세번째는 여러분이 맞추십시오..-_-ㅋ

NumberTable[ Random ]    = -1 ; <- 이부분이 앞에서 설명한 부분입니다. 일단 랜덤 배열에 들어간 숫자는 테이블에서 -1을 처리하여 위에서 예외 처리를 해주게되죠. 그런후 Count 는 하나씩 감소하여 3개의 랜덤한 숫자가 채워지면 while문은 종료하게 됩니다.


자~다음은 두번째 while문을 보게 됩니다. 두번째 while문은 게임 루프라고 생각하셔도 될듯합니다. 게임 루프는 무한 루프다. 라고 저는 정의를 내립니다. :)

```c
for( i = 0; i < 3; ++i )
{
   scanf("%d", &User[i] );    if( User[i] == -1 )    break;
}
```

이부분! 사용자 입력 받는 부분입니다. 3번의 for문을 돌면서 3개의 숫자를 입력받습니다.

```c
scanf("%d %d %d", &User[0], &User[1], &User[2] );
```

이런..방법이 있긴하지만 -1을 입력받게 되면 게임을 종료 시켜야 하기에 저렇게 한것이지요 :)

```c
for( i = 0; i < 3; ++i )
{
   for( j = 0; j < 3; ++j )
   {
       if( Computer[i] == User[j] )    
       {
           if( i == j )
               Strike++;
           else
               Ball++;
       }
       
   }
}
```

자~가장 중요한 부분입니다. 스트라이크와 볼을 판별하는 루틴이지요. 2중 for문 다 아시죠? :) 일단 Computer[i]와 User[j] 에 있는 숫자가 같다면 스트라이크든 볼이든 둘중 하나 겠죠? 맞습니까? 네~ 맞습니다.-_-;;

그런데 자리수가 같으면 스트라이크, 틀리면 볼이라고 처음 게임설명때 설명드렸죠?

```c
if( i == j )    Strike++;
else        Ball++;
```

요게 그놈입니다. 자리수가 같으면 스트라이크 아니라면 볼.

```c
if( Strike == 3 )
{
   printf("아웃! 게임종료\n" );
   break;
}
else
{
   printf("%s %s\n", StrikeString[ Strike ], BallString[ Ball ] );
}
```

스트라이크가 3개면 아웃! 출력후 while문을 빠져나와 게임종료. 아니라면 스트라이크는 몇개인지, 볼은 몇개인지 알려줘야 하겠죠? 그것이

```c
printf("%s %s\n", StrikeString[ Strike ], BallString[ Ball ] );
```

요놈입니다. 

자, 여기서 의문점. 아까전에 StrikeString과 BallString의 처음에 " " <- 이런 공백문자를 사용한 이유가 뭘까요? 소스코드를 보면

```c
int    Strike    =    0;
int    Ball      =    0;
```

이렇게 초기화를 해놓았습니다. 그런데 Strike와 Ball 이 0이라면 아무것도 못맞췄단 소리죠? 그럼 아무것도 출력이 되어선 아니 되옵니다. Strike가 1개고 Ball이 2개다 라고 가정하면 StrikeString의 1번째 배열 참조, BallString의 2번째 배열참조가 됩니다.

물론 Strike와 Ball을 -1로 시작하셔도 상관없지만..-1은 배열참조할때 에러가 나잖습니까.. 그러면 저코드말고 다른 출력방식을 찾아야겠죠?

```c
UserInputTable[UserInputCount][0]    =    User[0];
UserInputTable[UserInputCount][1]    =    User[1];
UserInputTable[UserInputCount][2]    =    User[2];

UserInputCount++;
```

이부분은 유저가 입력한 숫자들을 저장해 놓는 코드들입니다. 유저가 입력한 숫자를 저장해 두었다가 모두 출력해주면 게임을 맞추기가 더욱 쉬워지겠죠? :) for문 돌리기 귀찮아서 Copy & Paste 썼습니다.-_-)a 쿨럭

```c
if( UserInputCount >= 20 )
{
   printf("모든 기회를 놓치셨습니다.\n" )    ;
   printf("정답 : %d %d %d\n", Computer[0], Computer[1], Computer[2] )    ;
   break    ;
}
```

제가 이 게임을 20번만에 깨라고 정해 두었습니다. 그래서 UserInputCount 가 20이 되게 되면 정답을 말해준후 게임을 종료하게 되는것이지요. 

## 마무리

자~게임설명 여기까지입니다. 벌써 세번째인데 아직도 글쓰는 재주는 늘지가 않네요. 근데 소스보시면 다 이해 가실겁니다. :) 그다지 어렵지 않으셨죠?

지금 모기에 물려서-_- 죽을꺼 같습니다. 잠이 안옵니다. 새벽 5시 39분을 지나고 있는데도.. ㅠㅠ 아 땀나라..그럼 저는 여기서 물러나겠습니다.

좋은 하루 되십쇼 +_+/

## 저술정보
* 저자: Denoil
* 저술일: 2005년 8월 20일