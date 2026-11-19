---
title: "48. 아크볼 회전"
date: 2026-11-19
---

## 소개 {#TOC--}

[![48\. 아크볼 회전 실행 화면](/ko/galexandria/nehe-opengl-tutorial/images/lesson48.jpg)](/ko/galexandria/nehe-opengl-tutorial/images/lesson48.jpg)

마우스만을 이용해서 모델을 마음대로 회전할 수 있다는게 몬가 대단하다고 생각되지 않나여? 아크볼과 함께라면 이런 일을 할 수 있습니다. 이 강좌에서 저는 여러분의 소스에 아크볼을 추가할 때 고려할 사항과 구현방법에 대해서 설명할 것입니다.

제가 구현한 아크볼 클래스는 Bretton Wade와 그래픽 젬스 책에 있는 Ken Shoemake의 글에 기반을 두고 있습니다. 그러나 제가 약간의 버그를 고치고 저희의 목적에 맞게 최적화시켰습니다.

## 본문 {#TOC--1}

아크볼은 윈도우에서 마우스로 클릭한 좌표를 아크볼의 구면 좌표계로 매핑하는 방법으로 동작합니다. 마치 여러분 앞에 구면 좌표계가 있는 것처럼 말입니다.

이것을 하기 위해서 먼저 간단히 마우스 좌표를 \[0...폭), \[0...높이)에서 \[-1...1\], \[1...-1\] 범위로 크기조정을 해야합니다. 여기서 주의해야할 것은 OpenGL에서 정확한 결과를 얻으려면 Y부호를 반대로 해야한다는 것입니다. 이렇게 하기 위해 필요한 식은 아래 처럼 보일것입니다.

```cpp
MousePt.X  =  ((MousePt.X / ((Width  – 1) / 2)) – 1);
MousePt.Y  = -((MousePt.Y / ((Height – 1) / 2)) – 1);
```

좌표 범위를 \[-1...1\]로 크기조정하는 이유는 수학적으로 좀 더 단순하게 만들기 위함입니다. 이렇게 함으로써 컴파일러에게 약간의 최적화를 수행할수 있도록 해줄수 있습니다.

다음으로 벡터의 길이를 계산해서 이 벡터가 구의 내부에 있는지 외부에 있는지를 판단합니다. 만약 구 내부에 있다면 구 내부로부터의 벡터를 리턴하며 외부에 있다면 점을 정규화시켜서 구 외부에 가장 가까운 점을 리턴합니다.

일단 두 벡터를 모두 구했다면 이제 시작과 끝 벡터가 이루는 각에 수직인 벡터를 계산할 수 있습니다. 따라서 수직인 벡터와 그 각도를 하나로 표현하면 이게 쿼터니언이 되는거죠! 이 정보를 바탕으로 우리는 회전행렬을 생성할 수 있게 됩니다. 휴! 이제 간신히 한고비를 넘겼네여.

아크볼은 다음의 생성자를 사용해서 초기화시킵니다. NewWidth와 NewHeight는 윈도우 폭과 높이입니다.

```cpp
ArcBall_t::ArcBall_t(GLfloat NewWidth, GLfloat NewHeight)
```

시작 벡터는 사용자가 마우스를 클릭할 때 클릭된 지점을 기준으로 계산됩니다.

```cpp
void    ArcBall_t::click(const Point2fT* NewPt)
```

끝 벡터는 마우스를 드래그 할 때 갱신이 되며 쿼터니언 출력인자(NewPt)가 주어지기 때문에 쿼터니언 인자가 결과 회전값으로 업데이트됩니다.

```cpp
void    ArcBall_t::drag(const Point2fT* NewPt, Quat4fT* NewRot)
```

윈도우 크기가 바뀌면 단순히 윈도우 크기 정보만 가지고 ArcBall을 업데이트 합니다.

```cpp
void    ArcBall_t::setBounds(GLfloat NewWidth, GLfloat NewHeight)
```

여러분의 프로젝트에 아크볼을 사용하려면 추가적으로 멤버 변수들이 필요할 것입니다.

```cpp
// 최종 변환
Matrix4fT    Transform = {
   1.0f,  0.0f,  0.0f,  0.0f,
   0.0f,  1.0f,  0.0f,  0.0f,
   0.0f,  0.0f,  1.0f,  0.0f,
   0.0f,  0.0f,  0.0f,  1.0f };

Matrix3fT    LastRot = {
   1.0f,  0.0f,  0.0f,                                // 마지막 회전
   0.0f,  1.0f,  0.0f,
   0.0f,  0.0f,  1.0f };

Matrix3fT    ThisRot = {
   1.0f,  0.0f,  0.0f,                                // 이 회전
   0.0f,  1.0f,  0.0f,
   0.0f,  0.0f,  1.0f };

ArcBallT    ArcBall(640.0f, 480.0f);                // ArcBall 인스턴스
Point2fT    MousePt;                        // 현재 마우스 포인트
bool        isClicked  = false;                        // 마우스가 클릭되었는가?
bool        isRClicked = false;                        // 오른쪽버튼이 눌렸는가?
bool        isDragging = false;                        // 마우스를 드래그 중인가?
```

Transform은 저희가 구할려고 하는 최종변환 행렬입니다. LastRot은 드래그가 끝난 지점에서의 최종회전입니다. ThisRot는 드래그가 일어나는 동안에 발생하는 회전입니다. 모든 행렬은 항등행렬로(identity)로 초기화 됩니다.

클릭이 일어나면 항등 회전 행렬 상태에서 시작합니다. 드래그를 하는 도중에는 최초 클릭지점에서 현재 드래그된 지점사이까지의 회전을 계산합니다. 화면에서 물체를 회전하기 위해 이 정보를 사용할지라도 우리가 실제로 아크볼 그자체를 회전하지 않는다는 것을 아시는 것이 중요합니다. 따라서 누적된 회전을 가지기 위해서는 이것을 우리 스스로 처리해야합니다.

누적된 회전을 구하기 위해서는 LastRot와 ThisRot을 이용해야 합니다. LastRot은 "현재까지의 모든 회전"이고 ThisRot은 "현재 회전" 입니다. 매번 드래그를 시작할 때마다 ThisRot은 최초 회전에 의해 수정됩니다. ThisRot은 그 때 ThisRot \* LastRot곱으로 계산됩니다(이 떄 최종변환 행렬도 역시 갱신됩니다.). 드래그를 멈추면LastRot은 ThisRot으로 값으로 할당됩니다.

우리가 회전을 스스로 누적시키지 않으면 매 번 마우스를 클릭할 때마다 모델이 원점으로 돌아갈 것입니다. 예를 들면 우리가 x축 주위로 90도로 회전하고 나서 45도로 회전한다면 우리는 마지막 45도 회전만이 아닌 135도로 회전하는 것을 보기를 원할 것입니다.

isDragged를 제외한 나머지 변수들은 시스템에 따라 적절한 때에 업데이트시켜주기만 하면 됩니다. 아크볼은 윈도우 크기가 바뀔때마다 그 경계를 리셋시켜줘야 합니다. MousePt는 마우스가 이동할 때마다, 또는 마우스 버튼이 눌려있을 때마다 업데이트시켜줍니다. isClicked / isRClicked는 각각 왼쪽/오른쪽 마우스 버튼이 눌릴 때마다 업데이트시켜줍니다. isClicked는 클릭과 드래그를 판단하는데 사용됩니다. 우리는 모든 회전을 항등행렬로 리셋하기 위해 isRClicked를 사용할 것입니다.

NeHeGL/Windows 환경하에서 추가적인 시스템 갱신코드는 아래와 같을 것입니다.

```cpp
void ReshapeGL (int width, int height)
{
   . . .
   // 아크볼의 마우스 경계를 업데이트함
   ArcBall.setBounds((GLfloat)width, (GLfloat)height);
}
// 윈도우 메시지 콜백을 처리
LRESULT CALLBACK WindowProc (HWND hWnd, UINT uMsg, WPARAM wParam, LPARAM lParam)
{
   . . .
   // 아크볼용 마우스관련 메시지
   case WM_MOUSEMOVE:
       MousePt.s.X = (GLfloat)LOWORD(lParam);
       MousePt.s.Y = (GLfloat)HIWORD(lParam);
       isClicked   = (LOWORD(wParam) & MK_LBUTTON) ? true : false;
       isRClicked  = (LOWORD(wParam) & MK_RBUTTON) ? true : false;
       break;
   case WM_LBUTTONUP:   isClicked  = false; break;
   case WM_RBUTTONUP:   isRClicked = false; break;
   case WM_LBUTTONDOWN: isClicked  = true;  break;
   case WM_RBUTTONDOWN: isRClicked = true;  break;
   . . .
}
```

시스템 갱신 코드를 마무리했으니 이제는 클릭 로직을 작성할 차례입니다. 이것은 앞에서 설명한 내용을 모두 알고 있다면 말안해도 아실 것입니다.

```cpp
if (isRClicked)                                // 오른쪽 마우스버튼을 클릭중이라면 모든 회전을 리셋한다
{
   // 회전을 리셋함
   Matrix3fSetIdentity(&LastRot);

   // 회전을 리셋함
   Matrix3fSetIdentity(&ThisRot);

   // 회전을 리셋함
   Matrix4fSetRotationFromMatrix3f(&Transform, &ThisRot);
}
if (!isDragging)                            // 드래깅 중이 아님
{
   if (isClicked)                                // 첫번째 클릭
   {
       isDragging = true;                    // 드래깅에 대비
       LastRot = ThisRot;                    // 마지막 정적 회전을 마지막 동적 회전으로 설정
       ArcBall.click(&MousePt);                // 시작벡터를 업데이트하고 드래깅에 대비
   }
}
else
{
   if (isClicked)                                // 여전히 클릭중, 따라서 여전히 드래그중
   {
       Quat4fT     ThisQuat;

       ArcBall.drag(&MousePt, &ThisQuat);            // 끝 벡터를 업데이트하고 회전값을 쿼터니언으로 구한다
       Matrix3fSetRotationFromQuat4f(&ThisRot, &ThisQuat);    // 쿼터니언을 Matrix3fT로 변환한다
       Matrix3fMulMatrix3f(&ThisRot, &LastRot);        // 마지막 회전을 현재 회전에 축적한다
       Matrix4fSetRotationFromMatrix3f(&Transform, &ThisRot);    // ThisRot을 최종 변환의 회전값을 설정한다
   }
   else                                    // 더이상 드래그중이 아님
       isDragging = false;
}
```

이것들이 모든 일을 다해주고 있기 때문에 지금 우리가 해야하는 일은 모델에 변환을 적용하는 것뿐입니다. 그것이 이 강좌의 마지막입니다. 이것은 정말 쉽습니다. 아래를 보세여.

```cpp
glPushMatrix();                                // 동적 변환을 준비한다
   glMultMatrixf(Transform.M);                // 동적 변환을 적용한다

   glBegin(GL_TRIANGLES);                        // 모델 그리기를 시작
   . . .
   glEnd();                        // 모델 그리기를 마침

   glPopMatrix();                                // 동적 변환의 적용을 해제한다
```

저는 위에서 설명한 모든 것들을 보여드리기 위해서 하나의 샘플을 포함시켰습니다. 여러분에게 저의 수학 형식이나 함수를 사용하도록 강요하고 싶지는 않습니다. 여러분이 충분히 자신감이 있으시면 여러분이 직접 수학함수를 사용해서 이것을 만드셔도 됩니다.  그렇지만 음.. 소스에 모든 것이 다 자체적으로 구비되어 있기 떄문에 이것을 사용하시면 여러분의 소스에서 잘 작동할 것입니다.

간단히 아크볼을 회전하는 방법을 이해한 여러분은 이제 프로젝트에 아크볼을 추가하는것을 잘하실수 있을것입니다. 잘 사용하세요!

## 소스코드 다운로드 {#TOC--2}

원문 페이지에서 소스코드를 다운로드 받으실 수 있습니다.

## 원문 정보 {#TOC--3}

*   저자: Terence J. Grant
*   원문보기: [Lesson 48](https://nehe.gamedev.net/tutorial/arcball_rotation/19003/)
