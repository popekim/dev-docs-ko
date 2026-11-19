---
title: "ODE V0.5 사용자 가이드"
date: 2026-11-19
---

## 소개 {#TOC--}

Open Dynamics Engine (ODE)는 관절식 강체 동역학을 시뮬레이션하는 상용 수준의 공개 라이브러리로 차량이나 다리가 달린 생물체, 또는 가상 현실 환경에서 움직이는 물체들을 시뮬레이션하는 데 적합하다. ODE는 빠르고, 유연하며 견고합니다. 또한 자체 충돌검사 기능도 가지고 있습니다. 현재 [러셀 스미스](http://www.q12.org/)씨가 ODE를 개발 중이고, 몇 명의 [공헌자](http://opende.sourceforge.net/community.html)들의 도움을 받고 있습니다.

'강체 시뮬레이션'의 뜻을 모르시는 분들은 물리 [SDK 란?](http://opende.sourceforge.net/slides/slides.html)을 읽어보시기 바랍니다.

이 문서는 ODE 버전 0.5의 사용자 가이드입니다. 버전 번호가 낮음에도 불구하고 ODE는 상당히 완성되고 안정적입니다.

### 특징 {#TOC--1}

ODE는 관절로 연결된 강체 구조들을 시뮬레이션하는 데 훌륭한 라이브러리입니다. 관절로 연결된 강체구조란 다양한 모양의 강체들이 다양한 종류의 관절들을 통해 연결되어 있는 구조입니다. 바퀴가 차체에 연결된 차량, 다리가 몸에 연결되어 있는 생물, 또는 물체더미들이 좋은 예들입니다.

ODE는 상호작용(interactive) 실시간 시뮬레이션에 사용할 수 있도록 설계되었습니다. 변덕스러운 가상현실 환경에서 이동중인 물체들을 시뮬레이션하는 데 특히 훌륭합니다. ODE는 빠르고 견고하고 안정적이기 때문입니다. 심지어는 사용자가 시뮬레이션 도중에 시스템의 구조를 바꿀 수 있는 완벽한 자유를 가집니다.

ODE는 매우 안정적인 적분기(integrator)를 사용하므로 시뮬레이션 오류가 커져도 제어를 잃지 않습니다. 이것의 물리적 의미는 시뮬레이션되는 시스템은 어떠한 이유로도 "폭주(explode)"해서는 안된다는 뜻입니다. (다른 시뮬레이터에서는 사용자의 부주의로 인해 폭주하는 일이 많이 일어납니다.) ODE는 물리적 정확성보다는 속도와 안정성을 중시합니다.

ODE는 딱딱한 충돌을 가집니다. 이것은 두 강체가 충돌할 때는 항상 특수한 비-관통 제약(non-penetration constraint)을 사용한다는 것을 의미합니다. 많은 다른 시뮬레이터들은 접촉을 표현하기 위해 가상 용수철을 사용합니다. 이것은 제대로 작동하기 어렵고 오류가 나기 쉽습니다. 

ODE는 자체 충돌 검사 시스템을 가지고 있습니다만 이것 대신에 자신만의 충돌 검사 시스템을 만들어 사용할 수도 있습니다. 현재의 충돌 기본 요소는 구, 상자, 뚜껑 달린 원통, 평면, 반직선 그리고 삼각형 메쉬(mesh)입니다. (충돌 물체에 대해서는 나중에 더 자세히 설명하겠습니다.) ODE의 충돌 시스템은 '공간(space)'이라는 개념을 통해서 잠재적인 충돌 가능성이 있는 물체들을 빠르게 식별할 수 있게 해 줍니다. 

ODE의 특징은 다음과 같습니다.

*   임의 질량 분포를 가진 강체. 
*   관절 타입: 구상, 경첩, 미닫이, 경첩-2, 고정, 각모터, 십자축 관절. 
*   충돌 기본 요소: 구, 상자, 뚜껑 달린 원통, 평면, 반직선, 삼각형 메시. 
*   충돌 공간: 쿼드 트리(사분 트리), 해시(hash) 공간, 단순 공간 
*   시뮬레이션 방식: 운동 방정식은 Trinkle/Stewart와 Anitescu/Potra에 따른 라그랑지 승수 속도 기반 모델(Lagrange multiplier velocity based model)에서 파생되었습니다. 
*   1차 적분기를 사용 중입니다. 이것은 빠르지만, 계량 공학에 대해서는 아직 충분히 정확하지 않습니다. 나중에 더 높은 차수의 적분기가 등장할 것입니다. 
*   시간 스텝(step) 함수 선택: 표준 "큰 행렬(big matrix)" 방식 또는 새로운 반복 QuickStep 방식을 사용할 수 있습니다.
*   접촉과 마찰 모델: 비록 ODE가 쿨롱 마찰 모델에 대한 더 빠른 근사를 구현하지만, 이것은 Baraff가 기술한 Dantzig LCP solver에 기반을 두고 있습니다. 
*   순수 C 인터페이스를 사용 (하지만 ODE는 거의 C++로 작성하였음). 
*   C인터페이스를 바탕으로 한 C++ 인터페이스를 가짐. 
*   많은 단위 검사, 그리고 항상 코드를 추가 작성. 
*   플랫폼 한정적인 최적화들. 
*   미쳐 언급하지 못한 기타 특징들...

### ODE 라이센스 {#TOC-ODE-}

ODE is Copyright © 2001-2004 Russell L. Smith. All rights reserved.

This library is free software; you can redistribute it and/or modify it under the terms of EITHER: 

1.  The [GNU Lesser General Public License](http://www.opensource.org/licenses/lgpl-license.html) as published by the Free Software Foundation; either version 2.1 of the License, or (at your option) any later version. The text of the GNU Lesser General Public License is included with this library in the file LICENSE.TXT. 
2.  The [BSD-style license](http://opende.sourceforge.net/ode-license.html) that is included with this library in the file LICENSE-BSD.TXT. 

This library is distributed in the hope that it will be useful, but ''WITHOUT ANY WARRANTY;'' without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the files LICENSE.TXT and LICENSE-BSD.TXT for more details. 

### ODE 커뮤니티 {#TOC-ODE-1}

ODE에 대해 궁금한 점이나 제안하실 점이 있으신 분들이 계신가요? 아니면 그냥 많은 도움이 필요하신 분들이라도? 그렇다면 [ODE 메일링 리스트](http://q12.org/mailman/listinfo/ode)에 글을 남겨주세요.

## ODE 설치와 사용법 {#TOC-ODE-2}

### ODE 설치하기 {#TOC-ODE-3}

**단계 1:** ODE 압축을 푼다.   

**단계 2-4 (윈도우):** 윈도우즈에서 MSVC를 사용 중 이라면, 배포판의 VC6 하위 디렉토리에서 워크 스페이스와 프로젝트 파일들을 사용할 수 있다. 

**단계 2:** GNU make 툴을 얻는다. 많은 유닉스 플랫폼들은 make 또는 gmake를 가지고 있다. 윈도우즈용 GNU make는 여기에서 구할 수 있다.

**단계 3:** 파일에서 설정을 편집하라. 지원되는 플랫폼 리스트가 그 파일에 나와있다. 

**단계 4:** 환경 설정하고 ODE와 그래픽 테스트 프로그램을 빌드하기 위해 GNU make를 실행하라. 환경 설정 과정은 include/ode/config.h 파일을 생성한다.

**단계 5:** ODE 라이브러리를 시스템에 설치하기 위해서, lib/ 와 include/ 디렉토리를 적절한 곳으로 복사하라, 즉 유닉스에서는: 

*   include/ode/ --> /usr/local/include/ode/ 
*   lib/libode.a --> /usr/local/lib/libode.a 

#### MacOS X 에서 ODE 테스트를 빌드하고 실행하기 {#TOC-MacOS-X-ODE-}

ODE는 시뮬레이션되는 장면을 렌더링하기 위해 XWindows와 OpenGL를 사용한다. 예제를 빌드하기 위해서 Apple X11 서버와 X11SDK를 설치해야한다(일반 개발 툴도 함께 설치한다).

이런 것들은 Apple로부터 얻을 수 있다. http://www.apple.com/macosx/x11. 주의: SDK 페이지의 우측 하단에 조그만 링크가 있다.

소프트웨어가 설치되면 일반적인 빌드 지시들을 따르라.

ODE는 X11를 사용하기 때문에 X11 서버를 실행해야한다 (설치를 했으면, Applications 폴더에 있을 것이다).

X11 서버가 기본으로 여는 XTerm에서 테스트 프로그램을 실행하면, 잘 작동이 될 것이다. 그러나 만약 MacOs X Terminal에서 실행한다면, 환경 변수 DISPLAY를 정의할 필요가 있다. DISPLAY가 정의되지 않으면, "cannot open X11 display" 라는 메시지를 보게 될 것이다.

boxstack 테스트 프로그램을 실행하는 예

```cpp
   cd ode/test
   DISPLAY=:0.0 ./test_boxstack.exe
```

쉘 시작 스크립트에서 환경 변수를 정의할 수 있다.

### ODE 사용하기 {#TOC-ODE-4}

ODE의 사용법을 이해하는 가장 좋은 방법은 test/example안에 있는 프로그램들을 보는 것이다. 다음 사항에 주의하라: 

*   ODE를 사용하는 소스 파일은 단 하나의 헤더 파일만을 포함 시키면 된다: 

#include <ode/ode.h>

여기서 ode 디렉토리는 실제로 ODE 배포본의 include/ode 이다. 이 헤더 파일은 ode 디렉토리 안의 다른 파일들을 포함할 것이다. 그러므로 컴파일러의 include 경로를 설정해 줄 필요가 있다. 가령 리눅스에서는

```cpp
gcc -c -I /home/username/ode/include myprogram.cpp
```

*   ODE는 `dWorldStep`함수와 함께 사용될 때, 임시 값들을 저장하기 위해 스택을 적극 활용한다. 매우 큰 시스템에 대해 수 메가 바이트의 스택이 사용될 수 있다. 특히 윈도우즈에서 알수없는 out-of-memory 오류나 데이타 훼손을 경험하였다면, 스택의 크기를 늘리거나 `dWorldQuickStep`으로 바꿔라. 

## 개념 {#TOC--2}

### 배경 {#TOC--3}

\[이 장에서 강체 동역학과 시뮬레이션에 대해 배경 지식을 작성하는 동안 Baraff의 훌륭한 SIGGRAPH tutorial를 참고하라\].

### 강체 {#TOC--4}

시뮬레이션의 관점에서보면 강체(rigid body)는 다양한 속성을 가진다. 몇가지 속성은 시간에 따라 변한다: 

*   강체의 기준점(point of reference)의 위치 벡터 (x,y,z). 현재 기준점은 강체의 질량 중심(center of mass)과 일치해야 한다. 
*   기준점의 선속도, 벡터 (vx,vy,vz). 
*   강체의 회전, 사원수 (qs,qx,qy,qz) 또는 3x3 회전 행렬로 표현. 
*   각속도 벡터 (wx,wy,wz), 시간에 따라 회전이 어떻게 변하는 지를 표현.

나머지 강체 속성들은 시간에 따라 항상 일정하다: 

*   강체의 질량. 
*   기준점에 관한 질량의 중심의 위치. 현재 구현에서는 질량의 중심과 기준점이 일치해야 한다. 
*   관성 행렬(inertia matrix). 이것은 강체의 질량이 질량 중심 주위로 어떻게 분산되는지를 나타내는 3x3 행렬이다. 

개념적으로 각 강체는 x-y-z 좌표틀(coordinate frame)을 포함하고 있다. 그림1에서 보듯이, 좌표틀은 강체와 함께 움직이고 회전한다.

[![ODE V0.5 사용자 가이드 그림 1](/ko/galexandria/ode-v05-users-guide/images/body.jpg)](/ko/galexandria/ode-v05-users-guide/images/body.jpg)

**그림 1:** 강체 좌표틀. 

이 좌표틀의 원점은 강체의 기준점이다. ODE에서 어떤 값들(벡터, 행렬 등)은 강체 좌표틀에 관련이 있고, 나머지 값들은 전역 좌표틀에 관련이 있다. 

강체의 모양(shape)은 동역학적인 속성이 아니다(다양한 질량 속성에 영향을 주는 점을 제외한다). 강체의 상세한 모양은 충돌 검사에서만 고려된다.

#### 섬과 비활성화된 강체 {#TOC--5}

강체들은 관절에 의해 서로 연결된다. "섬(island)"은 분리될 수 없는 강체들의 그룹이다 - 바꿔 말하면 각 강체는 섬안의 다른 모든 강체들과 연결되어 있는 것이다.   
시뮬레이션 스텝이 진행될 때 월드 안의 각 섬은 따로따로 처리된다. 이것을 알아두는 것이 도움이 된다: 시뮬레이션안에 N 개의 비슷한 섬들이 존재한다면, 스텝 계산 시간은 O(N)이 될 것이다.

각각의 강체는 활성화또는 비활성화될 수 있다. 비활성화된 강체는 사실상 "꺼진" 상태이고, 시뮬레이션 스텝동안 갱신되지 않는다. 강체가 정지한 상태이거나 시뮬레이션과 무관할 때 강체를 비활성화하는 것은 계산 시간을 절약하는 효과적인 방법이다. 

섬 안에 활성화된 강체가 하나라도 있다면, 섬 안의 모든 강체가 다음 시뮬레이션 스텝에서 활성화될 것이다. 따라서 섬을 효과적으로 비활성화하기 위해서, 섬 안의 모든 강체가 비활성화되어야 한다. 만약 비활성화된 섬이 다른 활성화된 강체와 충돌하게 된다면, 접촉 관절이 그 활성화된 강체를 섬에 연결시킴에 따라 전체 섬이 활성화될 것이다.

### 적분 {#TOC--6}

시간의 흐름에 따라 강체 시스템을 시뮬레이션하는 과정을 적분(integration)이라고 한다. 각 적분 step은 주어진 스텝 크기만큼 현재 시간을 진행시킨다. 그리고 새로운 시간 값에 대해 모든 강체의 상태를 조정한다. 적분기로 작업할 때 고려해야 할 점이 2가지가 있다:

* 얼마나 정확한가? 즉, 시뮬레이션되는 시스템의 행동이 실세계에서 일어나는 것과 얼마나 가깝게 일치하는가?   
* 얼마나 안정적인가? 즉, 계산 오류는 항상 시뮬레이션되는 시스템의 완전히 비-물리적인 행동을 야기한다. (즉, 아무 이유없이 시스템이 "폭주"한다) 

ODE의 현재 적분기는 매우 안정적이지만, 스텝 크기가 작지 않으면 정확성이 떨어질 수 있다. 대부분의 ODE 사용에 있어서 이것은 문제가 되지 않는다 -- ODE의 행동은 거의 모든 경우에 완벽하게 현실적으로 보인다. 그러나 ODE는 앞으로 정확성 문제가 완전히 해결되지 않는한 계량 공학의 용도로 사용되어서는 안된다. 

### 힘 누적기 {#TOC--7}

각 적분기 스텝 사이에서 사용자는 강체에 힘을 적용하는 함수들을 호출할 수 있다. 이 힘들은 강체 오브젝트 안의 "힘 누적기(force accumulators)"에 추가된다. 다음 적분기 스텝이 일어날 때, 적용된 모든 힘의 합이 강체를 미는 데 사용될 것이다. 힘 누적기는 각 적분기 스텝이 끝나면 0으로 설정된다. 

### 관절과 구속 {#TOC--8}

실제 생활에서 관절(joint)은 경첩(hinge)과 같은 것이다. 관절은 두 오브젝트를 연결하는 데 쓰인다. ODE에서 관절도 이와 매우 유사하다: 이것은 두 강체 사이에 강제된 하나의 관계이다. 관절은 강체가 서로에 대해 특정한 위치와 회전을 가질 수 있도록 한다. 이 관계를 구속(constraint)이라 한다 -- 관절과 구속이라는 말은 서로 바꿔가며 자주 사용된다. 그림2는 3개의 다른 구속 타입을 보여준다. 

[![ODE V0.5 사용자 가이드 그림 2](/ko/galexandria/ode-v05-users-guide/images/joints.jpg)](/ko/galexandria/ode-v05-users-guide/images/joints.jpg)

**그림 2:** 3가지 다른 구속 타입들.

첫번째 관절은 구상 관절(ball and socket joint)이다. 구상 관절은 하나의 강체 "공(ball)"과 또다른 하나의 강체 "소켓(socket)"이 같은 위치에 있도록 한다. 두번째 관절은 경첩 관절(hinge joint)이다. 경첩 관절은 경첩의 두 부분이 같은 위치에 있도록 그리고 경첩축을 따라 정렬되도록 제한한다. 세번째 관절은 미닫이 관절(slider joint)이다. 미닫이 관절은 "피스톤(piston)"과 "소켓(socket)"을 동일 선상에 오도록 구속하고, 추가로 두 강체가 같은 회전을 갖도록 구속한다. 

적분기가 한 스텝을 거칠 때마다 모든 관절들이 영향을 주는 강체들에 구속력(constraint forces)을 적용하는 것이 허락된다. 이러한 힘들은 강체가 모든 관절 관계를 유지하면서 움직이도록 계산된다. 

각 관절은 그것의 기하구조를 제어하기 위한 수많은 매개 변수들을 가진다. 구상 관절의 위치 벡터가 그 예다. 관절 매개 변수들을 설정하는 함수들은 강체 좌표계(body-relative coordinates)가 아닌 전역 좌표계(global coordinates)를 받아들인다. 그러므로 관절이 연결하는 강체는 관절이 부착되기 전에 올바른 위치에 있어야 한다. 

### 관절 그룹 {#TOC--9}

관절 그룹(joint group)은 관절들을 하나의 그룹으로 보관하는 특별한 컨테이너(container)이다. 관절들은 그룹에 추가될 수 있고, 관절들이 더이상 필요없을 때 한번의 함수 호출로 관절들의 전체 그룹이 매우 빨리 소멸할 수 있다. 그러나, 그룹내의 각각의 관절들은 전체 그룹이 비워질 때까지 소멸할 수 없다. 

이것은 접촉 관절들(contact joints)에 가장 유용하다. 접촉 관절들은 매 시간 스텝마다 그룹으로부터 추가 및 삭제된다. 

### 관절 오류와 오류 감쇠 매개 변수 (ERP; error reduction parameter) {#TOC-ERP-error-reduction-parameter-}

관절이 두 강체에 부착될 때, 강체들은 서로에 대한 특정한 위치와 회전을 갖도록 요구된다. 그러나 관절 구속이 충족되지 않는 위치에 관절이 있을 수도 있다. 이러한 "관절 오류"는 다음 두가지 방식으로 일어날 수 있다: 

1.  사용자가 다른 강체의 위치/회전을 올바로 설정하지 않고 강체의 위치/회전을 설정한 경우 
2.  시뮬레이션 도중, 오류가 점점 증가하여 원하는 위치로부터 강체가 떨어져 있는 경우

그림 3은 구상 관절의 오류를 보여준다. 공과 소켓이 정렬되어 있지 않다.

![ODE V0.5 사용자 가이드 그림 3](/ko/galexandria/ode-v05-users-guide/images/ball-and-socket-bad.jpg)

**그림 3:** 구상 관절의 오류 예.

관절 오류를 줄이는 메카니즘이 있다: 각 시뮬레이션 스텝동안 각 관절이 강체를 올바른 위치로 오도록 끌어당기는 특별한 힘을 적용하는 것이다. 이 힘은 오류 감쇠 매개 변수(ERP; error reduction parameter)에 의해 제어된다. ERP의 값은 0과 1 사이이다. 

ERP는 다음 시뮬레이션 스텝중에 고칠 관절 오류 비율을 나타낸다. ERP=0는 어떠한 조정력도 적용되지 않고 시뮬레이션을 진행함에따라 강체들은 결국 떨어지게 될 것이다. ERP=1이면, 시뮬레이션은 다음 시간 스텝에서 모든 관절 오류를 고치려고 할 것이다. 그러나 ERP=1로 설정하는 것은 바람직하지 않다. 관절 오류는 다양한 내부적 근사에 의해 완벽하게 고쳐질 수 없기 때문이다. ERP를 0.1에서 0.8사이의 값으로 설정하는 것이 바람직하다 (0.2는 기본값). 

시뮬레이션에서 대부분의 관절에 영향을 미치는 전역 ERP 값을 설정할 수 있다. 그러나 어떤 관절들은 다양한 특성을 제어하는 지역 ERP값을 갖기도 한다. 

### 부드러운 구속과 구속 힘 혼합 (CFM; constraint force mixing) {#TOC-CFM-constraint-force-mixing-}

대부분의 구속은 본질적으로 "딱딱하다(hard)". 이것은 구속이 결코 변경될 수 없는 조건을 나타낸다는 의미이다. 예를 들면, 공은 항상 소켓안에 있어야 하고, 경첩의 두 부분은 항상 동일 선상에 정렬되어 있어야 한다. 실제로 구속은 시스템에 의도하지 않은 오류의 발생으로 변경될 수 있다. 그러나 오류 감쇠 매개 변수(error reduction parameter)가 이러한 오류들을 바로잡기 위해 설정될 수 있다. 

모든 구속이 딱딱한 것은 아니다. 어떤 "부드러운(soft)" 구속들은 변경 가능하도록 설계되었다. 예를 들면, 오브젝트들의 충돌로 인해 관통되지 않게 하는 접촉 구속은 기본적으로 딱딱하기 때문에 마치 충돌 표면이 강철로 만들어진 것처럼 보인다. 그러나 좀더 부드러운 재질을 시뮬레이션하기 위해 부드러운 구속을 사용하면, 두 강체가 충돌할 때 어느정도 자연스러운 관통이 허용된다. 

딱딱하고 부드러운 구속들의 구별을 제어하는 두가지 매개 변수가 있다. 첫번째 매개 변수는 이미 소개된 오류 감쇠 매개 변수(ERP)이다. 두번째 매개 변수는 구속 힘 혼합(CFM; constraint force mixing)이다. CFM은 아래에서 설명한다. 

#### 구속 힘 혼합 (CFM) {#TOC-CFM-}

다음은 CFM에 대한 다소 기술적인 설명이다. CFM이 실제로 어떻게 사용되는 지에만 관심이 있다면 다음 절로 넘어가라.   
전통적으로 모든 관절에 대한 구속 방정식은 다음과 같다. 

'' J \* v = c ''

여기서 v는 강체의 속도 벡터이고, J는 관절이 시스템으로부터 제거한 모든 자유도에 대한 1개의 행을 가진 "야코비언(Jacobian)" 행렬이다. c는 우측 벡터이다. 다음 시간 스텝에서, 관절 구속을 유지하기 위해 강체에 적용된 힘은 다음과 같다. 

'' force = J<sup>T</sup> \* lambda ''

ODE는 여기에 새로운 것을 추가한다. ODE의 구속 방정식은 다음과 같다. 

'' J \* v = c + CFM \* lambda ''

여기서 CFM는 대각 행렬(diagonal matrix)이다. CFM는 구속의 결과로 발생하는 구속력을 혼합한다. 0이 아닌 (양의) CFM값은 본래의 구속 방정식을 CFM과 구속에 필요한 복원력(restoring force) lambda의 곱에 비례하는 양 만큼 변경한다. 

''(J M<sup>-1</sup> J<sup>T</sup> + CFM/h) lambda = c/h''

따라서 CFM는 단순히 본래 시스템 행렬의 대각 행렬을 추가한다. 양의 CFM값을 사용하는 것은 시스템의 특이성을 없애는 추가 이점을 가지고 있다. 따라서 인수 분해의 정확도가 개선된다. 

#### ERP와 CFM의 사용법 {#TOC-ERP-CFM-}

ERP와 CFM은 많은 관절들에서 독립적으로 설정될 수 있다. 관절(또는 관절 한계)의 스펀지 같은 푹심함과 용수철 같은 탄력성을 조절하기 위해 접촉 관절들, 관절 한계들 그리고 다양한 곳들에서 설정될 수 있다.

CFM이 0으로 설정되면, 구속이 딱딱할 것이다. CFM이 양수로 설정되면, "밀어붙여서" 구속을 변경할 가능성이 있다. 바꿔말하면 구속이 부드러워 질 것이다. CFM이 증가함에따라 부드러움도 증가한다. 여기서 실제로 일어나는 것은 구속이 CFM과 구속에 필요한 복원력의 곱에 비례하는 양 만큼 변경된다는 것이다. CFM을 음수로 설정하는 것은 불안정성과 같은 바람직하지 못한 나쁜 결과를 가질 수 있다. CFM을 음수로 설정하지 말라. 

ERP와 CFM의 값을 조정함으로써 다양한 효과를 얻을 수 있다. 예를 들면, 용수철과 같은 구속을 시뮬레이션할 수 있다. 이러한 구속에서는 두 강체가 마치 용수철로 연결된 것처럼 흔들거린다. 아니면 진동없는 스펀지같은 구속을 시뮬레이션할 수 있다. 사실, ERP와 CFM는 용수철이나 완충 구속과 같은 효과를 갖기 위해 선택될 수 있다. 용수철 상수(spring constant) kp와 감쇠 상수(damping constant) kd가 있으면 대응하는 ODE 상수는: 

''ERP = h k<sub>p</sub> / (h k<sub>p</sub> + k<sub>d</sub>)  
CFM = 1 / (h k<sub>p</sub> + k<sub>d</sub>)''

여기서 ''h''는 stepsize이다. 이 값들은 불명확한 1차 적분으로 시뮬레이션되는 용수철-완충 시스템과 같은 효과를 가져다준다. 

CFM을 증가하는 것, 특히 전역 CFM을 증가하는 것은 시뮬레이션에서 수치적 오류를 줄여줄 수 있다. 사실, 시스템이 오동작을 하고 있다면, 제일 먼저 해봐야 할 것이 전역 CFM을 증가시키는 것이다. 

### 충돌 처리 {#TOC--10}

\[충돌 처리에 대해 써야 할 게 너무 많다.\] 

강체들 끼리의 충돌과 강체와 정적 환경의 충돌은 다음과 같이 처리된다: 

1.  각 시뮬레이션 스텝 전에, 사용자는 충돌 중인 것들을 알아내기 위해 충돌 검사 함수들을 호출한다. 이 함수들은 접촉점 리스트를 리턴한다. 각 접촉점은 공간에서의 위치, 표면 법선 벡터, 그리고 관통 깊이를 명시한다. 
2.  특별한 접촉 관절이 각 접촉점에 생성된다. 접촉 관절은 접촉에 대해서 추가 정보가 주어진다. 예를 들면, 접촉면이 얼마나 탄력적이거나 부드러운지에 대한 정보와 다양한 다른 속성들에 대한 정보들을 제공한다. 
3.  접촉 관절들은 관절 "그룹"에 넣어진다. 접촉 그룹은 관절들이 매우 빠르게 시스템에서 추가 및 삭제될 수 있게 해준다. 접촉이 늘어날 수록 시뮬레이션 속도가 느려지므로, 접촉점의 수를 제한하는 다양한 전략을 사용한다. 
4.  하나의 시뮬레이션 스텝을 거친다. 
5.  모든 접촉 관절들이 시스템에서 제거된다.

내부 충돌 함수들이 반드시 사용될 필요는 없다 - 올바른 접촉점 정보를 제공하기만 하면 다른 충돌 검사 라이브러리들을 사용할 수도 있다. 

### 전형적인 시뮬레이션 코드 {#TOC--11}

전형적인 시뮬레이션은 이와 같이 진행할 것이다.: 

#동역학 월드를 생성한다.   
#동역학 월드 안에 강체들을 생성한다.   
#모든 강체들의 상태(위치 등)를 설정한다.   
#동역학 월드 안에 관절들을 생성한다.   
#강체에 관절을 부착한다.   
#모든 관절들의 매개 변수들을 설정한다.   
#필요하면, 충돌 월드와 충돌 기하 구조 오브젝트들을 생성한다.   
#접촉 관절들을 보관할 한 개의 관절 그룹을 생성한다.   
#루프:   
    1. 필요에 따라 강체들에 힘을 적용한다.   
    1. 필요에 따라 관절 매개 변수들을 조정한다.   
    1. 충돌 검사를 호출한다.   
    1. 모든 충돌점에 대해 접촉 관절을 생성하고, 접촉 관절 그룹에 추가한다.   
    1. 하나의 시뮬레이션 스텝을 거친다.   
    1. 접촉 관절 그룹 안에 있는 모든 관절들을 제거한다.  
#동역학 월드와 충돌 월드를 소멸시킨다.

### 물리학 모델 {#TOC--12}

ODE에서 사용되는 다양한 방법과 근사들이 이 절에서 논의된다.

#### 마찰 근사 {#TOC--13}

\[여기에 그림이 몇 장 더 필요하다.\] 

쿨롱 마찰 모델은 단순하지만, 접촉점에서 마찰을 모델링하기에 효과적인 방법이다. 이것은 접촉점에 나타는 수직력과 접선력 사이의 단순한 관계이다(이러한 힘들을 설명하는 접촉 관절 절을 보라). 법칙은 이렇다: 

''| f<sub>T</sub> | <= mu \* | f<sub>N</sub> |''

여기서 fN과 fT는 각각 수직력과 법선력 벡터들을 나타낸다. 그리고 mu는 (보통 약 1.0 정도의) 마찰 계수이다. 이 방정식은 "마찰 원뿔"을 정의한다. - fN를 축으로하고 접촉점을 정점으로 하는 원뿔을 상상하라. 전체 마찰력 벡터가 원뿔내에 있으면, 접촉은 "끈적임 모드(sticking mode)"이고 마찰력은 접촉면들이 움직이는 것을 충분히 방지한다. 힘 벡터가 원뿔의 표면 상에 있으면, 접촉은 "미끄러짐 모드(sliding mode)"이고 마찰력은 접촉면들이 미끄러지는 것을 막지 못한다. 따라서 매개 변수 mu는 수직력에 대한 접선력의 최대 비율을 나타낸다. 

ODE의 마찰 모델은 효율성을 이유로한 마찰 원뿔에 대한 근사이다. 현재 선택할 수 있는 2개의 근사법들이 있다: 

1. mu의 의미는 접촉시에 접선의 마찰 방향들 중 한쪽에서 나타날 수 있는 최대한의 마찰 (접선)력을 나타내는 것으로 변경된다. 이것은 수직력과는 관계없기 때문에 비현실적이긴하지만, 유용하게 사용될 수 있고 계산 비용이 가장 싸다. 이 경우에 mu는 시뮬레이션에서 적절히 선택되어야 하는 힘한계이다.   
1. 마찰 원뿔은 첫번째와 두번째 방향에 정렬된 마찰 피라미드에 의해 근사된다. \[여기에 그림이 한장 필요하다.\] : 우선 ODE는 모든 접촉이 마찰이 없는 것처럼 가정하고 수직력들을 계산한다. 그리고 마찰 (접선)력에 대해 최대 한계 fm를 계산한다. 

''f<sub>m</sub> = mu \* | f<sub>N</sub> |''

이러한 고정된 한계들을 가진 전체 시스템에 대해 해를 구한다. 이것은 mu가 고정되지 않는 진짜 마찰 피라미드와 다르다. 이 근사법은 mu를 일반적인 쿨롱 마찰 계수와 같은 비율로 사용한다. 따라서 mu는 특정 시뮬레이션에 관계없이 약 1.0의 일정한 값으로 설정될 수 있다.

## 자료형과 규칙 {#TOC--14}

### 기본 자료형 {#TOC--15}

ODE 라이브러리는 단정도 또는 배정도 실수를 사용하여 빌드될 수 있다. 단정도는 더 빠르고 메모리도 더 적게 사용하지만, 시뮬레이션이 수치적 오류를 더 많이 갖게 되어 눈에 보일 정도의 문제를 야기할 수 있다. 단정도를 사용하면 정확성과 안정성이 더 떨어질 것이다. 

\[어떤 요인들이 정확성과 안정성에 영향을 주는 지를 설명해야 한다\]. 

부동 소수점 데이타 타입은 dReal이다. 자주 사용되는 다른 타입들은 dVector3, dVector4, dMatrix3, dMatrix4, dQuaternion 등이 있다. 

### 오브젝트와 ID {#TOC-ID}

많은 종류의 오브젝트를 생성할 수 있다: 

* dWorld - 동역학계  
* dSpace - 충돌 공간  
* dBody - 강체  
* dGeom - 기하 구조 (충돌용)  
* dJoint - 관절  
* dJointGroup - 관절 그룹

오브젝트를 생성하는 함수들은 ID를 리턴한다. 오브젝트 ID 타입으로 dWorldID, dBodyID 등이 있다. 

### 인자 규칙 {#TOC--16}

모든 벡터3에 대한 "set" 함수는 벡터3 (x, y, z) 또는 x, y, z 값을 별개의 인자로 받는다.

모든 벡터3에 대한 "get" 함수는 dReal 배열에 대한 포인터를 인자로 받는다. 

더 큰 벡터는 dReal 배열에 대한 포인터로 제공되고, 리턴된다. 

모든 좌표값들은 특별히 명시하지 않는 한 전역틀에 속한다. 

### C 대 C++ {#TOC-C-C-}

ODE 라이브러리는 C++로 작성되었지만, 인터페이스는 클래스가 아닌 단순한 C 함수들로 이루어져있다. 왜 그런가?

* C 인터페이스만 사용하는 것이 더 간단하다 – C++ 의 특징들은 ODE에 큰 도움이 되지 않는다.   
* C++의 이름 변경과 많은 컴파일러들에서 나타나는 실시간 지원 문제를 예방한다.   
* ODE를 사용하기위해 C++의 별난 문법과 친해질 필요가 없다.

### 디버깅 {#TOC--17}

ODE 라이브러리는 "디버깅(debugging)" 또는 "릴리즈(release)" 모드로 컴파일될 수 있다. 디버깅 모드는 더 느리지만, 함수 인자를 검사하고 내부 일관성을 확보하기 위해 많은 실시간 검사를 행한다. 릴리즈 모드는 더 빠르지만, 어떤 검사도 하지 않는다.

## 월드 {#TOC--18}

월드 오브젝트는 강체들과 관절들의 컨테이너이다. 다른 월드에 있는 오브젝트들은 상호작용할 수 없다. 예를 들면, 2개의 다른 월드에 속한 강체들은 충돌 할 수 없다.

월드 안의 모든 오브젝트들은 같은 시간 지점에 존재한다. 따라서 별개의 월드를 사용하는 한가지 방법은 시스템들을 다른 비율로 시뮬레이션하는 것이다. 

대부분의 응용 프로그램들은 오직 하나의 월드만을 필요로 할 것이다. 

 dWorldID dWorldCreate();

    새로운 비어있는 월드를 하나 생성하고 ID를 리턴한다.

 void dWorldDestroy (dWorldID);

월드를 소멸시키고 그 안의 모든 것을 소멸시킨다. 모든 강체와 관절 그룹의 일부분이 아닌 모든 관절들을 포함한다. 관절 그룹에 속한 관절들은 비활성화될 것이다. 그리고 [dJointGroupEmpty](#dJointGroupEmpty)를 호출하여 소멸될 수 있다. 

 void dWorldSetGravity (dWorldID, dReal x, dReal y, dReal z);  
 void dWorldGetGravity (dWorldID, dVector3 gravity);

월드의 전역 중력 벡터를 설정/리턴한다. 단위는 m/s/s, 따라서 +z를 위로 가정했을 때 지구의 중력 벡터는 (0,0,-9.81)이다. 기본값은 중력이 없는 상태, 즉 (0,0,0)이다.

 void dWorldSetERP (dWorldID, dReal erp);  
 dReal dWorldGetERP (dWorldID);

전역 오류 감쇠 매개 변수(ERP; error reduction parameter) 값을 설정/리턴한다. ERP는 각 시간 스텝에서 오류를 얼마나 많이 정정할 수 있는 지를 제어한다. 값의 범위는 일반적으로 0.1에서 0.8이다. 기본값은 0.2이다.

 void dWorldSetCFM (dWorldID, dReal cfm);  
 dReal dWorldGetCFM (dWorldID);

전역 구속 힘 혼합(CFM; constraint force mixing) 값을 설정/리턴한다. 일반적인 값은 10-9에서1 사이에 있다. 단정도를 사용하면 기본값이 10-5이고, 배정도를 사용하면 10-10이다.

```cpp
void  dWorldSetAutoDisableFlag (dWorldID, int do_auto_disable);
int   dWorldGetAutoDisableFlag (dWorldID);
void  dWorldSetAutoDisableLinearThreshold (dWorldID, dReal linear_threshold);
dReal dWorldGetAutoDisableLinearThreshold (dWorldID);
void  dWorldSetAutoDisableAngularThreshold (dWorldID, dReal angular_threshold);
dReal dWorldGetAutoDisableAngularThreshold (dWorldID);
void  dWorldSetAutoDisableSteps (dWorldID, int steps);
int   dWorldGetAutoDisableSteps (dWorldID);
void  dWorldSetAutoDisableTime (dWorldID, dReal time);
dReal dWorldGetAutoDisableTime (dWorldID);
```

새로 생성되는 강체에 대한 기본 자동-비활성화 매개 변수들을 설정/리턴한다. 자동-비활성화 특성에 대해 [6.5절](#자동-활성화와-비활성화)을 보라. 기본 매개 변수들은 다음과 같다: 

* AutoDisableFlag = disabled   
* AutoDisableLinearThreshold = 0.01   
* AutoDisableAngularThreshold = 0.01   
* AutoDisableSteps = 10   
* AutoDisableTime = 0 

```cpp
void dWorldImpulseToForce (dWorldID, dReal stepsize,
              dReal ix, dReal iy, dReal iz, dVector3 force);
```

강체에 선 또는 각 충격량을 적용하고 싶을 때, 힘이나 돌림힘 대신에 원하는 충격량을 힘/돌림힘 벡터로 변환하기 위해 이 함수를 사용할 수 있다.

이 함수는 원하는 충격량을 (ix,iy,iz)로 설정하고, 변환한 힘을 force 벡터에 넣는다. 현재의 알고리즘은 단순히 충격량을 stepsize로 나누기만 한다. 여기서 stepsize는 다음 스텝의 크기이다. 

이 함수는 dWorldID를 받는다. 향후에 힘 계산이 월드의 속성으로 설정된 적분기 매개 변수들에 좌우될지도 모르기 때문이다.

 void dCloseODE();

ODE에 의해 사용된 메모리를 해제한다. 프로그램 종료 전에 사용하면 된다.

### 스텝 함수 {#TOC--19}

<a id="dWorldStep"></a>

```cpp
void dWorldStep (dWorldID, dReal stepsize);
```

월드를 한 스텝 진행한다. 이것은 m3 차수의 시간을 소모하고 m2 차수의 메모리를 사용하는 "큰 행렬(big matrix)" 법을 사용한다. 여기서 m는 전체 구속 행 수이다. 

커다란 시스템에 대해서 이것은 아주 많은 메모리를 사용하고 매우 느릴 것이다. 하지만 현재 가장 정확한 방법이다. 

<a id="dWorldQuickStep"></a>

```cpp
void dWorldQuickStep (dWorldID, dReal stepsize);
```

월드를 한 스텝 진행한다. m\*N 차수의 시간과 m 차수의 메모리를 소모하는 반복적인 방법을 사용한다. 여기서 m는 전체 구속 행 수이고 N는 반복횟수이다.

커다란 시스템에 대해서 이 함수가 [dWorldStep](#dWorldStep)보다 훨씬 더 빠르다. 하지만 정확성은 더 떨어진다. 

QuickStep는 오브젝트 더미들에 좋은 성능을 발휘한다. 특히 자동-비활성화 특성이 함께 사용되면 더 좋다. 그러나 근-특이 시스템(near-singular systems)들에 대해서는 취약한 정확도를 가진다. 고-마찰 접촉, 모터, 특정 관절구조를 사용할 때 근-특이 시스템이 나타날 수 있다. 예를 들면, 다수의 다리를 가진 로봇이 땅위에 서있을 때 근-특이성을 가진다. 

QuickStep의 부정확성 문제를 극복하기 위한 방법들이 있다: 

* CFM을 늘린다.   
* 시스템에서 접촉 수를 줄인다(즉, 로봇이나 생물의 발에 대한 최소한의 접촉 수를 사용한다).   
* 접촉시 과도한 마찰을 사용하지 않는다.   
* 적절한 접촉 미끄러짐을 사용한다.   
* 운동학 루프를 피한다(그러나, 운동학 루프는 다리가 달린 생물체에서는 피할 수가 없다).   
* 과도한 모터 힘을 사용하지 않는다.   
* 속도 기반 모터 대신 힘 기반 모터를 사용한다. 

QuickStep의 반복 횟수를 증가하는 것이 조금 도움이 될 것이다. 하지만 시스템이 특이점에 가깝다면 별 도움이 되지 않을 것이다. 

 void dWorldSetQuickStepNumIterations (dWorldID, int num);  
 int dWorldGetQuickStepNumIterations (dWorldID);

QuickStep 함수가 스텝 당 실행하는 횟수를 설정/리턴한다. 더 많은 반복은 더 정확한 해를 줄 것이다. 하지만 계산 시간은 더 오래 걸릴 것이다. 기본값은 20이다.

### 접촉 매개 변수 {#TOC--20}

```cpp
void dWorldSetContactMaxCorrectingVel (dWorldID, dReal vel);
dReal dWorldGetContactMaxCorrectingVel (dWorldID);
```

접촉이 생성하는 허락된 최대 정정 속도를 설정/리턴한다. 기본값은 무한대이다. 이 값을 줄이는 것은 깊게 묻힌 오브젝트들이 "튀는(popping)" 것을 방지하는 데 도움을 줄 수 있다.

 void dWorldSetContactSurfaceLayer (dWorldID, dReal depth);  
 dReal dWorldGetContactSurfaceLayer (dWorldID);

모든 기하 구조 오브젝트의 표면 층의 깊이를 설정/리턴한다. 접촉시 정지할 때까지 표면을 주어진 깊이까지 파고 드는 것을 허락한다. 이것을 증가하는 것은 반복적인 접촉에 대해 떨림 문제(jittering problems)를 방지하는 데 도움을 줄 수 있다.

## 강체 함수 {#TOC--21}

### 강체의 생성과 소멸

 dBodyID dBodyCreate (dWorldID);

주어진 월드의 (0,0,0) 위치에 기본 질량을 가진 강체를 생성하고 ID를 리턴한다.

 void dBodyDestroy (dBodyID);

강체를 소멸시킨다. 강체에 부착된 모든 관절들을 림보(limbo)에 넣는다. (즉, 시뮬레이션에 영향을 주지는 않으나 제거또한 되지 않는다.)

### 위치와 회전 {#위치와-회전}

<a id="dBodySetPosition"></a>  
 void dBodySetPosition   (dBodyID, dReal x, dReal y, dReal z);  
<a id="dBodySetRotation"></a>  
 void dBodySetRotation   (dBodyID, const dMatrix3 R);  
<a id="dBodySetQuaternion"></a>  
 void dBodySetQuaternion (dBodyID, const dQuaternion q);  
 void dBodySetLinearVel  (dBodyID, dReal x, dReal y, dReal z);  
 void dBodySetAngularVel (dBodyID, dReal x, dReal y, dReal z);  
<a id="dBodyGetPosition"></a>  
 const dReal \* dBodyGetPosition   (dBodyID);  
<a id="dBodyGetRotation"></a>  
 const dReal \* dBodyGetRotation   (dBodyID);  
<a id="dBodyGetQuaternion"></a>  
 const dReal \* dBodyGetQuaternion (dBodyID);  
 const dReal \* dBodyGetLinearVel  (dBodyID);  
 const dReal \* dBodyGetAngularVel (dBodyID);

이 함수들은 강체의 위치, 회전, 선속도와 각속도를 설정하거나 얻는다. 여러 강체들을 설정한 후, 새로운 구성이 현재의 관절/구속과 어울리지 않을 경우 시뮬레이션의 결과는 알 수 없다. dBodyGet-함수를 사용할 때, 리턴 값은 내부 자료 구조에 대한 포인터이다. 그러므로 변경된 값들이 모두 강체 시뮬레이션 구조체에 반영되기까지는 유효하다. 

흠. dBodyGetRotation는 4x3 회전 행렬을 리턴한다.

### 질량과 힘 {#질량과-힘}

```cpp
void dBodySetMass (dBodyID, const dMass *mass);
void dBodyGetMass (dBodyID, dMass *mass);
```

강체의 질량을 설정/리턴한다(질량 함수들을 보라).

<a id="dBodyAddForce"></a>  
<a id="dBodyAddTorque"></a>  

```cpp
void dBodyAddForce            (dBodyID, dReal fx, dReal fy, dReal fz);
void dBodyAddTorque           (dBodyID, dReal fx, dReal fy, dReal fz);
void dBodyAddRelForce         (dBodyID, dReal fx, dReal fy, dReal fz);
void dBodyAddRelTorque        (dBodyID, dReal fx, dReal fy, dReal fz);
void dBodyAddForceAtPos       (dBodyID, dReal fx, dReal fy, dReal fz,
                                       dReal px, dReal py, dReal pz);
void dBodyAddForceAtRelPos    (dBodyID, dReal fx, dReal fy, dReal fz,
                                       dReal px, dReal py, dReal pz);
void dBodyAddRelForceAtPos    (dBodyID, dReal fx, dReal fy, dReal fz,
                                       dReal px, dReal py, dReal pz);
void dBodyAddRelForceAtRelPos (dBodyID, dReal fx, dReal fy, dReal fz,
                                       dReal px, dReal py, dReal pz);
```

절대적이거나 상대적인 좌표값들로 강체에 힘을 추가한다. 힘은 각 강체에 누적되고, 매 시간 스텝 후에는 누적치를 0으로 한다. 

...RelForce 와 ...RelTorque 함수들은 강체의 기준틀에 관련된 힘 벡터들을 받아들인다. 

...ForceAtPos 와 ...ForceAtRelPos 함수들은 각각 전역또는 강체 좌표계에 대해서 추가 위치 벡터를 받아들인다. 위치 벡터는 힘이 적용되는 지점을 명시한다. 다른 모든 함수들은 질량의 중심에 힘을 적용한다. 

```cpp
const dReal * dBodyGetForce  (dBodyID);
const dReal * dBodyGetTorque (dBodyID);
```

현재 누적된 힘과 돌림힘 벡터를 리턴한다. 3개 dReal로 된 배열에 대한 포인터를 리턴한다. 리턴값은 내부 데이타 구조체에 대한 포인터이다. 벡터값은 강체 시스템에 대한 변화가 반영될 때까지 유효하다.

```cpp
void dBodySetForce  (dBodyID b, dReal x, dReal y, dReal z);
void dBodySetTorque (dBodyID b, dReal x, dReal y, dReal z);
```

강체 힘과 돌림힘 누적 벡터를 설정한다. 강체를 비활성화하기 위해 힘과 돌림힘을 0으로 만들 때 주로 유용하다.

### 유용한 함수 {#유용한-함수}

```cpp
void dBodyGetRelPointPos (dBodyID, dReal px, dReal py, dReal pz,
                         dVector3 result);
void dBodyGetRelPointVel (dBodyID, dReal px, dReal py, dReal pz,
                         dVector3 result);
void dBodyGetPointVel    (dBodyID, dReal px, dReal py, dReal pz,
                         dVector3 result);
```

강체의 한 점(px,py,pz)를 받아서 그 점의 위치와 속도를 전역 좌표로 result에 리턴한다. dBodyGetRelPointXXX 함수들은 강체 좌표계의 점을 인자로 받는다. dBodyGetPointVel 함수들은 전역 좌표계에서 점을 인자로 받는다.

```cpp
void dBodyGetPosRelPoint (dBodyID, dReal px, dReal py, dReal pz,
                           dVector3 result);
```

dBodyGetRelPointPos의 반대이다. 전역 좌표의 (x,y,z)를 받아서 강체 좌표에서의 점의 위치를 result에 리턴한다.

```cpp
void dBodyVectorToWorld   (dBodyID, dReal px, dReal py, dReal pz,
                          dVector3 result);
void dBodyVectorFromWorld (dBodyID, dReal px, dReal py, dReal pz,
                          dVector3 result);
```

강체(월드) 좌표계에서 표현된 벡터 (x,y,z)를 받아서, 월드(강체) 좌표로 변환하여 그 결과를 result에 리턴한다.  

### 자동 활성화와 비활성화 {#자동-활성화와-비활성화}

모든 강체는 활성화되거나 비활성화될 수 있다. 활성화된 강체들은 시뮬레이션에 참가하고, 반면 비활성화된 강체들은 꺼진 상태라서 시뮬레이션 스텝 동안 갱신되지 않는다. 새로운 강체들은 항상 활성화된 상태로 생성된다.   
관절을 통해 활성화된 강체에 연결된 비활성화된 강체는 다음 시뮬레이션 스텝에서 자동적으로 재활성화된다. 

비활성화된 강체는 CPU 시간을 소모하지 않는다. 따라서 시뮬레이션 속도를 높이기 위해 강체가 정지된 상태에 이르면 비활성화돼야 한다. 이것이 자동-비활성화 특성으로 자동으로 행해진다. 

어떤 강체가 자동-비활성화 플랙을 켜놓았다면, 다음 경우에 자동으로 비활성화될 것이다. 

#주어진 시뮬레이션 스텝 수 동안 유휴상태인 경우.   
#주어진 시뮬레이션 시간 동안 유휴상태인 경우.

강체의 선속도와 각속도의 양이 모두 주어진 경계 이하에 있을 때 유휴 상태에 있다고 생각할 수 있다. 

따라서, 모든 강체는 5개의 자동-비활성화 매개 변수들을 가진다: 활성화된 플랙, 유휴 스텝 카운트, 유휴 시간, 그리고 선/각 속도 경계. 새로 생성된 강체들은 월드로부터 이러한 매개 변수들을 가진다. 

다음 함수들은 강체의 활성화/비활성화 매개 변수를 설정/리턴한다. 

```cpp
void dBodyEnable (dBodyID);
void dBodyDisable (dBodyID);
```

강체를 수동으로 활성화/비활성화 시킨다. 관절을 통해 활성화된 강체에 연결된 비활성화된 강체는 다음 시뮬레이션 스텝에서 자동으로 재활성화될 것이다.

 int dBodyIsEnabled (dBodyID);

강체가 현재 활성화돼 있으면 1를 리턴하고, 그렇지 않으면 0을 리턴한다.

 void  dBodySetAutoDisableFlag (dBodyID, int do\_auto\_disable);  
 int   dBodyGetAutoDisableFlag (dBodyID);

강체의 자동-비활성화 플랙을 설정/리턴한다. do\_auto\_disable이 0이면, 강체가 오랫동안 유휴상태로 있을 때 자동으로 비활성화된다.

 void  dBodySetAutoDisableLinearThreshold (dBodyID, dReal linear\_threshold);  
 dReal dBodyGetAutoDisableLinearThreshold (dBodyID);

자동 비활성화를 위한 강체의 선속도 경계치를 설정/리턴한다. 강체의 선속도 크기가 경계치보다 작아야 유휴 상태에 있다고 생각할 수 있다. 경계치를 dInfinity로 설정하면 선속도를 고려 대상에서 제외한다. 

 void  dBodySetAutoDisableAngularThreshold (dBodyID, dReal angular\_threshold);  
 dReal dBodyGetAutoDisableAngularThreshold (dBodyID);

자동 비활성화를 위한 강체의 각속도 경계를 설정/리턴한다. 강체의 각속도 크기가 경계치보다 작아야 유휴 상태에 있다고 생각할 수 있다. 경계치를 dInfinity로 설정하면 각속도를 고려 대상에서 제외한다. 

 void  dBodySetAutoDisableSteps (dBodyID, int steps);  
 int   dBodyGetAutoDisableSteps (dBodyID);

강체가 유휴 상태에 있어야 할 시뮬레이션 스텝 수를 설정/리턴한다. 이 값을 0으로 설정하면 스텝 수를 고려 대상에서 제외한다.

 void  dBodySetAutoDisableTime (dBodyID, dReal time);  
 dReal dBodyGetAutoDisableTime (dBodyID);

강체가 유휴 상태에 있어야 할 시뮬레이션 시간을 설정/리턴한다. 이 값을 0으로 설정하면 시뮬레이션 시간을 고려 대상에서 제외한다.

 void  dBodySetAutoDisableDefaults (dBodyID);

강체의 자동-비활성화 매개 변수들을 기본값으로 설정한다.

### 기타 강체 함수들 {#기타-강체-함수들}

 void  dBodySetData (dBodyID, void \*data);  
 void \*dBodyGetData (dBodyID);

강체의 사용자 데이타 포인터를 설정/리턴한다.

<a id="dBodySetFiniteRotationMode"></a>  
 void dBodySetFiniteRotationMode (dBodyID, int mode);

이 함수는 강체의 회전이 매 시간 스텝마다 갱신되는 방식을 제어한다. mode 인자는 다음과 같다:   
* 0: "무한소(無限小; infinitesimal)"의 회전 갱신을 사용한다. 계산이 빠르지만, 빠른 속도로 회전하는 강체에 대해 가끔 부정확한 결과를 유발할 수 있다. 특히 다른 강체에 연결된 강체의 경우 두드러진다. 새로 생성된 강체에 대해 기본값으로 사용된다.   
* 1: "유한(有限; finite)"한 회전 갱신을 사용한다. 계산하는 데 더 많은 비용이 들지만, 빠른 속도의 회전에 대해 더 정확하다. 그렇지만 빠른 속도의 회전은 시뮬레이션에서 많은 오류를 야기할 수 있다. 그리고 이 모드는 여러 오류들 중 한가지만 고칠 수 있을 것이다.

 int dBodyGetFiniteRotationMode (dBodyID);

강체의 현재 유한 회전 모드를 리턴한다. (0 또는 1).

<a id="dBodySetFiniteRotationAxis"></a>  
 void dBodySetFiniteRotationAxis (dBodyID, dReal x, dReal y, dReal z);

강체의 유한 회전 축을 설정한다. 유한 회전 모드가 설정되었을 때만 의미가 있다 ([dBodySetFiniteRotationMode](#dBodySetFiniteRotationMode) 참고). 

축이 (0,0,0)이면, 강체에 완전한 유한 회전을 수행한다. 

축이 영이 아니면, 강체는 축방향을 따라 부분적인 유한 회전을 하고나서, 직각 방향을 따라 무한소의 회전을 한다. 

이는 빠르게 회전하는 강체에 발생하는 오류들을 완화시키는 데 유용하다. 예를 들어, 빠른 속도로 회전하는 차바퀴가 있다면, 바퀴의 움직임을 개선하기위한 인자로서 바퀴의 경첩 관절축에 이 함수를 호출할 수 있다. 

 void dBodyGetFiniteRotationAxis (dBodyID, dVector3 result);

강체의 현재 유한 회전축을 리턴한다.

<a id="dBodyGetNumJoints"></a>  
 int dBodyGetNumJoints (dBodyID b);

강체에 부착된 관절의 수를 리턴한다. 

 dJointID dBodyGetJoint (dBodyID, int index);

강체에 부착된 index번째 관절을 리턴한다. 유효한 인덱스는 0에서 n-1사이이다. 여기서 n은 [dBodyGetNumJoints](#dBodyGetNumJoints)에 의해 리턴된 값이다. 

 void dBodySetGravityMode (dBodyID b, int mode);  
 int dBodyGetGravityMode (dBodyID b);

강체가 월드의 중력에 의해 영향을 받는지 여부를 설정/리턴한다. mode가 0이 아니면 중력의 영향을 받고, 0이면 영향을 받지 않는다. 새로 생성된 강체는 항상 중력의 영향을 받는다.

## 관절 타입과 함수 {#관절-타입과-함수}

### 관절의 생성과 소멸 {#관절의-생성과-소멸}

```cpp
dJointID dJointCreateBall (dWorldID, dJointGroupID);
dJointID dJointCreateHinge (dWorldID, dJointGroupID);
dJointID dJointCreateSlider (dWorldID, dJointGroupID);
dJointID dJointCreateContact (dWorldID, dJointGroupID,
                             const dContact *);
dJointID dJointCreateUniversal (dWorldID, dJointGroupID);
dJointID dJointCreateHinge2 (dWorldID, dJointGroupID);
dJointID dJointCreateFixed (dWorldID, dJointGroupID);
dJointID dJointCreateAMotor (dWorldID, dJointGroupID);
```

주어진 타입의 새로운 관절을 생성한다. 이 관절은 처음에 강체에 연결된 상태가 아니기 때문에 "림보(limbo)"(시뮬레이션에 아무런 영향을 주지 않는)에 존재한다. 일반적으로 관절 그룹 ID는 0이다. 0이 아니면 특정 관절 그룹에 할당된다. 접촉 관절은 주어진 dContact 구조체를 가지고 초기화된다. 

void dJointDestroy (dJointID);

관절을 소멸시키고 강체와 연결을 끊고 월드로부터 제거한다. 그러나, 관절이 그룹의 일원이라면, 이 함수는 아무 효력이 없다. - 그런 관절을 소멸시키려면 그룹을 비우거나 소멸시켜야 한다. 

dJointGroupID dJointGroupCreate (int max\_size);

관절 그룹을 생성한다. max\_size 인자는 현재 사용되지 않으며 0으로 설정해야 한다. 이는 이전 버전과의 호환성을 위해 남겨진 것이다. 

void dJointGroupDestroy (dJointGroupID);

관절 그룹을 소멸시킨다. 그룹 안의 모든 관절들이 소멸할 것이다.   
<a id="dJointGroupEmpty"></a>

void dJointGroupEmpty (dJointGroupID);

관절 그룹을 비운다. 그룹 안의 모든 관절들이 소멸하지만, 그룹 자체는 소멸하지 않을 것이다. 

### 기타 관절 함수 {#기타-관절-함수}

<a id="dJointAttach"></a>  
void dJointAttach (dJointID, dBodyID body1, dBodyID body2);

새 강체에 관절을 부착한다. 관절이 이미 부착되어있으면, 예전의 강체로부터 떼어낸다. 관절을 하나의 강체에만 부착하려면, body1 또는 body2를 0으로 설정하면된다. - 0인 강체는 정적 환경을 말한다. 두 강체를 모두 0으로 설정하면 관절이 "림보"에 들어간다. 즉, 시뮬레이션에 영향을 주지 않는다. 

경첩 관절-2 같은 몇몇 관절들은 제대로 작동하려면 두 개의 강체에 부착되어야 한다. 

void dJointSetData (dJointID, void \*data);  
void \*dJointGetData (dJointID);

관절의 사용자 데이타 포인터를 설정/리턴한다.

int dJointGetType (dJointID);

관절의 타입을 얻는다. 다음 상수들 중 하나가 리턴된다: 

{| style="text-align:left" border=1  
| dJointTypeBall || 구상 관절(ball-and-socket joint)  
|-   
|dJointTypeHinge ||경첩 관절(hinge joint)  
|-   
|dJointTypeSlider ||미닫이 관절(slider joint)   
|-  
|dJointTypeContact ||접촉 관절(contact joint)  
|-  
|dJointTypeUniversal|| 십자축 관절(universal joint)  
|-  
|dJointTypeHinge2 ||경첩-2 관절(hinge-2 joint)  
|-  
|dJointTypeFixed|| 고정 관절(fixed joint)  
|-  
|dJointTypeAMotor|| 각모터 관절(angular motor joint)  
|}

dBodyID dJointGetBody (dJointID, int index);

관절이 연결한 강체들을 리턴한다. index가 0이면 "첫번째" 강체를 리턴한다. 이는 [dJointAttach](#dJointAttach)의 body1 인자와 일치한다. index가 1이면 "두번째" 강체를 리턴한다. 이는 [dJointAttach](#dJointAttach)의 body2 인자와 일치한다. 

리턴된 강체 ID 중 하나가 0이면, 관절이 강체를 정적 환경과 연결한 것이다. 두 강체 ID 모두 0이면, 관절은 "림보"에 있는 것이고, 시뮬레이션에 영향을 주지 않는다. 

void dJointSetFeedback (dJointID, dJointFeedback \*);  
dJointFeedback \*dJointGetFeedback (dJointID);

월드의 시간 스텝 동안, 각각의 관절에 의해 적용되는 힘들이 계산된다. 이 힘들은 연결된 강체에 직접 더해지고, 사용자는 보통 관절이 얼마나 많은 힘을 부여하는 지 알 길이 없다. 

이러한 정보가 필요하다면, 사용자는 dJointFeedback 구조체를 할당해서 그 포인터를 dJointSetFeedback() 함수에 넘길 수 있다. 피드백 정보 구조체는 다음과 같이 정의된다: 

```cpp
typedef struct dJointFeedback {
 dVector3 f1;       // force that joint applies to body 1
 dVector3 t1;       // torque that joint applies to body 1
 dVector3 f2;       // force that joint applies to body 2
 dVector3 t2;       // torque that joint applies to body 2
} dJointFeedback;
```

시간 스텝 중에 관절에 부착된 피드백 구조체는 관절의 힘과 돌림힘 정보로 채워질 것이다. dJointGetFeedback() 함수는 현재 피드백 구조체 포인터를 리턴하거나 아무것도 사용되지 않으면(이게 기본값), 0을 리턴한다. 관절에 대한 피드백을 비활성화시키기위해 dJointSetFeedback()에 0을 인자로 넘길 수 있다. 

API 디자인에 잠깐 주목해보자. 사용자가 이러한 구조체들에 할당을 하도록 하는 것이 이상해 보일 수도 있다. 그냥 각 관절에 정적으로 데이타를 저장하면 안되나? 그렇게 하지 않는 이유는 모든 사용자가 피드백 정보를 사용하지 않을 것이고, 심지어 모든 사용자가 사용하더라도 모든 관절이 필요로 하지는 않을 것이기 때문이다. 정적으로 정보를 저장하는 것은 메모리를 낭비하게된다. 특히 앞으로 이 구조체가 추가 정보를 저장하기 위해 크기가 커질 수도 있다. 

그럼 사용자가 요청할 때 ODE가 직접 구조체를 할당하면 되지 않나? 그렇게 하지 않는 이유는 (시뮬레이션 단계마다 생성되고 소멸하는) 접촉 관절은 피드백을 요청할 경우 메모리 할당에 많은 시간이 걸릴 것이기 때문이다. 사용자로 하여금 할당하도록 하는 것이 더 나은 정책이다. 즉, 사용자는 단순히 고정 배열로부터 구조체를 할당하면 된다. 

이러한 API에 대한 대안은 관절-힘 콜백 함수를 갖는 것이다. 물론 잘 동작하지만 약간의 문제를 가지고 있다. 첫째, 콜백 함수는 API를 더럽히고 때때로 사용자가 데이타를 얻기위해 부자연스러운 왜곡을 거쳐야한다. 둘째, ODE가 시뮬레이션 과정 중에 변경되면 안좋은 결과를 갖게 될 수 있다. 이러한 상황에 대비책을 마련하거나 디버깅 검사가 필요하다 - 이것은 복잡한 문제다. 

int dAreConnected (dBodyID, dBodyID);

유용한 함수: 두 강체가 관절에 의해 서로 연결되어 있다면 1을 리턴하고, 그렇지 않으면 0을 리턴한다. 

int dAreConnectedExcluding (dBodyID, dBodyID, int joint\_type);

유용한 함수: 두 강체가 joint\_type이 아닌 관절에 의해 연결되어 있다면 1을 리턴하고, 그렇지 않으면 0을 리턴한다. joint\_type은 dJointTypeXXX 상수이다. 이 함수는 두 강체 사이에 접촉 관절을 추가할 지를 결정할 때 유용하다: 만약 두 강체가 이미 접촉 관절이 아닌 다른 관절로 연결되어있다면, 접촉 관절을 추가하는 것은 적절하지 않을 것이다. 그러나 접촉 관절을 가지고 있는 강체 사이에 접촉 관절을 더 추가하는 것은 괜찮다. 

### 관절 매개 변수 설정 함수 {#관절-매개-변수-설정-함수}

#### 구상 관절 {#구상-관절}

그림 4에 구상 관절(ball and socket joint)이 나와 있다. 

![ODE V0.5 사용자 가이드 그림 4](/ko/galexandria/ode-v05-users-guide/images/ball-and-socket.jpg)

**그림 4:** 구상 관절

void dJointSetBallAnchor (dJointID, dReal x, dReal y, dReal z);

관절 고정점을 설정한다. 관절은 두 강체를 연결하기 위해 고정점을 유지한다. 입력값은 월드 좌표계의 좌표이다.   
<a id="dJointGetBallAnchor"></a>  
void dJointGetBallAnchor (dJointID, dVector3 result);

월드 좌표계에서 body1의 관절 고정점을 얻는다. 관절이 완벽하게 만족되면, body2의 점과 똑같을 것이다.   
<a id="dJointGetBallAnchor2"></a>  
void dJointGetBallAnchor2 (dJointID, dVector3 result);

월드 좌표계에서 body2의 관절 고정점을 얻는다. 관절이 완벽하게 만족되면 [dJointGetBallAnchor](#dJointGetBallAnchor)와 [dJointGetBallAnchor2](#dJointGetBallAnchor2)의 결과값이 반올림 오차내에서 같다고 볼 수 있다.

#### 경첩 관절 {#경첩-관절}

그림 5에 경첩 관절(hinge joint)이 나와 있다. 

![ODE V0.5 사용자 가이드 그림 5](/ko/galexandria/ode-v05-users-guide/images/hinge.jpg)

**그림 5:** 경첩 관절

void dJointSetHingeAnchor (dJointID, dReal x, dReal y, dReal z);  
void dJointSetHingeAxis (dJointID, dReal x, dReal y, dReal z);

경첩 관절 고정점과 고정축 매개 변수를 설정한다.   
<a id="dJointGetHingeAnchor"></a>  
void dJointGetHingeAnchor (dJointID, dVector3 result);

월드 좌표계에서 body1의 관절 고정점을 얻는다. 관절이 완벽하게 만족되면, body2의 점과 똑같을 것이다. 

void dJointGetHingeAnchor2 (dJointID, dVector3 result);

월드 좌표계에서 body2의 관절 고정점을 얻는다. 관절이 완벽하게 만족되면, [dJointGetHingeAnchor](#dJointGetHingeAnchor)의 리턴값과 똑같을 것이다. 그렇지 않으면, 리턴값이 약간 다를 것이다. 이것은 관절이 얼마나 떨어져있는지를 알아내는 데 사용할 수 있다.

void dJointGetHingeAxis (dJointID, dVector3 result);

경첩 관절축 매개 변수를 얻는다.

dReal dJointGetHingeAngle (dJointID);  
dReal dJointGetHingeAngleRate (dJointID);

경첩 관절의 각과 시간 변화율을 얻는다. 각도는 두 강체 또는 강체와 정적 환경의 사잇각으로 -pi와 pi 사이의 값이다. 

경첩 관절 고정점 또는 축이 설정되었을 때, 부착된 강체의 현재 위치가 검사되고 그 위치는 0도가 될 것이다. 

#### 미닫이 관절 {#미닫이-관절}

그림 6에 미닫이 관절이 나와 있다. 

![ODE V0.5 사용자 가이드 그림 6](/ko/galexandria/ode-v05-users-guide/images/slider.jpg)

**그림 6:** 미닫이 관절

void dJointSetSliderAxis (dJointID, dReal x, dReal y, dReal z);

미닫이 관절 축 매개 변수를 설정한다.

void dJointGetSliderAxis (dJointID, dVector3 result);

미닫이 관절 축 매개 변수를 얻는다.

dReal dJointGetSliderPosition (dJointID);  
dReal dJointGetSliderPositionRate (dJointID);

미닫이 관절의 선형 위치와 시간 변화율을 얻는다. 

축이 설정되면, 부착된 강체의 현재 위치가 검사되고 그 위치는 0이 될것이다. 

#### 십자축 관절 {#십자축-관절}

그림 7에 십자축 관절이 나와 있다. 

![ODE V0.5 사용자 가이드 그림 7](/ko/galexandria/ode-v05-users-guide/images/universal.jpg)

**그림 7:** 십자축 관절

십자축 관절은 추가적인 회전 자유도를 억압한 볼-소켓 관절과 비슷하다. body1에 axis1을 설정하고, body2에 axis1에 수직인 axis2를 설정하고 그 상태를 유지한다. 바꿔 말하면, 두 축에 수직인 방향에 대한 두 강체의 회전이 같다. 

그림에서, 두 강체는 십자가로 연결되어있다. axis1은 body1에 부착되고, axis2는 body2에 부착된다. 십자가는 두 축을 직각으로 유지한다. 그러므로 body1을 잡고 비틀면, body2 또한 비틀어진다. 

십자축 관절 축이 서로 수직이고 완충 지점에서 견고하게 연결된 경첩-2 관절과 동일하다. 

십자축 관절은 자동차에서 볼 수 있다. 엔진은 자신의 축을 따라 회전하는 굴대와 손잡이에 영향을 준다. 굴대의 방향을 바꾸고 싶을 때가 있을 것이다. 그런데 문제는 굴대를 구부리면, 굽은 곳 이후의 부분은 자신의 축을 따라 회전하지 않을 것이다. 그래서 구부러진 위치를 잘라버리고 그 부분에 십자축 관절을 넣으면, 첫번째 굴대의 각도만큼 두번째 굴대를 회전시키는 구속력을 사용할 수 있게 된다. 

팔을 쫙 뻗고 있는 사람을 상상해보라. 팔을 위 아래와 앞 뒤로 움직일 수 있지만 팔의 축에 대해 회전할 수는 없다. 

다음은 십자축 관절 함수들이다: 

void dJointSetUniversalAnchor (dJointID, dReal x, dReal y, dReal z);  
void dJointSetUniversalAxis1 (dJointID, dReal x, dReal y, dReal z);  
void dJointSetUniversalAxis2 (dJointID, dReal x, dReal y, dReal z);

십자축 관절 고정점과 고정축 매개 변수를 설정한다. axis1과 axis2는 서로 수직이어야 한다.   
<a id="dJointGetUniversalAnchor"></a>  
void dJointGetUniversalAnchor (dJointID, dVector3 result);

월드 좌표계에서 body1의 관절 고정점을 얻는다. 관절이 완벽하게 만족되면, body2의 고정점과 똑같을 것이다.  
<a id="dJointGetUniversalAnchor2"></a>  
void dJointGetUniversalAnchor2 (dJointID, dVector3 result);

월드 좌표계에서 body2의 관절 고정점을 얻는다. 관절이 완벽하게 만족된다면, [dJointGetUniversalAnchor](#dJointGetUniversalAnchor)의 리턴값이 반올림 오차범위내에서 같다고 볼 수 있다. [dJointGetUniversalAnchor2](#dJointGetUniversalAnchor2)는 관절이 얼마나 떨어져 있는지를 알아보는 데 사용할 수 있다. 

void dJointGetUniversalAxis1 (dJointID, dVector3 result);  
void dJointGetUniversalAxis2 (dJointID, dVector3 result);

십자축 관절 축 매개 변수를 얻는다.

#### 경첩-2 {#경첩-2}

그림 8에 경첩-2 관절이 나와 있다. 

![ODE V0.5 사용자 가이드 그림 8](/ko/galexandria/ode-v05-users-guide/images/hinge2.jpg)

**그림 8:** 경첩-2 관절

경첩-2 관절은 각자 다른 축을 가진 두개의 경첩 관절이 연속으로 연결되어있는 것과 같다. 예를 들면, 위 그림을 자동차 핸들이라고 보면, 하나의 축이 바퀴의 방향을 틀고, 다른 축은 바퀴가 회전하도록 한다. 

경첩-2 관절은 한개의 고정점과 두개의 경첩 관절 축을 가진다. axis1은 body1에 상대적으로 특화된 것이고(body1이 차라면 axis1은 핸들이다), axis2는 body2에 상대적으로 특화된 것이다(body2가 바퀴라면 axis2는 바퀴 축이다). 

axis1은 관절 한계들과 한 개의 모터를 가질 수 있고, axis2는 한 개의 모터만을 가질 수 있다. 

axis1은 완충 장치 축 기능을 할 수 있다. 즉, 구속이 그 축을 따라 압력을 가할 수 있다. 

axis1이 axis2에 수직인 경첩-2 관절은 완충 장치가 추가된 십자축 관절과 동일하다. 

void dJointSetHinge2Anchor (dJointID, dReal x, dReal y, dReal z);  
void dJointSetHinge2Axis1 (dJointID, dReal x, dReal y, dReal z);  
void dJointSetHinge2Axis2 (dJointID, dReal x, dReal y, dReal z);

경첩-2 고정점과 고정축 매개 변수들을 설정한다. axis1과 axis2가 절대로 같은 선상에 있어선 안된다. 

void dJointGetHinge2Anchor (dJointID, dVector3 result);

월드 좌표계에서 body1의 관절 고정점을 얻는다. 관절이 완벽하게 만족되면, body2의 고정점과 똑같을 것이다.  
<a id="dJointGetHinge2Anchor"></a>  
void dJointGetHinge2Anchor2 (dJointID, dVector3 result);

월드 좌표계에서 body2의 관절 고정점을 얻는다. 관절이 완벽하게 만족되면, [dJointGetHinge2Anchor](#dJointGetHinge2Anchor)의 리턴값과 똑같을 것이다. 조건을 완벽하게 만족하지 않으면, 값이 약간 다를 것이다. 이것은 관절이 얼마나 떨어져 있는지를 알아내는 데 사용될 수 있다. 

void dJointGetHinge2Axis1 (dJointID, dVector3 result);  
void dJointGetHinge2Axis2 (dJointID, dVector3 result);

경첩-2 축 매개 변수를 얻는다.

dReal dJointGetHinge2Angle1 (dJointID);  
dReal dJointGetHinge2Angle1Rate (dJointID);  
dReal dJointGetHinge2Angle2Rate (dJointID);

경첩-2 axis1과 axis2에 대한 각도와 시간 변화율을 얻는다. 

고정점과 축이 설정될 때, 부착된 강체의 현재 위치가 검사되고, 그 위치는 0이 될 것이다.

#### 고정 관절 {#고정-관절}

고정 관절은 두 강체 또는 강체와 정적 환경 사이에 고정된 상대적인 위치와 회전을 유지한다. 이 관절을 사용하는 것은 디버깅을 제외하고 실제로 결코 좋은 생각이 아니다. 두 강체를 서로 달라붙게 할 바에는 하나의 강체로 표현하는 것이 더 낫다. 

void dJointSetFixed (dJointID);

강체 사이의 현재 상대적인 위치와 회전을 상기하기 위해 이미 부착된 고정 관절에 이 함수를 호출한다.

#### 접촉 관절 {#접촉-관절}

그림 9에 접촉 관절이 나와 있다. 

![ODE V0.5 사용자 가이드 그림 9](/ko/galexandria/ode-v05-users-guide/images/contact.jpg)

**그림 9:** 접촉 관절

접촉 관절은 body1과 body2가 접촉점에서 서로 파고드는 것을 방지한다. 이것은 강체가 접촉면의 법선 방향으로 "나가는" 속도만를 가지도록 허용함으로써 가능하다. 접촉 관절은 일반적으로 한 시간 스텝의 수명을 갖고, 충돌 감지에 응답하여 생성되고 소멸된다. 

접촉 관절은 법선에 수직인 두 마찰 방향에 특별한 힘을 적용함으로써 접촉면에 마찰을 시뮬레이션할 수 있다. 

접촉 관절을 생성할 때, dContact 구조체를 제공해야 한다. 이것의 정의는 다음과 같다: 

```cpp
struct dContact {
 dSurfaceParameters surface;
 dContactGeom geom;
 dVector3 fdir1;
};
```

geom은 충돌 함수에 의해 설정된다. 충돌 절에서 설명된다.

fdir1는 마찰력이 적용되는 방향을 나타내는 "첫번째 마찰 방향" 벡터이다. 이것은 단위 벡터여야 하며 접촉 법선에 수직이여야 한다(일반적으로 이것은 접촉면의 접선이다). surface.mode에 [dContactFDir1](#dContactFDir1)플랙이 설정되었을 때 정의되어야 한다. "두번째 마찰 방향"은 접촉 법선과 fdir1에 모두 수직인 벡터이다. 

surface는 사용자에 의해 설정되는 구조체이다. 충돌 표면의 속성들을 정의한다. 다음과 같은 멤버를 가진다: 

* int mode - 접촉 플랙. 항상 설정돼 있어야 한다. 다음 플랙들의 하나 이상의 조합으로 설정된다: 

{| style="text-align:left" border="1"  
|dContactMu2  
|설정되지 않으면, 두 마찰 방향에 대해 mu를 사용한다. 설정되면, 마찰 방향1에 대해 mu를 사용하고, 마찰 방향2에 대해 mu2를 사용한다.    
|-  
|<a id="dContactFDir1"></a>dContactFDir1  
|설정되면, fdir1를 마찰 방향1로 하고, 그렇지 않으면 자동으로 접촉 법선에 수직인 마찰 방향1를 계산한다 (이 경우 도출된 방향은 예측할 수 없다).    
|-  
|dContactBounce  
|설정되면, 접촉면은 탄력을 가지게 되어 강체가 서로 되튄다. 정확한 탄력도는 bounce 매개 변수에 의해 제어된다.    
|-  
|dContactSoftERP  
|설정되면, 접촉 법선의 오류 감쇠 매개 변수는 soft\_erp 매개 변수로 설정된다. 이는 표면을 부드럽게 만든다.    
|-  
|dContactSoftCFM  
|설정되면, 접촉 법선의 구속력 혼합 매개 변수는 soft\_cfm 매개 변수로 설정된다. 이는 표면을 부드럽게 만든다.    
|-  
|dContactMotion1  
|설정되면, 접촉면은 강체들의 움직임과는 독립적으로 움직이는 것처럼 가정한다. 이것은 표면위를 움직이는 일종의 컨베이어 벨트와 같다. motion1은 마찰 방향1에서 표면 속도를 정의한다.    
|-  
|dContactMotion2  
|마찰 방향2에 대한 것을 빼고, 위와 동일.    
|-  
|dContactSlip1  
|마찰 방향1에서의 힘-종속-미끄럼(FDS; force-dependent-slip).    
|-  
|dContactSlip2  
|마찰 방향2에서의 힘-종속-미끄럼(FDS; force-dependent-slip).    
|-  
|dContactApprox1\_1  
|마찰 방향1에 대한 마찰 피라미드 근사(friction pyramid approximation)를 사용한다. 설정되지 않으면 상수-힘-한계 근사(constant-force-limit approximation)가 사용된다 (mu가 힘 한계이다).    
|-  
|dContactApprox1\_2  
|마찰 방향2에 대한 마찰 피라미드 근사(friction pyramid approximation)를 사용한다. 설정되지 않으면 상수-힘-한계 근사(constant-force-limit approximation)가 사용된다 (mu가 힘 한계이다).    
|-  
|dContactApprox1  
|dContactApprox1\_1, dContactApprox1\_2와 동일하다.    
|}

* dReal mu : 쿨롱(Coulomb) 마찰 계수. 0에서 dInfinity까지의 범위안에 있어야 한다. 0은 마찰이 없는 접촉을, dInfinity는 절대로 미끄러지지 않는 접촉을 나타낸다. 마찰이 없는 접촉은 마찰이 있는 접촉보다 계산 시간이 덜 걸리고, 무한 마찰 접촉은 유한 마찰 접촉보다 계산 비용이 더 싸다. 값이 항상 설정되어 있어야 한다. 

* dReal mu2 : 마찰 방향2에 대한 선택적인 쿨롱 마찰 계수 (0..dInfinity). mode에 대응하는 플랙이 설정되어 있을때만 유효하다. 

* dReal bounce : 복원 매개 변수 (0..1). 0은 표면이 전혀 탄력적이지 않다는 것을 의미하고, 1은 최대 탄력도이다. mode에 대응하는 플랙이 설정되어 있을때만 유효하다. 

* dReal bounce\_vel : 되튐에 필요한 최소한의 접촉 속도 (단위 m/s). 접촉 속도가 이보다 이하이면 사실상 되튐 매개 변수가 0이된다. mode에 대응하는 플랙이 설정되어 있을때만 유효하다. 

* dReal soft\_erp : 접촉 법선의 "부드러움(softness)" 매개 변수. mode에 대응하는 플랙이 설정되어 있을때만 유효하다. 

* dReal soft\_cfm : 접촉 법선의 "부드러움(softness)" 매개 변수. mode에 대응하는 플랙이 설정되어 있을때만 유효하다. 

* dReal motion1,motion2 : 마찰 방향1, 2에서 표면 속도 (단위 m/s). mode에 대응하는 플랙이 설정되어 있을때만 유효하다. 

* dReal slip1,slip2 : 마찰 방향1, 2에 대한 힘-종속-미끄럼(FDS; force-dependent-slip) 계수. mode에 대응하는 플랙이 설정되어 있을때만 유효하다. 

FDS는 접촉면들이 표면의 접선 방향으로 적용되는 힘에 비례하는 속도로 서로를 지나가도록 하는 효과이다. 

마찰 계수 ''mu'' 가 무한대일 때 접촉점을 고려해보자. 통상적으로, 서로를 미끄러져 지나가도록 하기 위해, 힘 ''f''가 두 접촉면들에 적용되면, 접촉면들이 움직이지 않을 것이다. 그러나, FDS 계수가 양수 ''k''값으로 설정되면, ''k\*f'' 의 등속도에 도달하여 접촉면들이 서로를 지나갈 것이다. 

이는 보통의 마찰 효과와는 사뭇 다르다는 점을 주목하라: 힘은 접촉면 서로에 관하여 등가속(constant acceleration)을 일으키지 않는다 - 등속도에 도달하기위해 짧은 가속을 일으킨다. 

이것은 몇가지 상황을 모델링하는 데 유용하다, 특히 타이어가 그렇다. 예를 들어 길에 정차된 차를 보자. 차를 앞으로 밀면 움직이기 시작한다 (즉, 타이어가 구르기 시작한다). 수직 방향으로 차를 밀면 꿈쩍도 하지 않는다. 타이어가 그 방향으로는 구르지 않기 때문이다. 그러나 - 만약 차가 v속도로 이동중에, 수직 방향으로 힘 f를 적용하면 타이어가 f\*v에 비례하는 속도로 길위를 미끄러진다(그렇다, 실제로 일어난다). 

ODE에서 이것을 모델링하기 위해서는 타이어-길 접촉 매개 변수들을 다음과 같이 설정한다: 타이어가 구르는 방향에서 마찰 방향1을 설정하고, 마찰 방향2에서 FDS 미끄럼 계수를 k\*v로 설정한다. v는 타이어가 구르는 속도이고, k는 실험에서 얻을 수 있는 타이어 매개 변수이다. 

FDS는 쿨롱 마찰의 끈적임/미끄럼 효과와는 완전히 별개다 - 두 모드가 하나의 접촉점에 같이 사용될 수 있다. 

#### 각모터 관절 {#각모터-관절}

각모터 관절(angular motor)은 제어되는 두 강체의 상대적인 각속도를 허용한다. 각속도는 세개의 축으로 제어될 수 있다, 돌림힘 모터와 멈춤(torque motors and stops)이 축들에 대한 회전을 위해 설정된다 (아래의 "멈춤과 모터 매개 변수들(stops and motor parameters)"항을 보라). 이것은 주로 볼 관절과 결합할 때 유용하다 (볼 관절은 각자유도들을 전혀 구속하지 않는다), 하지만 각제어(angular control)가 필요한 어느 상황에서든 사용될 수 있다. 볼 관절로 각모터를 사용하기 위해서, 볼 관절이 부착된 두 강체에 각모터를 간단히 부착하기만 하면 된다.

각모터는 다른 모드들에서 사용될 수 있다. dAMotorUser 모드에서는, 각모터가 제어하는 축들을 사용자가 직접 설정한다. dAMotorEuler 모드에서는, 각모터가 상대적인 회전에 상응하는 오일러 각들(euler angles)을 계산한다, 돌림힘 모터와 멈춤이 오일러 각으로 설정되는 것을 허용한다. 오일러 각을 가진 각모터 관절은 그림 10에 나와있다. 

![ODE V0.5 사용자 가이드 그림 10](/ko/galexandria/ode-v05-users-guide/images/amotor.jpg)

**그림 10:** 오일러 각들을 가진 각모터 관절.

그림에서, a0, a1, a2는 각모터를 제어하는 세 축이다. 녹색 축은 body1에 고정된다. 파란색 축은 body2에 고정된다. body1 축들로부터 body2 축들을 얻기위해 아래 순서의 회전들을 수행한다: 

* a0축에 대해 theta<sub>0</sub>만큼 회전한다.   
* a1축에 대해 theta<sub>1</sub>만큼 회전한다 (a<sub>1</sub>는 원래 위치로부터 회전되었다).   
* a2축에 대해 theta<sub>2</sub>만큼 회전한다 (a<sub>2</sub>는 원래 위치로부터 두번 회전되었다). 

오일러 각들을 사용할 때 중요한 제약 조건이 있다: theta<sub>1</sub>각은 - ''pi'' /2 ... ''pi'' /2 범위 밖에 있어선 안된다. 만약 범위를 넘으면 각모터 관절이 불안정해질 것이다 (+/- ''pi'' /2에 특이점이 있다). 따라서 axis1에 적절한 멈춤을 설정해야 한다. 

void dJointSetAMotorMode (dJointID, int mode);  
int dJointGetAMotorMode (dJointID);

각모터 모드를 설정(리턴)한다. mode 매개 변수는 다음 상수들 중 하나여야 한다: 

{| style="text-align:left" border="1"  
|dAMotorUser  
|각모터 축과 관절 각 설정이 완전히 사용자에 의해 제어된다. 이것이 기본 모드이다.   
|-  
|dAMotorEuler  
|오일러 각들이 자동 계산된다. 축 a1 또한 자동 계산된다. 이 모드에서 각모터 축들은 올바르게 설정되야 한다. 초기에 이 모드가 설정되면, 강체의 현재 상대적인 회전들은 0에서 모든 오일러 각들과 일치한다.    
|}

void dJointSetAMotorNumAxes (dJointID, int num);  
int dJointGetAMotorNumAxes (dJointID);

각모터에 의해 제어되는 각 축들의 수를 설정/리턴한다. 인자 num은 0부터 3까지의 범위내에 속한다. 0은 관절을 비활성화한다. dAMotorEuler 모드에서는 자동적으로 3으로 설정된다. 

```cpp
void dJointSetAMotorAxis (dJointID, int anum, int rel,
             dReal x, dReal y, dReal z);
void dJointGetAMotorAxis (dJointID, int anum, dVector3 result);
int dJointGetAMotorAxisRel (dJointID, int anum);
```

각모터 축들을 설정/리턴한다. anum 인자는 변경할 축(0,1, 또는 2)을 선택한다. 각각의 축은 세 개의 "상대 회전(relative orientation)" 모드들 중 하나를 가질 수 있고, rel에 의해 선택된다:   
* 0: 축이 전역 틀에 고정된다.   
* 1: 축이 첫번째 강체에 고정된다.   
* 2: 축이 두번째 강체에 고정된다.

축 벡터 (x,y,z)는 rel값에 관계없이 항상 전역 좌표계에서 설정된다. 두개의 GetAMotorAxis 함수들이 있다, 하나는 축을 리턴하고 하나는 관련 모드를 리턴한다. 

dAMotorEuler 모드에 대해: 

* axis0과 axis2만 설정하면 된다. axis1은 자동으로 매 시간 스텝마다 계산된다.   
* axis0과 2만 반드시 서로 수직이어야 한다.   
* axis0은 첫번째 강체에, axis2는 두번째 강체에 고정되어야 한다.  
<a id="dJointSetAMotorAngle"></a>  
void dJointSetAMotorAngle (dJointID, int anum, dReal angle);

각모터에게 축 anum에 대한 현재 각도를 알려준다. 이 함수는 dAMotorUser 모드에서만 호출되어야 한다, 왜냐하면 이 모드에서 각모터는 관절 각을 알 수 있는 방법이 없기 때문이다. 각 정보는 축을 따라 멈춤이 설정되었을 때 필요하다. 그러나 각모터에는 필요하지 않다. 

dReal dJointGetAMotorAngle (dJointID, int anum);

축 anum에 대한 현재 각도를 리턴한다. dAMotorUser 모드에서 이것은 [dJointSetAMotorAngle](#dJointSetAMotorAngle)로 설정한 값이다. dAMotorEuler 모드에서 이것은 오일러 각과 일치한다. 

dReal dJointGetAMotorAngleRate (dJointID, int anum);

축 anum에 대한 현재 각 변화율을 리턴한다. dAMotorUser 모드에서 정보가 충분치 않을 때 이것은 항상 0이다. dAMotorEuler 모드에서는 오일러 각 변화율과 일치한다. 

### 일반 {#일반}

관절 기하구조 매개 변수를 설정하는 함수들은 관절이 강체에 부착된 후에 호출되어야 한다. 이때 강체가 올바른 위치에 있어야 한다. 그렇지 않으면 관절이 올바르게 초기화되지 않을 것이다. 관절이 미리 부착되어 있지 않으면, 이 함수들은 아무 일도 하지 않는다.

매개 변수를 얻는 함수에 대해, 시스템이 조정되어 있지 않으면 (즉, 몇가지 관절 오류가 있다면), 고정점/축이 body1([dJointAttach](#dJointAttach)함수에서 body1을 0으로 지정한 경우에는 body2)에 대해서만 적절한 값을 가질 것이다. 

모든 관절에 대한 고정점은 기본값이 (0,0,0)이다. 모든 관절에 대한 기본 축은 (1,0,0)이다. 

축이 설정될 때, 단위 길이로 정규화된다. 축을 얻는 함수들은 이처럼 조정된 단위 길이의 축을 리턴한다. 

관절의 각 또는 위치를 측정할 때, 0값은 서로에 대한 강체들의 초기 위치와 일치한다. 

관절의 각 또는 위치(또는 변화율)를 직접 설정하는 함수는 없다, 그대신 대응하는 강체의 위치와 속도를 설정해야 한다. 

### 멈춤과 모터 매개 변수들 {#멈춤과-모터-매개-변수들}

관절이 처음 생성될 때, 관절이 아무 제약없이 멋대로 움직이는 것을 막을 수가 없다. 예를 들면, 경첩 관절은 360도로 움직일 수 있고, 미닫이 관절은 어떤 길이로든 움직일 수 있다.

관절에 멈춤을 설정하여 이러한 운동 범위를 제한할 수 있다. 이는 관절 각(또는 위치)이 하한 멈춤 값 이하로 내려가거나 상한 멈춤 값 이상으로 올라가지 않게 해준다. 0의 관절 각(또는 위치)은 강체의 초기 위치와 상응한다. 

많은 관절 타입들이 멈춤 뿐만아니라 모터를 가질 수 있다. 모터는 관절의 회전축(또는 미닫이 관절)이 원하는 속도에 도달하도록 관절의 자유도에 돌림힘(또는 힘)을 적용한다. 모터에는 힘 한계가 있다, 힘 한계는 주어진 최대 힘/돌림힘 이상을 관절에 적용할 수 없다는 것을 의미한다. 

모터는 2개의 매개 변수를 가진다: 원하는 속도, 그리고 그런 속도에 도달하기위해 쓸 수 있는 최대 힘이다. 이것은 실세계의 모터, 엔진 또는 서보모터(servo)를 아주 단순화한 모델이다. 그러나, 이것은 관절에 연결되기 전에 변속기(gearbox)로 변속이 되는 모터를 모델링하는 데 상당히 유용하다. 그러한 장치들은 흔히 원하는 속도를 설정하여 제어되고, 그러한 속도(관절에 가할 수 있는 특정한 힘의 양에 대응하는)를 얻기 위해 최대 힘을 발생할 수 있다. 

모터는 관절에 마른(또는 쿨롱) 마찰을 정확히 모델링하는 데 사용되기도 한다. 단순히 원하는 속도를 0으로 설정하고 최대 힘을 어떤 상수값으로 설정한다 - 그러면 모든 관절 운동이 그 힘에 의해 방해를 받을 것이다. 

관절 멈춤과 모터를 사용하는 것에 대한 대안은 힘을 강체 자체에 적용하는 것이다. 모터 힘을 적용하는 것은 쉽다, 그리고 관절 멈춤은 억제된 용수철 힘으로 에뮬레이트될 수 있다. 그러나 힘을 직접적으로 적용하는 것은 종종 좋은 접근법이 아니며 조심스럽게 다루지 않으면 가혹한 안정성 문제를 일으킬 수 있다. 

원하는 속도를 얻기 위해 강체에 힘을 적용하는 경우를 생각해 보자. 이 힘을 계산하기 위해서 현재 속도에 대한 정보를 사용한다: 

''force = k \* (desired speed - current speed)''

이것은 몇가지 문제를 갖고 있다. 첫째, 매개 변수 k는 반드시 수동으로 조율되어야 한다. 이 값이 너무 낮으면 강체는 원하는 속도에 도달하는 데 시간이 오래 걸릴 것이다. 너무 높으면 시뮬레이션이 불안정해질 것이다. 둘째, ''k''를 잘 선택했더라도 강체가 속도를 내는데 약간의 시간 스텝이 걸릴 것이다. 셋째, 다른 "외부의" 힘이 강체에 적용되면, 원하는 속도에 결코 도달할 수 없게 된다 (좀더 복잡한 힘 방정식이 필요하겠으나 추가 매개 변수와 또다른 문제점을 갖게 될 것이다). 

관절 모터는 이러한 모든 문제를 해결한다: 강체가 한번의 시간 스텝에서 속도를 낼 수 있고, 허용된 양 이상의 힘을 받지 않는다. 관절 모터는 사실 구속 조건으로서 수행되기 때문에 추가 매개 변수가 필요없다. 그리고 적절한 힘을 얻기 위해 시간 스텝를 미리 내다볼 수 있다. 이는 계산 비용이 더 들지만 더욱 견고하고 안정적이며 설계하는 데 시간이 덜 든다. 방대한 강체 시스템에서 특히 그렇다. 

비슷한 인자들이 관절 멈춤에 적용된다. 

#### 매개 변수 함수 {#매개-변수-함수}

다음은 관절에서 멈춤과 모터 매개 변수들을 설정하는 함수들이다: 

```cpp
void dJointSetHingeParam (dJointID, int parameter, dReal value);
void dJointSetSliderParam (dJointID, int parameter, dReal value);
void dJointSetHinge2Param (dJointID, int parameter, dReal value);
void dJointSetUniversalParam (dJointID, int parameter, dReal value);
void dJointSetAMotorParam (dJointID, int parameter, dReal value);
dReal dJointGetHingeParam (dJointID, int parameter);
dReal dJointGetSliderParam (dJointID, int parameter);
dReal dJointGetHinge2Param (dJointID, int parameter);
dReal dJointGetUniversalParam (dJointID, int parameter);
dReal dJointGetAMotorParam (dJointID, int parameter);
```

각 관절 타입에 대해 한계/모터 매개 변수들을 설정/리턴한다: 

{| style="text-align:left" border="1"  
|dParamLoStop  
|하한 멈춤 각 또는 위치. 이것을 -dInfinity(기본값)로 설정하면 하한 멈춤을 끈다. 회전가능한 관절에 대해, 효과를 보기위해서는 멈춤 각이 반드시 - pi이상이어야 한다.    
|-  
|dParamHiStop  
|상한 멈춤 각 또는 위치. 이것을 dInfinity(기본값)로 설정하면 상한 멈춤을 끈다. 회전 가능한 관절에 대해, 효과를 보기 위해서는 멈춤 각이 반드시 pi이하이어야 한다. 상한 멈춤이 하한 멈춤보다 작다면, 둘다 효력을 잃게 될 것이다.    
|-  
|dParamVel  
|원하는 속도 (각 또는 선 속도).    
|-  
|dParamFMax  
|모터가 원하는 속도를 얻기 위해 사용하는 최대 힘 또는 돌림힘. 반드시 0보다 같거나 커야한다. 0(기본값)으로 설정하면 모터를 끈다.    
|-  
|dParamFudgeFactor  
|현재 관절 멈춤/모터 구현은 작은 문제가 하나 있다: 관절이 하나의 멈춤에 있고 모터가 멈춤으로부터 관절을 움직이도록 설정되어 있을 때, 한번에 너무 많은 힘이 적용되면 "뛰는(jumping)" 운동을 하게 된다. 이러한 과다한 힘을 조절하기위해 조작 계수(fudge factor)를 사용한다. 이 계수는 0과 1(기본값) 사이의 값을 가진다. 관절에서 뛰는 운동이 너무 눈에 띄면, 값을 줄일 수 있다. 이 값이 너무 작으면 모터가 관절을 멈춤 상태에서 벗어나 움직이도록 할 수가 없다.    
|-  
|dParamBounce  
|멈춤의 탄력도. 0..1 범위의 복원 매개 변수이다. 0은 멈춤이 전혀 탄력적이지 않음을 의미하고, 1은 최고의 탄력도를 의미한다.    
|-  
|dParamCFM  
|멈춤이 아닐 때 구속 힘 혼합 (CFM; constraint force mixing) 값이 사용된다.    
|-  
|dParamStopERP  
|멈춤에 의해 사용되는 오류 감쇠 매개 변수(ERP; error reduction parameter).    
|-  
|dParamStopCFM  
|멈춤에 의해 사용되는 구속 힘 혼합(CFM) 값. ERP 값과 함께 스펀지나 부드러운 멈춤을 위해 사용된다. 이것은 움직이지 않는 관절을 위한 것이므로, 힘을 받은 관절이 한계에 다다를 때 예상대로 작동하지 않는다.    
|-  
|dParamSuspensionERP  
|완충 장치의 오류 감쇠 매개 변수 (ERP). 현재 경첩-2 관절에서만 구현되었다.    
|-  
|dParamSuspensionCFM  
|완충 장치의 구속 힘 혼합(CFM) 값. 현재 경첩-2 관절에서만 구현되었다.    
|}

주어진 관절에 어떤 매개 변수가 구현되지 않았다면, 그 값을 설정하는 것은 아무 효과가 없다. 

이 매개 변수 이름들은 다음에 숫자(2또는 3)이 올 수 있다. 즉, 경첩-2 관절에서 두번째 축, 또는 각모터 관절에서 세번째 축을 예로 들 수 있다. 상수 dParamGroup은 다음과 같이 정의 될 수 있다: dParamXi = dParamX + dParamGroup \* (i-1) 

### 관절에 힘/돌림힘을 직접 설정 {#관절에-힘/돌림힘을-직접-설정}

모터는 관절에 속도를 직접적으로 설정하는 것을 허용한다. 그러나, 대신 관절에 돌림힘이나 힘을 설정하고 싶을 때도 있다. 다음 함수들이 그런 일을 한다. 모터에 영향을 주지 않지만 모터에 부착된 강체에 [dBodyAddForce](#dBodyAddForce)/[dBodyAddTorque](#dBodyAddTorque)를 호출한다. 

dJointAddHingeTorque(dJointID joint, dReal torque)

경첩 축에 대해 torque 크기 만큼의 돌림힘을 적용한다. 즉, 경첩 축의 방향으로 body1에 돌림힘을 적용하고 반대 방향으로 body2에 같은 크기의 힘을 적용한다. 이 함수는 단순히 [dBodyAddTorque](#dBodyAddTorque)의 래퍼 함수이다. 

dJointAddUniversalTorques(dJointID joint, dReal torque1, dReal torque2)

십자축 관절의 axis1에 대해 torque1을 적용하고, axis2에 대해 torque2를 적용한다. 이 함수는 단지 [dBodyAddTorque](#dBodyAddTorque)의 래퍼함수이다. 

dJointAddSliderForce(dJointID joint, dReal force)

미닫이 관절의 방향으로 force 크기 만큼의 힘을 적용한다. 즉, 미닫이 관절의 축방향으로 body1에 force의 크기를 가진 힘을 적용한다. 그리고 반대 방향으로 body2에 같은 크기의 힘을 적용한다. 이 함수는 단지 [dBodyAddForce](#dBodyAddForce)의 래퍼함수이다. 

dJointAddHinge2Torques(dJointID joint, dReal torque1, dReal torque2)

경첩2 관절의 axis1에 대해 torque1을 적용하고, axis2에 대해 torque2를 적용한다. 이 함수는 단지 [dBodyAddTorque](#dBodyAddTorque)의 래퍼함수이다. 

dJointAddAMotorTorques(dJointID joint, dReal torque0, dReal torque1,  
                      dReal torque2)

각모더의 축0에 대해 torque0을, axis1에 대해 torque1을, axis2에 대해 torque2를 적용한다. 만약 모터가 세 개 이하의 축을 가지면, 나머지 돌림힘은 무시된다. 이 함수는 단지 [dBodyAddTorque](#dBodyAddTorque)의 래퍼함수이다.

## StepFast {#StepFast}

**주의: StepFast 알고리즘은 QuickStep 알고리즘으로 대체되었다: [dWorldQuickStep](#dWorldQuickStep)함수를 보라. 그러나 다음의 논문에서 사용되는 방법의 세부 내용을 제외한 많은 부분은 QuickStep에도 적용된다.** 

ODE's [dWorldStep](#dWorldStep) ODE의 dWorldStep함수는 현재 시스템을 한단계 진행하는 데 "큰 행렬 (big matrix)" 방법을 사용하고 있다. 이 방법은 몇몇 큰 시스템에 대해서 느려질 수 있고 많은 메모리를 요구한다. StepFast1 알고리즘은 다른 대안을 제시한다. 즉, 정확도를 희생시키고 속도와 메모리에서 큰 이득을 보는 것이다. 이것을 이용하기 위해서 간단히 [dWorldStep](#dWorldStep)대신 [dWorldStepFast1](#dWorldStepFast1)를 호출하면된다. 

그림 11의 그래프는 표준적인 [dWorldStep](#dWorldStep) 알고리즘에 대한 속도 이득을 예시하고 있다. 

![ODE V0.5 사용자 가이드 그림 11](/ko/galexandria/ode-v05-users-guide/images/sf-graph1.jpg)

**그림 11:** StepFast의 속도 이득.

그래프는 수행 시간에 따라 시스템으로부터 제거되는 자유도 (DOF; degree of freedom)의 수를 보여준다. [dWorldStep](#dWorldStep) 알고리즘의 수행 시간이 제거된 자유도(DOFs removed)의 수의 세제곱에 비례한다는 것을 알 수 있다. 그러나 StepFast1 알고리즘은 대략 선형적이다. 섬이 늘어날 수록 (예로, 커다란 자동차 더미가 있을 때, "봉제 인형 시체들 (ragdoll corpses)", 또는 벽돌 무더기), StepFast1 알고리즘은 [dWorldStep](#dWorldStep) 보다 낫다. 이는 최악의 상황에서도 일정한 프레임율을 더 잘 유지할거라는 의미이다. 

메모리에 대한 제거된 자유도에 관한 그래프가 상당히 비슷해 보인다 (그림 12를 보라). 

![ODE V0.5 사용자 가이드 그림 12](/ko/galexandria/ode-v05-users-guide/images/sf-graph2.jpg)

**그림 12:** StepFast의 메모리 이득.

[dWorldStep](#dWorldStep)는 제거된 자유도의 수의 제곱에 비례한 만큼의 메모리를 요구한다. 그러나, StepFast1는 여전히 선형이다. 이것은 한 스텝 당 반복 횟수와는 관련이 없다. 그래서 StepFast1와 함께라면 "커다란 더미가 있었는데 ODE가 오류 메시지없이 충돌이 나버렸다는" 무시무시한 문제(주로 스택 오버플로우)는 일어나지 않을 거라는 것을 의미한다. 

### 언제 StepFast1를 사용할 것인가 {#언제-StepFast1를-사용할-것인가}

위에서 보듯이, StepFast1는 속도와 메모리 사용면에서 꽤 훌륭하다. 그러나, 이런 능력은 공짜로 오는 것이 아니다. 이미 언급했듯이 StepFast1는 속도와 메모리 이득에 대해 정확도라는 대가를 지불한다. 실제로 스텝 당 반복 회수를 조정함으로써 속도의 비용에서 얼마나 많은 정확도를 양보할지를 선택해야 한다. 결코 [dWorldStep](#dWorldStep)의 정확도에 도달하지는 못하겠지만, 반복 횟수를 늘릴수록 더 정확한 (더 느린) 결과가 나올 것이 거의 확실하다. 그러므로 StepFast1는 많은 다양한 상황에서 사용될 수 있다.

그럼 이 문제에 대한 일반적인 답은: 속도나 메모리 이득을 보길 원하고, 시스템을 안정적으로 돌아가게 하기 위해 조금 더 많은 매개 변수들을 가져도 상관없다면, StepFast1를 사용하라. 수많은 강체가 접촉하는 상황에서 [dWorldStep](#dWorldStep)가 너무 느리다면, StepFast1으로 바꿔보라. 많은 시스템들이 [dWorldStep](#dWorldStep)함수를 [dWorldStepFast1](#dWorldStepFast1)로 바꾸기만 해도 잘 작동할 것이다. 나머지 시스템들의 경우 StepFast1와 잘 작동하도록하기 위해서 보통 강체의 질량에 약간의 조정이 필요할 것이다. 관절이 두 강체들을 큰 질량 비율(즉, 하나의 강체가 다른 강체의 질량의 몇 배를 가진다)로 연결할 때, StepFast1가 그것을 처리하는 데 어려움이 있을지 모른다. 

StepFast1의 또다른 가능성은 밑바닥으로부터 그것을 설계하는 것이다. 많은 물리적 기반의 오브젝트들로 거대한 세계를 만들고자한다면, 일단 앞장서서 StepFast1를 사용할 계획을 세워야 한다. 위의 질량율 문제때문에, 시스템 안의 모든 강체의 질량을 1.0으로 하거나 0.5와 1.5처럼 아주 작은 범위로 한정하고 싶을 것이다. 오브젝트가 더이상 가능한 많은 관절들을 삭제할 필요가 없다는 점을 제외하고, 속도와 안정성을 위해 대부분의 다른 제안 사항들이 StepFast1에 적용된다. 고정 관절에 의해 연결된 몇 개의 강체들에 질량을 퍼뜨림으로써 하나의 거대한 강체로 구현하는 것보다 더 나은 성능을 얻을 수 있을 것처럼 보인다. 그렇다면 하나의 거대한 강체는 안정성을 유지하기위해 [dWorldStep](#dWorldStep)으로 되돌려야한다는 것을 의미한다. 

StepFast1에 대한 마지막 가능성은 필요할 때만 사용하는 것이다. StepFast1는 강체와 월드 구조체들을 [dWorldStep](#dWorldStep)과 똑같은 식으로 사용하기 때문에, 두 해결책 사이를 사실상 자유롭게 왔다갔다 할 수 있다. 어느 시점을 위한 스위치를 만드는 것이 좋을지에 대한 방법은 단순히 충돌 검사시 접촉 관절의 수를 세어보는 것이다. 대부분 충돌 검사는 시뮬레이션 단계 전에 이루어지므로, 이 방법을 사용하는 것은 속도를 느리게하는 큰 섬을 절대 [dWorldStep](#dWorldStep)로 보내지 않도록 한다. 더 나은 유일한 해결책은 변종 섬 생성 함수이다. 이것은 작은 섬들을 [dWorldStep](#dWorldStep)에 보내고, 큰 섬들을 [dWorldStepFast1](#dWorldStepFast1)에 보낸다. 이 방법이 미래의 어느 시점에 원천적으로 성공할 것이다. 

### 언제 StepFast1를 사용하면 안되는가 {#언제-StepFast1를-사용하면-안되는가}

비록 StepFast1를 사용하지 않는 것이 바람직한 몇가지 특정한 경우가 있음에도, 그러한 경우들은 다음 한 문장으로 요약될 수 있을거라 믿는다: 정확성이 속도와 메모리보다 더 중요할 때, StepFast1를 사용하지 말라. 이런 경우에도 여전히 StepFast1를 재고하고, 부정확성이 두드러져 보이는 지를 확인하고, 상대적으로 반복 횟수(20+)를 늘려서라도 StepFast1를 사용하고 싶을 것이다. 

### 어떻게 작동하는가 {#어떻게-작동하는가}

저 너머 흥미로운 부분들에 대해, 여기 StepFast1알고리즘이 어떻게 작동하는지에 대한 간략한 설명을 하겠다. ODE가 해결하고자 하는 일반적인 문제는 (''m'' = 구속조건들) 미지수들로 이루어진 1차원의 (부)등식들의 시스템이다. 여기서 하나의 구속 조건은 한 개의 관절에 대한 1 자유도를 구속한다. 많은 관절을 가진 강체의 큰 섬들에 대해, 이것은 다소 큰 ''O(m2)''배열을 소모하고, ''O(m3)''시간이 걸린다. StepFast1는 한가지 가정을 통해 큰 행렬을 생성하지 않는다: 상대적으로 작은 시간 스텝들에서, 어떠한 관절의 효과도 국지적이기 때문에 시스템 내에서 다른 관절을 고려하지 않고 계산할 수 있고, 충돌하는 관절들은 강체가 실제로 움직이기 전에 서로를 무효로 한다. 그래서 StepFast1는 한 개의 관절(m <= 6)에 국지화된 같은 문제를 해결하기 위해 같은 방법(LCP)를 사용한다. StepFast1는 시간 스텝을 세분화하고 아주 짧은 시간 스텝들을 통해 시뮬레이션 과정을 반복함으로써 이것을 해낸다 (i = 최대 반복 횟수). 그래서 StepFast1의 수행 시간은 "대략" ''O(m)''이다. 사실은 ''O(j i (m/j)3) = O(m i (m/j)2)'' 에 더 가깝다, 여기서 ''j'' = 관절들, ''(m/j)'' 는 절대 6보다 크지 않고, ''(m/j)<sup>2</sup>'' 는 상수라서 제외된다. 

### StepFast1에 포함되는 실험적인 유용한 함수들 {#StepFast1에-포함되는-실험적인-유용한-함수들}

몇가지 실험적인 함수들이 StepFast1 코드 흐름의 일부분으로서 ODE에 포함되어왔다. 대부분 강체를 자동으로 (비)활성화하는 것에 관련이 있다. 일반적인 아이디어는 이렇다: 

* 강체가 특정 선속도와 각속도 이하로 떨어질 때 비활성화 후보로 낙점된다. 이를 AutoDisableThreshold라 한다. 빠른 실행을 위해서, 측정된 실제 속도는 강체의 속도의 제곱이다. 그래서 예상보다 더 낮은 값을 설정할 필요가 있다. 0.003이 기본값이고, test\_crash에서 잘 작동한다.   
* 강체가 특정 수의 step들(AutoDisableSteps)동안 남아있는 비활성화 후보를 가지고 있을 때, 비활성화된다. 이것은 상자의 경우에 완벽히 들어맞는다. 상자는 두 점들 위에서 지면에 닿았다가 튀어오르고, 뒤로 젖히기전에 서너번의 단계동안 움직임없이 앞뒤로 움직이는 성향이 있다. 둥근 오브젝트는 일반적으로 상자(10+)보다 훨씬 낮은 1정도의 AutoDisableSteps를 필요로 한다. 기본값은 10이다.   
* AutoDisabling는 기본적으로 비활성화되어 있다. 활성화하려면 dBodySetAutoDisableSF1(body, true)를 사용하라.   
* 강체는 다른 활성화된 강체와 접촉하게 되면 자동으로 다시 활성화된다.   
* 활성화된 강체는 매 단계마다 (AutoEnableDepth) 안의 강체들만을 활성화한다. 이것은 AutoDisabling과 함께, AutoDisable 매개 변수들에 의해 허용되는 가장 작은 영역으로 활성화된 강체들을 수용한다. AutoEnableDepth를 아주 큰 수로 설정하는 것은 현재의 기능을 계속 유지할 것이다. 0으로 설정하는 것은 새로운 기능을 제공한다: 비활성화된 강체들이 결코 자동으로 재활성화되지 않고, 기하 구조처럼 행동한다. 3이 test\_crash에서 가장 좋은 값인 듯하다. 그러나 기본적인 기능을 유지하기 위해 기본값은 1000이다. 

자동 비활성화에 관계된 함수들은 아직 구현되지 않았다는 점에 주의하라! 

### API {#API}

<a id="dWorldStepFast1"></a>  
void dWorldStepFast1(dWorldID, dReal stepsize, int maxiterations);

StepFast1 알고리즘을 사용하여 stepsize 초만큼 시뮬레이션을 진행한다. maxiterations는 수행 횟수이다.

void dWorldSetAutoEnableDepthSF1(dWorldID, int autoEnableDepth);  
int dWorldGetAutoEnableDepthSF1(dWorldID);

StepFast1 알고리즘에 의해 사용되는 AutoEnableDepth 매개 변수를 설정/리턴한다.

void dBodySetAutoDisableThresholdSF1(dBodyID, dReal autoDisableThreshold);  
dReal dBodyGetAutoDisableThresholdSF1(dBodyID);

StepFast1 알고리즘에 의해 사용되는 주어진 강체에 대한 AutoDisableThreshold 매개 변수를 설정/리턴한다.

void dBodySetAutoDisableStepsSF1(dBodyID, int AutoDisableSteps);  
int dBodyGetAutoDisableStepsSF1(dBodyID);

StepFast1 알고리즘에 의해 사용되는 주어진 강체에 대한 AutoDisableSteps 매개 변수를 설정/리턴한다. 

void dBodySetAutoDisableSF1(dBodyID, int doAutoDisable);  
int dBodyGetAutoDisableSF1(dBodyID);

StepFast1알고리즘에 의해 사용되는 주어진 강체에 대한 AutoDisable 플랙을 설정/리턴한다. doAutoDisable이 0이 아니면, auto-disabling이 활성화된다. doAutoDisable이 0이면, auto-disabling이 비활성화된다. 

## 지원 함수 {#지원-함수}

### 회전 함수 {#회전-함수}

강체 회전은 사원수로 표현된다. 사원수는 네 개의 수들이다. \[cos( theta /2),sin( theta /2)\*u\] 여기서, theta는 회전각이고, u는 단위 길이의 회전축이다. 

모든 강체는 사원수로부터 유도된 3x3 회전 행렬도 가지고 있다. 회전 행렬과 사원수는 항상 일치한다. 

사원수에 대한 몇가지 정보:   
* q와 -q는 같은 회전을 의미한다.   
* 임의의 사원수의 역은 \[ q\[0\] -q\[1\] -q\[2\] -q\[3\] \]이다. 

다음은 회전 행렬과 사원수를 처리하는 함수들이다. 

void dRSetIdentity (dMatrix3 R);

R을 단위 행렬로 설정한다(즉 회전이 없다). 

```cpp
void dRFromAxisAndAngle (dMatrix3 R,
                        dReal ax, dReal ay, dReal az, dReal angle);
```

축(ax,ay,az)에 대한 angle 라디안 회전으로서 회전 행렬 R을 계산한다. 

```cpp
void dRFromEulerAngles (dMatrix3 R,
                       dReal phi, dReal theta, dReal psi);
```

세 개의 오일러 회전 각들로부터 회전 행렬 R을 계산한다. 

```cpp
void dRFrom2Axes (dMatrix3 R, dReal ax, dReal ay, dReal az,
                 dReal bx, dReal by, dReal bz);
```

두 벡터 'a' (ax,ay,az)와 'b' (bx,by,bz)로부터 회전 행렬 R을 계산한다. 'a'와 'b'는 각각 회전 좌표계의 x와 y축이다. 필요하면, 'a'와 'b'는 단위 길이로 변환되고, 'b'는 'a'에 평행하도록 투영될 것이다. z축은 'a'와 'b'의 외적이다. 

void dQSetIdentity (dQuaternion q);

q를 단위 사원수로 설정한다 (즉 회전이 없다).

```cpp
void dQFromAxisAndAngle (dQuaternion q, dReal ax, dReal ay, dReal az,
                        dReal angle);
```

축 (ax,ay,az)에 대한 angle 라디안 만큼의 회전을 사원수 q로 계산한다. 

```cpp
void dQMultiply0 (dQuaternion qa,
                 const dQuaternion qb, const dQuaternion qc);
void dQMultiply1 (dQuaternion qa,
                 const dQuaternion qb, const dQuaternion qc);
void dQMultiply2 (dQuaternion qa,
                 const dQuaternion qb, const dQuaternion qc);
void dQMultiply3 (dQuaternion qa,
                 const dQuaternion qb, const dQuaternion qc);
```

qa에 qb\*qc를 설정한다. 이것은 qc회전을 적용한 후 qb회전을 적용한 것과 같다. 0/1/2 버전은 곱하기 함수와 유사하다. 1는 qb의 역을 사용하고, 2는 qc의 역을 사용한다. 3는 둘의 역을 모두 사용한다. 

void dQtoR (const dQuaternion q, dMatrix3 R);

사원수 q를 회전 행렬 R로 변환한다.

void dRtoQ (const dMatrix3 R, dQuaternion q);

회전 행렬 R를 사원수 q로 변환한다.

void dWtoDQ (const dVector3 w, const dQuaternion q, dVector4 dq);

사원수 q와 각속도 벡터 w가 주어졌을 때, dq/dt로 끝나는 dq를 리턴한다.

### 질량 함수 {#TOC--22}

강체에 대한 질량 매개 변수들은 dMass 구조체에 의해 정의된다:  

```cpp
typedef struct dMass {
 dReal mass;   // total mass of the rigid body
 dVector4 c;   // center of gravity position in body frame (x,y,z)
 dMatrix3 I;   // 3x3 inertia tensor in body frame, about POR
} dMass;
```

다음은 이 구조체에 관련한 함수들이다: 

void dMassSetZero (dMass \*);

질량 매개 변수를 0으로 설정한다.

```cpp
void dMassSetParameters (dMass *, dReal themass,
                        dReal cgx, dReal cgy, dReal cgz,
                        dReal I11, dReal I22, dReal I33,
                        dReal I12, dReal I13, dReal I23);
```

질량 매개 변수들을 주어진 값들로 설정한다. themass는 강체의 질량이다. (cx,cy,cz)는 강체 틀의 무게 중심이다. lxx는 관성 행렬의 요소들이다:   

```cpp
   [ I11 I12 I13 ]
   [ I12 I22 I23 ]
   [ I13 I23 I33 ]
```

void dMassSetSphere (dMass \*, dReal density, dReal radius);  
void dMassSetSphereTotal (dMass \*, dReal total\_mass, dReal radius);

주어진 반지름과 밀도(density)를 가진 구를 표현하는 질량 매개 변수들을 설정한다. 질량 중심은 강체에 상대적인 (0,0,0)이다. 첫번째 함수는 구의 밀도를, 두번째 함수는 구의 총 질량을 받아들인다.

```cpp
void dMassSetCappedCylinder (dMass *, dReal density, int direction,
                            dReal radius, dReal length);
void dMassSetCappedCylinderTotal (dMass *, dReal total_mass,
                            int direction, dReal radius, dReal length);
```

주어진 매개 변수와 밀도(density)를 가진 닫힌 원통을 표현하는 질량 매개 변수들을 설정한다. 질량 중심은 강체에 상대적인 (0,0,0)이다. 원통과 구로된 뚜껑의 반지름은 radius이다. 뚜껑을 제외한 원통의 길이는 length이다. 원통의 긴 축은 강체의 x, y 또는 z축을 따라 방향 값(1=x, 2=y, 3=z)에 준해 정해진다. 첫번째 함수는 오브젝트의 밀도를 받아들이고, 두번째는 오브젝트의 총 질량을 받아들인다. 

```cpp
void dMassSetCylinder (dMass *, dReal density, int direction,
              dReal radius, dReal length);
void dMassSetCylinderTotal (dMass *, dReal total_mass, int direction,
              dReal radius, dReal length);
```

주어진 매개 변수와 밀도(density)를 가진 평평하게 끝나는 원통을 표현하는 질량 매개 변수들을 설정한다. 질량 중심은 강체에 상대적인 (0,0,0)이다. 원통의 반지름은 radius이다. 원통의 길이는 length이다. 원통의 긴 축은 강체의 x, y 또는 z축을 따라 방향 값(1=x, 2=y, 3=z)에 준해 정해진다. 첫번째 함수는 오브젝트의 밀도를 받아들이고, 두번째는 오브젝트의 총 질량을 받아들인다. 

```cpp
void dMassSetBox (dMass *, dReal density,
                 dReal lx, dReal ly, dReal lz);
void dMassSetBoxTotal (dMass *, dReal total_mass,
                 dReal lx, dReal ly, dReal lz);
```

주어진 차원과 밀도(density)를 가진 상자를 표현하는 질량 매개 변수들을 설정한다. 질량 중심은 강체에 상대적인 (0,0,0)이다. 상자의 너비 길이는 x, y, z축을 따라서 lx, ly, lz이다. 첫번째 함수는 오브젝트의 밀도를 받아들이고, 두번째는 오브젝트의 총 질량을 받아들인다.

void dMassAdjust (dMass \*, dReal newmass);

어떤 오브젝트에 대해 총 질량이 newmass가 되도록 질량 매개 변수들을 조정한다. 어떤 오브젝트에 대한 질량 매개 변수들을 설정하기 위해 위의 함수들을 사용할 때 이 함수가 유용하다. 

void dMassTranslate (dMass \*, dReal x, dReal y, dReal z);

어떤 오브젝트의 주어진 질량 매개 변수에 대해 강체틀에 상대적인 (x,y,z)로 강체가 위치되도록 질량 매개 변수들을 조정한다. 

void dMassRotate (dMass \*, const dMatrix3 R);

어떤 오브젝트에 대해 강체 틀에 상대적인 회전이 R이 되도록 질량 매개 변수들을 조정한다. 

void dMassAdd (dMass \*a, const dMass \*b);

질량a에 질량b를 더한다.

### 수학 함수 {#수학-함수}

\[많은 수학 함수들이 있지만, 아직 문서화할 만큼 충분히 표준화되지 않았다\].   

### 오류와 메모리 함수 {#오류와-메모리-함수}

\[추후에 문서화할 예정이다\].

## 충돌 검사 {#충돌-검사}

ODE는 두가지 주요 구성 요소를 가지고 있다: 동역학 시뮬레이션 엔진과 충돌 검사 엔진이다. 충돌 엔진은 강체의 모양(shape)에 대한 정보를 제공받는다. 매 시간 스텝마다 어떤 강체들이 서로 부딪히는 지를 알아내고, 그러한 접촉 정보를 사용자에게 건네준다. 사용자는 순서대로 강체 사이에 접촉 관절을 생성한다. 

ODE의 충돌 검사를 사용하는 것은 선택적이다 - 올바른 접촉 정보를 제공하기만 하면 다른 충돌 검사 시스템을 사용할 수 있다. 

### 접촉점 {#접촉점}

두 강체가 접촉하거나 하나의 강체가 환경 안의 정적인 오브젝트를 건드리면, 하나또는 그 이상의 "접촉점"으로 접촉을 표현한다. 각 접촉점에 대응하는 dContactGeom 구조체가 있다: 

```cpp
struct dContactGeom {
 dVector3 pos;       // 접촉위치
 dVector3 normal;    // 법선 벡터
 dReal depth;        // 관통깊이
 dGeomID g1,g2;      // 충돌하는 물체들
};
```

pos는 전역 좌표계로 접촉 위치를 기록한다. 

depth는 두 강체가 서로를 관통한 깊이이다. 깊이가 0이면 두 강체가 아주 살짝 접촉을 한 것이다. 즉 "방금 막" 부딪힌 것이다. 그러나, 이런 경우는 드물다 - 시뮬레이션이 완전히 정확하지 않고 종종 충돌직후 강체가 너무 멀리 떨어져서 깊이가 0이 되지 않을 때도 있다. 

normal는 일반적으로 말하자면, 접촉면에 수직인 단위 길이의 벡터이다. 

g1과 g2는 충돌한 기하 구조물이다. 

body1이 normal 벡터 방향으로 depth길이만큼 움직이면 (또는 body2가 반대 방향으로 같은 길이를 움직이면), 접촉 깊이는 0으로 줄어들 것이다. 이것은 normal 벡터가 body1의 "안쪽"을 가리킨다는 의미이다. 

실세계에서, 두 강체들간의 접촉은 복잡한 문제이다. 접촉점에 의해 표현되는 접촉은 근사값일 뿐이다. 접촉 "부분" 또는 "표면"이 물리적으로 더 정확하겠지만, 이러한 표현은 빠른 속도의 시뮬레이션 소프트웨어에서 일종의 도전이다. 

시뮬레이션에 추가된 각각의 접촉점은 속도를 더 느리게 하기때문에, 때로는 속도의 이유로 접촉점들을 무시한다. 예를 들어, 두 상자가 충돌할 때 적절한 시뮬레이션을 위해 많은 접촉점들이 필요하겠지만, 최선의 접촉점 세 개 만을 유지하는 쪽을 택할 것이다.

### 기하 구조 {#기하-구조}

기하 구조 오브젝트들(줄여서 "geoms")은 충돌 시스템에서 기본 요소이다. 기하 구조는 (구나 상자 같은) 단일 강체를 나타내거나, "공간(space)"이라고 부르는 여러 기하 구조들의 그룹을 나타낸다. 

기하 구조는 서로 충돌할 수 있고, 0이상의 접촉점을 발생한다. 공간은 내부의 기하 구조들끼리 충돌하면 접촉점을 발생하는 추가 능력을 가지고 있다. 

기하 구조는 배치 가능할 수도 불가능할 수도 있다. 배치 가능한 기하 구조(placeable geom)는 강체처럼 시뮬레이션 중에 변경이 가능한 위치 벡터와 3x3 회전 행렬을 가진다. 배치 불가능한 기하 구조(non-placeable geom)는 그런 능력이 없다 - 예를 들어, 움직일 수 없는 정적 오브젝트를 표현한다. 공간은 배치 불가능한 기하 구조이다, 왜냐하면 내부의 기하 구조는 각기 자신의 위치와 회전을 가질 수는 있어도 공간 자체가 위치와 회전을 가진다는 것은 말이 않되기 때문이다. 

강체 시뮬레이션에서 충돌 엔진을 사용하기 위해서, 배치 가능한 기하 구조는 강체 오브젝트와 결합된다. 이로써 충돌 엔진이 강체로부터 기하 구조의 위치와 회전을 얻어 올 수 있게 된다. 기하 구조는 기하 속성들(크기, 모양, 위치, 회전)을 가지고 있지만 속도, 질량 같은 역학 속성들은 가지고 있지 않기 때문에 강체와 별개로 보아야 한다. 강체와 기하 구조가 함께 시뮬레이션 오브젝트의 속성을 표현하는 것이다. 

모든 기하 구조는 구, 평면, 또는 상자 처럼 클래스(class)의 인스턴스이다. 아래에서 설명하듯이 많은 수의 내장 클래스가 존재한다. 그리고 자신만의 클래스를 정의할 수도 있다. 

배치가능한 기하 구조의 기준점은 자신의 위치 벡터로 제어되는 점이다. 표준 클래스의 기준점은 기하 구조의 질량 중심과 일치한다. 이런 특징때문에 표준 클래스가 강체에 쉽게 연결된다. 다른 기준점이 요구되면, 기하 구조를 감싸는 변환 오브젝트가 사용될 수 있다. 

모든 기하 구조들에 적용하는 개념과 함수들이 아래에 설명될 것이다. 그후에 다양한 기하 구조 클래스들과 그것들을 조작하는 함수들이 나온다. 

### 공간 {#공간}

공간은 다른 기하 구조들을 수용할 수 있는 배치 불가능한 기하 구조이다. 동역학 대신 충돌에 적용된다는 점을 빼면 "월드(world)"라는 강체 개념과 비슷하다. 

공간은 충돌 검사를 더욱 빨리 하기 위한 것이다. 공간이 없으면, 한 쌍의 기하 구조들마다 접촉점들을 얻기 위해 dCollide를 호출하여 접촉을 발생한다. N개의 기하 구조에 대해 O(N2)개의 테스트가 필요하다. 이것은 많은 오브젝트들을 가진 환경에서는 너무 비싼 계산 비용이다. 

더 나은 접근법은 기하 구조들을 공간에 집어 넣고 dSpaceCollide를 호출하는 것이다. 그러면 공간은 충돌 선별을 수행할 것이다. 충돌 선별이란 잠재적으로(potentially) 충돌가능한 기하 구조 쌍을 빠르게 확인할 수 있다는 뜻이다. 이 쌍들은 콜백 함수로 넘어간다. 콜백 함수는 차례대로 쌍들에 대해 dCollide를 호출한다. 이렇게 하면 쓸데없는 dCollide테스트들로부터 많은 시간을 절약할 수 있다. 콜백 함수에 넘겨지는 쌍들의 수는 모든 가능한 오브젝트 쌍들의 수에 비하면 작은 부분이기 때문이다. 

공간은 다른 공간을 수용할 수 있다. 충돌 검사 공간을 더욱 최적화하기 위해서 충돌 환경을 몇 개의 계층으로 나누는 것이 실용적이다. 아래에서 좀더 자세히 설명할 것이다. 

### 일반 기하 구조 함수 {#일반-기하-구조-함수}

다음 함수들은 어떠한 기하 구조에도 적용될 수 있다. 

<a id="dGeomDestroy"></a>  
void dGeomDestroy (dGeomID);

기하 구조가 속해 있는 공간으로부터 제거한 다음 소멸시킨다. 이 함수 하나로 어떠한 타입의 기하 구조든 소멸시킬 수 있다. 하지만 기하 구조를 생성할 때는 해당 타입에 맞는 생성 함수를 호출해야 한다.   
공간이 소멸할 때는, 청소 모드가 1(기본값)이면, 그 공간 안의 모든 기하 구조들이 자동으로 소멸한다. 

void dGeomSetData (dGeomID, void \*);  
void \*dGeomGetData (dGeomID);

기하 구조에 저장된 사용자 정의 데이타 포인터를 설정/리턴한다. 

void dGeomSetBody (dGeomID, dBodyID);  
<a id="dGeomGetBody"></a>  
dBodyID dGeomGetBody (dGeomID);

배치 가능한 기하 구조에 결합된 강체를 설정/리턴한다. 기하 구조에 강체를 설정하는 것은 강체와 기하 구조의 위치 벡터와 회전 행렬을 자동으로 결합한다. 그러면 한쪽의 위치또는 회전 설정이 기하 구조와 강체 모두에 적용될 것이다. 

강체 ID를 0으로 설정하면 다른 강체로부터 독립적인 자신의 위치와 회전값을 기하 구조에 준다. 기하 구조가 이전에 강체와 연결되었다면, 새로운 독립적인 위치/회전이 강체의 현재 위치/회전에 설정된다. 

배치 불가능한 기하 구조에 이 함수들을 호출하는 것은 ODE의 디버그 빌드에서 런타임 오류를 발생시킨다.

```cpp
void dGeomSetPosition (dGeomID, dReal x, dReal y, dReal z);
void dGeomSetRotation (dGeomID, const dMatrix3 R);
void dGeomSetQuaternion (dGeomID, const dQuaternion);
```

배치 가능한 기하 구조의 위치 벡터, 회전 행렬 또는 사원수를 설정한다. 이 함수들은 [dBodySetPosition](#dBodySetPosition), [dBodySetRotation](#dBodySetRotation) 그리고 [dBodySetQuaternion](#dBodySetQuaternion)과 유사하다. 기하 구조가 강체에 부착되어 있다면, 강체의 위치 / 회전 / 사원수 또한 변경될 것이다.

배치 불가능한 기하 구조에 이 함수들을 호출하는 것은 ODE의 디버그 빌드에서 런타임 오류를 발생시킨다.

<a id="dGeomGetPosition"></a>  
const dReal \* dGeomGetPosition (dGeomID);  
const dReal \* dGeomGetRotation (dGeomID);  
<a id="dGeomGetQuaternion"></a>  
void dGeomGetQuaternion (dGeomID, dQuaternion result);

앞의 두 함수는 기하 구조의 위치 벡터와 회전 행렬에 대한 포인터를 리턴한다. 리턴값들은 내부 데이타 구조체들에 대한 포인터들이다. 기하 구조에 어떠한 변경이 반영되기 전까지 값들은 유효하다. 기하 구조가 강체에 부착되어 있으면, 강체의 위치 / 회전 포인터들이 리턴될 것이다. 즉, 결과는 [dBodyGetPosition](#dBodyGetPosition)또는 [dBodyGetRotation](#dBodyGetRotation)를 호출한 것과 동일할 것이다.

[dGeomGetQuaternion](#dGeomGetQuaternion)는 기하구조의 사원수를 제공되는 공간으로 복사한다. 기하 구조가 강체에 부착되어 있으면, 강체의 사원수가 리턴될 것이다. 즉, 결과 사원수는 [dBodyGetQuaternion](#dBodyGetQuaternion)를 호출한 것과 동일할 것이다. 

배치 불가능한 기하 구조에 이 함수들을 호출하는 것은 ODE의 디버그 빌드에서 런타임 오류를 발생시킨다.

void dGeomGetAABB (dGeomID, dReal aabb\[6\]);

주어진 기하 구조를 감싸는 축 정렬 경계 상자(aabb; axis aligned bounding box)를 리턴한다. aabb 배열의 요소는 (minx, maxx, miny, maxy, minz, maxz)이다. 기하 구조가 공간이라면, 공간 안의 모든 기하 구조를 감싸는 경계 상자가 리턴된다.

이 함수는 경계 상자가 지난번에 계산된 이후로 기하 구조가 움직이지 않았다면, 미리 계산되어 저장된 경계 상자를 리턴할 것이다. 

int dGeomIsSpace (dGeomID);

주어진 기하 구조가 공간이면 1를 리턴, 아니면 0을 리턴한다. 

dSpaceID dGeomGetSpace (dGeomID);

주어진 기하 구조가 수용되어 있는 공간을 리턴한다. 수용된 공간이 없으면 0을 리턴한다. 

int dGeomGetClass (dGeomID);

주어진 기하 구조의 클래스 ID를 리턴한다. 표준 클래스 ID는 다음과 같다: 

{| style="text-align:left" border=1  
| dSphereClass || 구   
|-   
|dBoxClass ||상자   
|-   
|dCCylinderClass ||뚜껑을 씌운 원통   
|-  
|dCylinderClass ||끝이 평평한 원통   
|-  
|dPlaneClass|| 무한 평면 (배치 불가능)   
|-  
|dGeomTransformClass ||기하 구조 변환   
|-  
|dRayClass|| 반직선   
|-  
|dTriMeshClass ||삼각형 메시   
|-  
|dSimpleSpaceClass ||단순 공간   
|-  
|dHashSpaceClass ||해시 테이블 기반 공간   
|}

사용자 정의 클래스들은 자신만의 ID를 리턴할 것이다.

void dGeomSetCategoryBits (dGeomID, unsigned long bits);  
void dGeomSetCollideBits (dGeomID, unsigned long bits);  
unsigned long dGeomGetCategoryBits (dGeomID);  
unsigned long dGeomGetCollideBits (dGeomID);

주어진 기하 구조에 대한 "범주(category)"와 "충돌(collide)" 비트 필드를 설정/리턴한다. 이 비트 필드는 어떤 기하 구조가 서로 상호 작용하는 지를 공간이 관리하기 위해 사용된다. 비트 필드는 최소한 32비트의 크기를 가진다. 새로 생성된 기하 구조에 대해 기본적인 범주와 충돌 값들이 설정된다. 

void dGeomEnable (dGeomID);  
void dGeomDisable (dGeomID);  
int dGeomIsEnabled (dGeomID);

기하 구조를 활성화/비활성화 시킨다. 비활성화된 기하 구조는 여전히 공간의 일원일지라도 [dSpaceCollide](#dSpaceCollide)와 [dSpaceCollide2](#dSpaceCollide2)함수들에 완전히 무시된다. 

dGeomIsEnabled()는 기하 구조가 활성화된 상태이면 1를 리턴하고 아니면 0을 리턴한다. 새로운 기하 구조는 활성화된 상태로 생성된다.

### 충돌 검사 {#충돌-검사-2}

충돌 검사 "월드"는 공간을 만들고 그 공간에 기하 구조들을 추가함으로써 생성된다. 매 시간 스텝마다 서로 충돌하는 기하 구조들에 대한 접촉 리스트를 산출하고 싶을 것이다. 세 함수가 이런 일을 한다: 

* [dCollide](#dCollide) 두 기하 구조의 교차를 검사하고 접촉점들을 생성한다.   
* [dSpaceCollide](#dSpaceCollide)공간 내에서 잠재적으로 충돌할 수 있는 기하 구조 쌍을 결정하고 각 후보 쌍에 대해 콜백 함수를 호출한다. 이것은 접촉점들을 직접적으로 발생하지 않는다. 왜냐하면 사용자는 몇개 쌍들을 특별히 처리하고 싶을 지도 모르기 때문이다 - 예를 들면, 그냥 무시하거나 다른 접촉 발생 전략을 사용할 수 있다. 콜백 함수에서 그런 결정을 한다. 콜백 함수는 각 쌍에 대해 dCollide를 호출할 지를 선택할 수 있다.   
* [dSpaceCollide2](#dSpaceCollide2) 한 공간의 기하 구조와 다른 공간의 기하 구조가 잠재적으로 충돌할 수 있는 지를 결정하고, 각 후보 쌍에 대해 콜백 함수를 호출한다. 또한 공간에 대해 공간이 아닌 기하 구조를 검사할 수도 있다. 이 함수는 충돌 계층이 있을 때, 즉 다른 공간들을 수용하는 공간들이 있을 때 유용하다. 

충돌 시스템은 어떤 오브젝트를 테스트할지를 결정하기 위해 사용자에게 최대한의 융통성을 제공하도록 설계되었다. 그러한 이유로 모든 접촉점들을 생성하는 하나의 함수 대신 세 가지의 충돌 함수를 제공하는 것이다. 

공간은 다른 공간들을 수용할 수 있다. 이러한 부공간들은 전형적으로 서로 가까이 위치한 기하 구조(또는 공간)들의 집합을 나타낸다. 충돌 월드를 계층으로 나눔으로써 추가 충돌 성능을 획득할 수 있다. 이것이 유용한 예가 여기있다: 

어떤 지형위에 운전 중인 차 두대가 있다고 가정하자. 각 차는 많은 기하 구조들로 이루어져 있다. 이 기하 구조들이 하나의 같은 공간에 속해 있다면, 두 차들 사이의 충돌 계산이 항상 총 기하 구조 수에 비례할 것이다(또는 사용된 공간 타입에 따라 제곱에 비례할 수도 있다). 

충돌 계산의 속도를 높이기 위해 차를 표현하는 개별 공간을 생성한다. 차 기하 구조들은 차-공간들에 포함되고, 차-공간들은 최상위 공간에 포함된다. 매 시간 스텝마다 최상위 공간에 대해 [dSpaceCollide](#dSpaceCollide)를 호출한다. 이것은 차-공간들(실제로는 차의 경계 상자들) 사이에 한번의 교차 검사만을 해서, 부딪히면 콜백함수를 호출할 것이다. 그러면 콜백 함수는 [dSpaceCollide2](#dSpaceCollide2)를 사용해서 차-공간 안의 기하 구조들을 검사할 것이다. 차가 서로 가까이 있지 않고 떨어져 있으면 콜백 함수도 호출되지 않고, 불필요한 검사를 수행하는데 드는 시간을 더이상 낭비하지도 않는다. 

공간 계층이 사용되고 있다면, 콜백 함수는 반복적으로 호출될 것이다. 즉, [dSpaceCollide](#dSpaceCollide)가 콜백 함수를 호출하면, 콜백 함수가 차례대로 [dSpaceCollide](#dSpaceCollide)를 호출한다. 이러한 경우에 사용자는 콜백 함수가 적절히 재귀 호출되도록 확실히 해야 한다. 

여기에 모든 공간과 부공간들을 순회하면서 모든 교차하는 기하 구조들에 대해 가능한 모든 접촉점들을 생성하는 콜백 함수의 예가 있다: 

```cpp
 void nearCallback (void *data, dGeomID o1, dGeomID o2)
 {
   if (dGeomIsSpace (o1) || dGeomIsSpace (o2)) {
     // colliding a space with something
     dSpaceCollide2 (o1,o2,data,&nearCallback);
     // collide all geoms internal to the space(s)
     if (dGeomIsSpace (o1)) dSpaceCollide (o1,data,&nearCallback);
     if (dGeomIsSpace (o2)) dSpaceCollide (o2,data,&nearCallback);
   }
   else {
     // colliding two non-space geoms, so generate contact
     // points between o1 and o2
     int num_contact = dCollide (o1,o2,max_contacts,contact_array,skip);
     // add these contact points to the simulation
     ...
   }
 }

 ...

 // collide all objects together
 dSpaceCollide (top_level_space,0,&nearCallback);
```

  

공간 콜백 함수는 공간이 [dSpaceCollide](#dSpaceCollide)나 [dSpaceCollide2](#dSpaceCollide2)에 의해 처리중일 때는 공간을 수정할 수 없다. 예를 들면, 공간에 기하 구조를 추가하거나 삭제할 수 없고, 공간내의 기하 구조의 위치를 변경할 수 없다. 변경을 하는 것은 ODE의 디버그 빌드에서 런타임 오류를 일으킨다. 

#### 범주와 충돌 비트 필드 {#범주와-충돌-비트-필드}

각각의 기하 구조는 "구조(category)"와 "충돌(collide)" 비트 필드를 가진다. 이것은 어떤 기하 구조가 상호 작용해야 하고 어떤 것이 상호 작용해서는 안되는지를 결정하는 데 보조적인 역할을 한다. 이것을 사용하는 것은 선택적이다 - 기본적으로 기하 구조는 다른 기하 구조와 충돌 할 수 있다고 여겨진다. 

비트 필드에서 각 비트 위치는 다른 오브젝트 범주를 나타낸다. 이러한 범주들의 실제 의미는 사용자에 의해 정의된다. 범주 비트 필드는 하나의 기하 구조가 어떤 범주에 속해있는 지를 가리킨다. 충돌 비트 필드는 기하 구조가 충돌 검사 중에 어떤 범주의 기하 구조들과 충돌할 지를 가리킨다. 

한 쌍의 기하 구조에 대해 둘 중 하나가 다른 하나의 범주 비트에 대응하는 충돌 비트를 가지고 있을 때만 [dSpaceCollide](#dSpaceCollide)와 [dSpaceCollide2](#dSpaceCollide2)에 의해 콜백 함수로 넘겨질 것이다: 

```cpp
 // test if geom o1 and geom o2 can collide
 cat1 = dGeomGetCategoryBits (o1);
 cat2 = dGeomGetCategoryBits (o2);
 col1 = dGeomGetCollideBits (o1);
 col2 = dGeomGetCollideBits (o2);
 if ((cat1 & col2) || (cat2 & col1)) {
   // call the callback with o1 and o2
 }
 else {
   // do nothing, o1 and o2 do not collide
 }
```

[dSpaceCollide](#dSpaceCollide)와 [dSpaceCollide2](#dSpaceCollide2)만이 이 비트 필드를 쓸 수 있고, [dCollide](#dCollide)는 이것을 무시할 거라는 점에 주의하라. 

전형적으로 기하 구조는 오직 하나의 범주에 속해 있기 때문에, 하나의 비트만이 범주 비트 필드에 설정될 것이다. 비트 필드는 최소한 32비트 크기를 보장하므로, 사용자는 32개의 오브젝트까지 임의의 상호작용 패턴을 명시할 수 있다. 만약 32가지 이상이 된다면, 그들 중 몇가지는 같은 범주를 가져야 할 것이다. 

가끔씩 범주 필드는 다수의 비트를 가질 수도 있다. 예를 들어, 기하 구조가 공간이라면 공간의 범주를 그 공간안의 모든 기하 구조의 범주의 합으로 설정하고 싶을 것이다. 

**설계 노트:** 왜 하나의 범주 비트 필드를 가지고 검사하지 않는가? 이게 더 간단하지만, 단일한 비트 필드는 몇가지 상호 작용 패턴을 표현하는 데 더 많은 비트가 필요하다. 예를 들어, 32개의 기하 구조가 5차원의 육면체 상호 작용 패턴을 가지고 있다면, 더 간단한 분류에서 80비트가 필요하다. 더 간단한 분류는 어떤 상황에 대해 어떤 범주가 알맞는 지에 대한 결정을 더 어렵게 한다. 

#### 충돌 검사 함수 {#충돌-검사-함수}

<a id="dCollide"></a>  

```cpp
int dCollide (dGeomID o1, dGeomID o2, int flags,
             dContactGeom *contact, int skip);
```

잠재적으로 충돌 가능성이 있는 기하 구조 o1과 o2에 대해, 접촉 정보를 발생한다. 내부적으로 o1과 o2에 대해 클래스에 특화된 적절한 충돌 함수를 호출한다. 

flags는 기하 구조가 부딪힐 때 접촉이 발생하는 방식을 명시한다. 플랙의 하위 16비트는 발생할 최대 접촉점의 수를 명시한다. 이 수가 0이면, 함수는 1로 간주한다. 나머지 비트들은 0이어야 한다. 미래에 아마 여러가지 접촉 발생 전략들로부터 선택하는데 사용될지도 모른다. 

contact는 dContactGeom 구조체 배열에 대한 포인터이다. 배열은 최소한 최대 접촉 수를 감당할 수 있어야 한다. 이 dContactGeom 구조체들은 배열내의 더 큰 구조체들에 포함될 수도 있다. - skip는 배열 내에서 다음 구조체로 넘어가기 위한 바이트 오프셋이다. skip이 sizeof(dContactGeom)이면, contact는 일반적인 c스타일의 배열에 대한 포인터이다. skip이 sizeof(dContactGeom)보다 더 작으면 오류이다. 

기하 구조가 충돌하면, 이 함수는 생성된 접촉점들의 수를 리턴하고 접촉 배열을 갱신한다, 그렇지 않으면 0을 리턴하고 접촉 배열은 손대지 않는다. 

o1또는 o2에 공간을 넘기면, 이 함수는 o1내의 모든 오브젝트들을 o2내의 모든 오브젝트들과 충돌 검사를 실시하고, 결과 접촉점들을 리턴한다. 이러한 공간과 기하 구조 (또는 공간과 공간) 사이의 충돌 검사 방법은 사용자가 제어할 수 없다. 제어를 얻기 위해, [dSpaceCollide](#dSpaceCollide)또는 [dSpaceCollide2](#dSpaceCollide2)를 사용하라. 

o1과 o2가 같은 기하 구조라면, 이 함수는 아무 일도 하지 않고 0을 리턴한다. 기술적으로 말해서, 자기 자신과 충돌하는 오브젝트는 이 경우에 접촉점을 찾아봤자 쓸모가 없다. 

이 함수는 o1과 o2가 같은 공간에 있는지(또는 어느 공간에 있는지)를 신경쓰지 않는다. 

<a id="dSpaceCollide"></a>  

```cpp
void dSpaceCollide (dSpaceID space,
                   void *data, dNearCallback *callback);
```

공간 안의 잠재적으로 충돌할 가능성이 있는 기하 구조 쌍을 결정하고, 각 후보 쌍에 대해 콜백 함수를 호출한다. 콜백 함수는 다음과 같이 정의된다:

typedef void dNearCallback (void \*data, dGeomID o1, dGeomID o2);

data 인자는 [dSpaceCollide](#dSpaceCollide)에서 콜백 함수로 직접 넘어온다. 즉 사용자 정의 데이타이다. o1와 o2는 충돌 가능성이 있는 기하 구조이다.

콜백 함수는 각 쌍에 접촉점을 발생하기 위해 o1과 o2에 대해 [dCollide](#dCollide)를 호출한다. 접촉점들은 접촉 관절로서 시뮬레이션에 추가될 것이다. 사용자 콜백 함수는 어떠한 쌍에 대해 [dCollide](#dCollide)를 호출할 지를 즉, 상호 작용해서는 안된다는 것을 사용자가 결정할 지를 선택할 수 있다. 

충돌하는 공간 안에 포함된 다른 공간들은 특별히 취급되지 않는다. 즉, 회귀되지 않는다. 콜백 함수는 이러한 공간들을 하나 혹은 둘의 기하 구조 인자로서 넘겨받는다. 

dSpaceCollide()는 모든 충돌하는 기하 구조 쌍을 콜백 함수로 보낼 것을 보장하지만, 실수를 해서 충돌하지 않는 쌍을 넘길 가능성도 있다. 실수로 호출하는 수는 공간에 의해 사용되는 내부 알고리즘에 좌우된다. 따라서 [dCollide](#dCollide)가 콜백에 넘겨지는 모든 쌍에 대한 접촉을 리턴할 거라고 예상해선 안된다. 

<a id="dSpaceCollide2"></a>  

```cpp
void dSpaceCollide2 (dGeomID o1, dGeomID o2,
                    void *data, dNearCallback *callback);
```

이 함수는 두 개의 기하 구조(또는 공간)를 인자로 받는다는 점을 제외하면 [dSpaceCollide](#dSpaceCollide)와 비슷하다. o1의 하나의 기하 구조와 o2의 하나의 기하 구조로 된 잠재적으로 충돌할 가능성이 있는 쌍에 대해서 콜백 함수를 호출한다. 

정확한 행동은 o1과 o2의 타입에 달려있다: 

* 하나의 인자가 비공간 기하 구조이고 다른 하나가 공간이면, 그 기하 구조와 공간 안의 오브젝트들 사이의 잠재적인 충돌에 대해 콜백 함수가 호출된다.   
* o1과 o2 둘다 공간이면, o1의 기하구조와 o2의 기하구조로 된 충돌 가능성이 있는 모든 쌍들에 대해 콜백 함수를 호출한다. 어떤 종류의 공간이 충돌되었는가에 따라 다른 알고리즘이 사용된다. 최적화된 알고리즘이 하나도 없으면 다음 두가지 전략 중 하나에 기대한다:   
* #o1의 모든 기하 구조들은 o2에 대해 하나씩 테스트 된다.   
* #o2의 모든 기하 구조들은 o1에 대해 하나씩 테스트 된다.   
:어떤 전략을 쓸 것인가에는 많은 법칙이 있겠지만, 일반적으로는 오브젝트 수가 더 적은 공간이 내부 기하 구조를 하나씩 검사한다.   
* 두 인자 모두 같은 공간이라면, 그 공간에 대해 [dSpaceCollide](#dSpaceCollide)를 호출한 것과 같다.   
* 두 인자 모두 비공간 기하 구조라면, 이 인자들에 대해 콜백 함수를 한번만 호출하면 된다.

만약 이 함수에 하나의 공간과 그 공간내의 어떤 기하 구조 X가 주어진다면, 이 경우는 특별하게 처리되지 않는다. 항상 (X,X) 쌍을 인자로 콜백 함수가 호출될 것이다. 왜냐하면 오브젝트는 항상 자기 자신과 충돌하기 때문이다. 사용자는 이런 경우를 검사해서 무시하거나, [dCollide](#dCollide)에 (X,X) 쌍을 건네주면 된다. (dCollide는 0을 리턴할 것이다)

### 공간 함수 {#공간-함수}

공간의 종류로 몇가지가 있다. 각 종류는 기하 구조들을 저장하기 위한 다른 내부 데이타 구조체와 충돌 컬링을 수행하기 위한 다른 알고리즘을 사용한다: 

* 단순 공간. 어떠한 충돌 컬링도 하지 않는다. - 단순히 모든 기하 구조 쌍에 대해 교차 검사를 수행하고, 경계 상자가 겹치는 쌍들을 보고한다. n개의 오브젝트에 대해 교차 검사를 수행하는 데 필요한 시간은O(n2)이다. 많은 수의 오브젝트들에 대해서 이것을 사용하면 안된다. 하지만, 작은 수의 오브젝트들에 대해서는 선호되는 알고리즘이다. 또한 충돌 시스템의 잠재적인 문제들을 디버깅하는데 유용하다. 

* 다해상도 해시 테이블 공간. 각 기하 구조가 몇몇 3차원 격자들 중 하나에서 셀이 겹치는 지를 기록하는 내부 데이타 구조체를 사용한다. 각 격자는 길이가 2i인 입방체 셀을 가진다. 여기서i는 최소값과 최대값 사이의 정수이다. 오브젝트들이 너무 가까이 모여있지 않을 거란 가정하에 n개의 오브젝트들에 대해 교차 검사를 수행하는 데 필요한 시간은O(n)이다. 

* 쿼드 트리 공간. 충돌 검사를 빠르게 제외하기 위해 미리 할당된 계층적인 격자 기반 경계 상자 트리를 사용한다. 이것은 지형 모양의 세계에서 많은 수의 오브젝트들에 대해 유별나게 빠르다. 사용되는 메모리의 양은 4^depth \* 32 바이트이다. 현재 [dSpaceGetGeom](#dSpaceGetGeom)는 쿼드 트리 공간에 대해 구현되어 있지 않다.

다음은 공간에 대하여 사용되는 함수들이다: 

 dSpaceID dSimpleSpaceCreate (dSpaceID space);  
 dSpaceID dHashSpaceCreate (dSpaceID space);

단순 공간이나 다해상도 해시 테이블 류의 공간을 생성한다. space가 0이 아니면, 새로 생성된 공간을 space에 추가한다. 

```cpp
dSpaceID dQuadTreeSpaceCreate (dSpaceID space, dVector3 Center,
                              dVector3 Extents, int Depth);
```

쿼드 트리 공간을 생성한다. center와 extents는 루트 블럭의 크기를 정의한다. depth는 트리의 깊이를 설정한다 - 생성되는 블럭의 수는 4^depth이다. 

 void dSpaceDestroy (dSpaceID);

공간을 소멸시킨다. dSpaceID 인자를 받아들인다는 점을 제외하면 [dGeomDestroy](#dGeomDestroy)와 똑같이 기능한다. 공간이 소멸할 때, 청소 모드가 1(기본값)이면 그 공간내의 모든 기하구조가 자동으로 소멸한다. 

 void dHashSpaceSetLevels (dSpaceID space, int minlevel, int maxlevel);  
<a id="dHashSpaceGetLevels"></a>  
 void dHashSpaceGetLevels (dSpaceID space, int \*minlevel, int \*maxlevel);

다해상도 해시 테이블 공간에 대해 몇가지 매개 변수들을 설정/리턴한다. 해시 테이블에 사용되는 가장 작은 크기와 가장 큰 셀 크기는 각각 2^minlevel와 2^maxlevel이다. minlevel는 maxlevel과 작거나 같아야 한다. 

[dHashSpaceGetLevels](#dHashSpaceGetLevels)에서는 최소 레벨과 최대 레벨이 포인터로 리턴된다. 만약 포인터가 0이면, 무시되고 아무 인자도 리턴되지 않는다.

 void dSpaceSetCleanup (dSpaceID space, int mode);  
 int dSpaceGetCleanup (dSpaceID space);

공간의 청소 모드를 설정/리턴한다. 청소 모드가 1이면, 공간이 소멸할 때 포함된 기하 구조들이 같이 소멸될 것이다. 청소 모드가 0이면, 그런 일은 일어나지 않는다. 새로운 공간에 대한 기본 청소 모드는 1이다. 

<a id="dSpaceAdd"></a>  
 void dSpaceAdd (dSpaceID, dGeomID);

기하 구조를 공간에 추가한다. 기하 구조가 이미 공간에 있다면 아무 일도 하지 않는다. space 인자가 기하 구조 생성 함수에 주어지면 이 함수가 자동으로 호출된다.

 void dSpaceRemove (dSpaceID, dGeomID);

공간으로부터 기하 구조를 제거한다. 기하 구조가 공간에 실재하지 않으면 아무 일도 하지 않는다. [dGeomDestroy](#dGeomDestroy) 는 기하구조가 공간 안에 있다면 자동으로 이 함수를 호출한다. 

 int dSpaceQuery (dSpaceID, dGeomID);

주어진 기하 구조가 주어진 공간안에 있다면 1를 리턴하고, 그렇지 않으면 0을 리턴한다. 

 int dSpaceGetNumGeoms (dSpaceID);

공간 안에 수용된 기하 구조의 수를 리턴한다.

<a id="dSpaceGetGeom"></a>  
 dGeomID dSpaceGetGeom (dSpaceID, int i);

공간 안에 수용된 i번째 기하 구조를 리턴한다. i는 반드시 0과 dSpaceGetNumGeoms()-1 사이의 값이어야 한다. 

만약 공간에 기하 구조를 추가하고 삭제하는 것을 포함한 어떤 변화가 반영되었다면, 특정 기하 구조의 인덱스 번호가 어떻게 변경될지에 대해서 아무런 보장도 없다. 고로 기하 구조를 열거하는 동안에는 공간에 어떠한 변화도 있어서는 안된다. 

이 함수는 기하 구조들이 0,1,2 등의 순서로 접근될 때 가장 빠르다는 것을 보장한다. 다른 비순차적인 순서는 내부 구현에 따라서 더 느린 접근을 야기한다.

### 기하 구조 클래스 {#기하-구조-클래스}

#### 구 클래스 {#구-클래스}

dGeomID dCreateSphere (dSpaceID space, dReal radius);

주어진 반지름(radius)의 구를 생성하고 ID를 리턴한다. space가 0이 아니면, 그 공간에 구를 추가한다. 기준점은 구의 중심이다. 

void dGeomSphereSetRadius (dGeomID sphere, dReal radius);

구의 반지름을 설정한다.

dReal dGeomSphereGetRadius (dGeomID sphere);

구의 반지름을 리턴한다.

dReal dGeomSpherePointDepth (dGeomID sphere, dReal x, dReal y, dReal z);

구의 점 (x,y,z)의 깊이를 리턴한다. 구 내부의 점은 양의 깊이를, 외부의 점은 음의 깊이를, 표면의 점은 0의 깊이를 갖는다. 

#### 상자 클래스 {#상자-클래스}

dGeomID dCreateBox (dSpaceID space, dReal lx, dReal ly, dReal lz);

(lx,ly,lz)의 길이를 가진 상자를 생성하고 ID를 리턴한다. space가 0이 아니면, 그 공간에 상자를 추가한다. 기준점은 상자의 중심이다. 

void dGeomBoxSetLengths (dGeomID box, dReal lx, dReal ly, dReal lz);

상자의 길이를 설정한다. 

void dGeomBoxGetLengths (dGeomID box, dVector3 result);

result에 상자의 길이를 리턴한다. 

dReal dGeomBoxPointDepth (dGeomID box, dReal x, dReal y, dReal z);

상자안의 점 (x,y,z)의 깊이를 리턴한다. 상자 내부의 점은 양의 깊이를, 외부의 점은 음의 깊이를, 표면의 점은 0의 깊이를 가진다. 

#### 평면 클래스 {#평면-클래스}

```cpp
dGeomID dCreatePlane (dSpaceID space,
                     dReal a, dReal b, dReal c, dReal d);
```

주어진 매개 변수들로 평면을 생성하고 ID를 리턴한다. space가 0이 아니면, 그 공간에 평면을 추가한다. 평면의 방정식은 

''a\*x+b\*y+c\*z = d''

평면의 법선 벡터는 (a,b,c)이고, 길이는 반드시 1이어야 한다. 평면은 배치 불가능한 기하 구조이다. 즉, 배치 가능한 기하 구조와는 달리 위치와 회전을 가지고 있지 않으며 (a,b,c,d)는 항상 전역 좌표계이다. 평면은 항상 정적 환경의 일부분이고 어떠한 움직이는 오브젝트에 묶이지 않는다.

void dGeomPlaneSetParams (dGeomID plane, dReal a, dReal b, dReal c, dReal d);

주어진 평면에 매개 변수를 설정한다. 

void dGeomPlaneGetParams (dGeomID plane, dVector4 result);

주어진 평면의 매개 변수들을 result에 리턴한다. 

dReal dGeomPlanePointDepth (dGeomID plane, dReal x, dReal y, dReal z);

주어진 평면 안의 점 (x,y,z)의 깊이를 리턴한다. 평면 내부의 점은 양의 깊이를, 외부의 점은 음의 깊이를, 표면의 점은 0의 깊이를 가진다. 

#### 뚜껑이 덮힌 원통 클래스 {#TOC--23}

```cpp
dGeomID dCreateCCylinder (dSpaceID space, dReal radius, dReal length);
```

주어진 매개 변수에 따라 뚜껑이 덮힌 원통을 생성하고, ID를 리턴한다. space가 0이 아니면, 그 공간에 원통을 추가한다.   
뚜껑이 덮힌 원통은 양쪽 끝부분에 반구 모양의 뚜껑을 가진 것만 빼면 일반 원통과 같다. 이런 특징은 내부 충돌 검사 코드를 빠르고 정확하게 해준다. length는 뚜껑을 제외한 원통의 길이이다. 원통은 기하 구조의 로컬 Z축에 정렬되어있다. radius는 뚜껑과 원통의 반지름이다.

```cpp
void dGeomCCylinderSetParams (dGeomID ccylinder,
                             dReal radius, dReal length);
```

원통의 매개 변수를 설정한다.

```cpp
void dGeomCCylinderGetParams (dGeomID ccylinder,
                             dReal *radius, dReal *length);
```

radius와 length에 주어진 원통의 매개 변수들을 리턴한다. 

```cpp
dReal dGeomCCylinderPointDepth (dGeomID ccylinder,
                               dReal x, dReal y, dReal z);
```

주어진 원통에서 점 (x,y,z)의 깊이를 리턴한다. 원통 내부의 점은 양의 깊이를, 외부의 점은 음의 깊이를, 표면의 점은 0의 깊이를 가진다.

#### 반직선 클래스 {#반직선-클래스}

반직선은 다른 모든 기하 구조 클래스들과 다르다. 이것은 딱딱한 오브젝트를 표현하지 않는다. 기하 구조의 위치에서 시작해서 기하 구조의 로컬 Z축의 방향으로 뻗어나가는 무한히 얇은 선이다.

반직선과 다른 기하 구조 사이에서 [dCollide](#dCollide)를 호출하는 것은 최대 한 개의 접촉점을 발생시킨다. 반직선은 dContactGeom구조체에서 접촉 정보에 대한 자신만의 방식이 있다. 따라서 이 정보로부터 접촉 관절을 생성하는 것은 아무 쓸모 없다: 

* pos - 반직선이 다른 기하 구조의 표면에 교차한 점이다. 반직선이 기하 구조의 내부또는 외부에서 출발했는지는 관계없다. 

* normal - 다른 기하 구조의 접촉점에서의 표면 법선이다. [dCollide](#dCollide)에 반직선이 첫번째 인자로 넘겨지면 normal는 반직선이 그 표면에서 반사된 방향과 같을 것이다. 그렇지 않으면 반대 부호를 갖게 될 것이다. 

* depth - 반직선의 시작점에서 접촉점까지의 거리이다. 

반직선은 가시성 검사, 발사체의 경로 설정이나 빛, 오브젝트 배치 같은 것들에 유용하다. 

dGeomID dCreateRay (dSpaceID space, dReal length);

주어진 길이(length)의 반직선을 생성하고 ID를 리턴한다. space가 0이 아니면, 그 공간에 반직선을 추가한다. 

void dGeomRaySetLength (dGeomID ray, dReal length);

주어진 반직선의 길이를 설정한다.

dReal dGeomRayGetLength (dGeomID ray);

주어진 반직선의 길이를 리턴한다.

```cpp
void dGeomRaySet (dGeomID ray, dReal px, dReal py, dReal pz,
                 dReal dx, dReal dy, dReal dz);
```

주어진 반직선의 시작점 (px,py,pz)과 방향 (dx, dy, dz)를 설정한다. 반직선의 회전 행렬은 로컬 Z축이 방향과 일치하도록 조정될 것이다. 이것은 반직선의 길이를 조정하지는 않는 것에 주의하라. 

void dGeomRayGet (dGeomID ray, dVector3 start, dVector3 dir);

반직선의 시작 위치(start)와 방향(dir)을 얻는다. 리턴된 방향은 단위 길이 벡터이다. 

#### 삼각형 메시 클래스 {#삼각형-메시-클래스}

임의의 삼각형 집합을 나타낸다. 삼각형 메시 충돌 시스템은 다음 특성을 가진다: 

* 어떠한 삼각형 "수프(soup)"도 표현될 수 있다 --- 즉 삼각형 메시는 가느다란 끈, 팬, 격자 구조를 해서는 안된다.   
* 삼각형 메시는 구, 상자, 반직선 그리고 다른 삼각형 메시들과 상호 작용할 수 있다.   
* 비교적 큰 삼각형 메시에 잘 작동한다.   
* 충돌 검사 속도를 높이기 위해 시간 결맞음(temporal coherence)을 사용한다. 어떤 기하 구조가 삼각형 메시와 충돌 검사를 했을 때, 데이타가 메시 안에 저장된다. 이 데이타는 [dGeomTriMeshClearTCCache](#dGeomTriMeshClearTCCache)함수로 지워질 수 있다. 미래에 이 기능을 비활성화하는 것이 가능해 질 것이다. 

삼각형 메시/삼각형 메시 충돌은 훌륭하게 작동한다. 그러나 세가지 사소한 주의를 요한다: 

* 일반적으로 정확한 충돌 해상도를 위해 stepsize를 줄여야 한다. 오목한 형태 충돌은 기본 형태의 충돌 보다 충돌 기하 구조에 훨씬 더 의존적이다. 게다가 로컬 접촉 기하 구조는 간단한 형태보다 오목한 형태에 대해 더 빨리 (그리고 더 복잡한 방식으로) 변화한다. 볼록한 형태는 구와 입방체 같은 것을 말한다.   
* 효과적으로 충돌을 해결하기 위해서, dCollideTTL는 이전 시간 스텝(timestep)에서 충돌한 삼각형 메시의 위치를 필요로 한다. 이것은 각 충돌 삼각형의 예상 속도를 계산하는 데 사용된다. 충돌 삼각형은 충돌의 방향, 접촉 법선 등을 알아내기 위해 사용된다. 사용자가 매 timestep 마다 이러한 변수들을 갱신해야 한다. 이 갱신은 ODE 자체에 포함되어 있지 않으므로 ODE 외부에서 수행된다. 이를 수행하는 코드는 다음과 같다: 

```cpp
 const double *DoubleArrayPtr =
   Bodies[BodyIndex].TransformationMatrix->GetArray();
 dGeomTriMeshDataSet( TriMeshData,
   TRIMESH_LAST_TRANSFORMATION,
   (void *) DoubleArrayPtr );
```

변환 행렬은 4x4 동차 변환 행렬이다. 그리고 "DoubleArray"는 16개의 행렬 요소 배열이다. 

**주의: 삼각형 메시 클래스는 아직 확정되지 않았기 때문에 미래에 API의 변경이 있을 수 있다** 

```cpp
dTriMeshDataID dGeomTriMeshDataCreate();
void dGeomTriMeshDataDestroy (dTriMeshDataID g);
```

메시 데이타를 저장하는 데 사용하는 dTriMeshData 오브젝트를 생성하고 소멸시킨다. 

```cpp
void dGeomTriMeshDataBuild (dTriMeshDataID g, const void* Vertices,
                           int VertexStride, int VertexCount,
               const void* Indices, int IndexCount,
                           int TriStride, const void* Normals);
```

dTriMeshData 오브젝트에 데이타를 채워넣을 때 사용된다. 어떠한 데이타도 복사되지 않으므로 유효한 포인터를 이 함수에 넘겨야한다. 다음은 TriStride 인자에 데이타를 넘기는 방법이다: 

```cpp
struct StridedVertex {
 dVector3 Vertex;  // 4th component can be left out, reducing memory usage
 // Userdata
};
int VertexStride = sizeof (StridedVertex);

struct StridedTri {
 int Indices[3];
 // Userdata
};
int TriStride = sizeof (StridedTri);
```

Normals 인자는 선택적이다: 각 삼각형 메시의 표면의 법선. 예를 들면, 

```cpp
 dTriMeshDataID TriMeshData;
 TriMeshData = dGeomTriMeshGetTriMeshDataID (
       Bodies[BodyIndex].GeomID);

 // as long as dReal == floats
 dGeomTriMeshDataBuildSingle (TriMeshData,
       // Vertices
       Bodies[BodyIndex].VertexPositions,
       3*sizeof(dReal), (int) numVertices,
       // Faces
       Bodies[BodyIndex].TriangleIndices,
       (int) NumTriangles, 3*sizeof(unsigned int),
       // Normals
       Bodies[BodyIndex].FaceNormals);
```

이렇게 미리 계산해 놓는 것은 접촉을 평가하는 동안 시간을 절약해준다. 그러나 꼭 필요하지는 않다. 빌드하기 전에 평면 법선들을 계산하고 싶지 않다면 (또는 거대한 메시를 가지고 있고 오직 적은 수의 평면들만이 접촉하고 시간을 절약하고 싶다면), Normals 인자에 단지 "널(NULL)"을 넣어주면된다. 그리고 dCollideTTL가 스스로 법선 계산을 할 것이다. 

```cpp
void dGeomTriMeshDataBuildSimple (dTriMeshDataID g, const dVector3*Vertices,
                                 int VertexCount, const int* Indices,
                                 int IndexCount);
```

편의를 위해 제공되는 간단한 빌드 함수.

typedef int dTriCallback (dGeomID TriMesh, dGeomID RefObject, int TriangleIndex);  
void dGeomTriMeshSetCallback (dGeomID g, dTriCallback \*Callback);  
dTriCallback\* dGeomTriMeshGetCallback (dGeomID g);

선택적인 삼각형당 콜백 함수. 사용자가 특정 삼각형의 충돌을 제어할 수 있게 해준다. 리턴 값이 0이면 아무런 접촉도 발생되지 않을 것이다. 

```cpp
typedef void dTriArrayCallback (dGeomID TriMesh, dGeomID RefObject,
                               const int* TriIndices, int TriCount);
void dGeomTriMeshSetArrayCallback (dGeomID g, dTriArrayCallback* ArrayCallback);
dTriArrayCallback *dGeomTriMeshGetArrayCallback (dGeomID g);
```

선택적인 기하 구조당 콜백 함수. 사용자가 모든 충돌하는 삼각형의 리스트를 한번에 얻을 수 있게 해준다. 

```cpp
typedef int dTriRayCallback (dGeomID TriMesh, dGeomID Ray, int TriangleIndex,
                            dReal u, dReal v);
void dGeomTriMeshSetRayCallback (dGeomID g, dTriRayCallback* Callback);
dTriRayCallback *dGeomTriMeshGetRayCallback (dGeomID g);
```

선택적인 반직선 콜백 함수. 사용자가 반직선이 하나의 삼각형과 충돌했는지를 결정할 수 있게 해준다. 예를 들어, 사용자는 충돌이 일어나는지를 결정하기 위해 비트맵을 표본 추출할 수 있다. 

```cpp
dGeomID dCreateTriMesh (dSpaceID space, dTriMeshDataID Data,
                       dTriCallback *Callback,
                       dTriArrayCallback * ArrayCallback,
                       dTriRayCallback* RayCallback);
```

생성자. Data는 새로 생성된 삼각형 메시가 사용할 정점 데이타를 정의한다. 

void dGeomTriMeshSetData (dGeomID g, dTriMeshDataID Data);

현재 데이타를 바꾼다.

<a id="dGeomTriMeshClearTCCache"></a>  
void dGeomTriMeshClearTCCache (dGeomID g);

내부 시간 결맞음(temporal coherence) 저장을 지운다. 

```cpp
void dGeomTriMeshGetTriangle (dGeomID g, int Index, dVector3 *v0,
                             dVector3 *v1, dVector3 *v2);
```

오브젝트 공간에서 하나의 삼각형을 얻는다. v0, v1 그리고 v2 인자들은 선택적이다. 

```cpp
void dGeomTriMeshGetPoint (dGeomID g, int Index, dReal u, dReal v,
                          dVector3 Out);
```

오브젝트 공간에서 하나의 위치를 얻는다.

void dGeomTriMeshEnableTC(dGeomID g, int geomClass, int enable);  
int dGeomTriMeshIsTCEnabled(dGeomID g, int geomClass);

이 함수들은 삼각형 메시 충돌 검사 중 시간 결맞음의 사용을 활성화/비활성화하는데 사용한다. 삼각형 메시 인스턴스/기하 구조 클래스 쌍 마다 시간 결맞음이 활성화/비활성화될 수 있다. 현재 이것은 구와 상자에만 작동한다. 구와 상자에 대한 기본값은 'false'이다. 

'enable' 인자는 true에 대해 1, false에 대해 0이다. 

시간 결맞음은 선택적이다. 왜냐하면 이것을 허용하는 것은 삼각형 메시가 많은 다른 기하 구조와 충돌할지도 모를 상황에서 미미한 효율성 문제를 야기한다. 삼각형 메시에 대해 시간 결맞음을 활성화하면 이 문제들은 간헐적으로 [dGeomTriMeshClearTCCache](#dGeomTriMeshClearTCCache)를 호출함으로써 쉽게 해결될 수 있다.

#### 기하 구조 변환 클래스 {#기하-구조-변환-클래스}

기하 구조 변환 'T'는 다른 기하 구조 'E'를 감싸는 기하 구조이다. E는 이동하고 기준점에 대해 임의로 회전할 수 있다.

구와 상자같은 대부분의 배치가능한 기하 구조들은 질량 중심에 상응하는 기준점을 가지고 있다. 이때문에 기하 구조들은 역학적인 오브젝트에 쉽게 연결할 수 있다. 변환 오브젝트들은 더 많은 유연성을 제공한다 - 예를 들면, 구의 중심을 이동시키거나 기본값이 아닌 다른 축으로 원통을 회전시킬 수 있다. 

T는 E를 흉내낸다: T는 마치 자신이 E인 것처럼 어떤 공간에 추가되고 강체에 부착되기도 한다. E자체는 절대로 공간에 추가되거나 강체에 부착되지 않는다. E의 위치와 회전은 어떻게 T에 상대적으로 변환되었는가를 알려주는 일정한 값으로 설정된다. E의 위치와 회전이 기본값으로 남겨졌다면, T는 마치 그것을 직접 사용하고 있는 것처럼 E와 똑같이 행동할 것이다. 

dGeomID dCreateGeomTransform (dSpaceID space);

새로운 기하 구조 변환 오브젝트를 생성하고, ID를 리턴한다. space가 0이 아니면, 그 공간에 추가한다. 생성시 감싸인 기하 구조는 0으로 설정된다. 

void dGeomTransformSetGeom (dGeomID g, dGeomID obj);

기하 구조를 g가 감싸는 기하 구조의 변환으로 설정한다. 오브젝트 obj는 어떠한 공간에도 추가되어서는 안되고, 어떠한 강체와도 결합되어서는 안된다. 

g의 청소 모드가 켜져있고, g가 이미 어떤 오브젝트를 감싸고 있다면, 예전의 오브젝트는 새 것으로 대체되기 전에 소멸할 것이다. 

<a id="dGeomTransformGetGeom"></a>  
dGeomID dGeomTransformGetGeom (dGeomID g);

g가 감싸는 기하 구조의 변환을 얻는다. 

void dGeomTransformSetCleanup (dGeomID g, int mode);  
int dGeomTransformGetCleanup (dGeomID g);

기하 구조 변환 g의 청소 모드를 설정/리턴한다. 청소 모드가 1이면, 기하 구조 변환이 소멸할 때 감싸인 오브젝트가 소멸할 것이다. 청소 모드가 0이면 아무일도 일어나지 않는다. 기본 청소 모드는 0이다. 

void dGeomTransformSetInfo (dGeomID g, int mode);  
int dGeomTransformGetInfo (dGeomID g);

기하-변환 기하구조 g의 "정보" 모드를 설정/리턴한다. 모드는 0또는 1이 될 수 있다. 기본 모드는 0이다. 

모드 0의 경우, 변환 오브젝트가 다른 오브젝트와 충돌될 때 (dCollide(tx\_geom,other\_geom,...)를 이용할 때), dContactGeom 구조체의 g1는 변환 오브젝트에 의해 감싸지는 기하 구조에 설정된다. g1값은 호출자가 변환되는 기하 구조의 타입을 조사할 수 있게 해주지만, 호출자가 전역 좌표계에서 위치를 결정하거나 강체를 결합하는 것을 허락하지 않는다. 이러한 속성들은 둘다 감싸인 기하 구조에 대해 다르게 사용된다. 

모드 1의 경우, dContactGeom구조체의 g1는 변환 오브젝트 자체에 설정된다. 이것은 오브젝트를 다른 종류의 기하 구조처럼 보이게 한다. [dGeomGetBody](#dGeomGetBody)는 부착된 강체를 리턴할 것이고, [dGeomGetPosition](#dGeomGetPosition)는 전역 위치를 리턴할 것이다. 이 경우에 감싸인 기하 구조의 실제 타입을 얻기 위해, [dGeomTransformGetGeom](#dGeomTransformGetGeom)이 사용돼야 한다. 

### 사용자 정의 클래스들 {#사용자-정의-클래스들}

ODE의 기하 구조 클래스들은 내부적으로 C++ 클래스들로 구현된다. 만약 자신만의 기하 구조 클래스를 정의하고 싶다면 두가지 방법이 있다:

1. C 함수들을 사용하라. 당신의 코드와 ODE사이를 깨끗이 구분해주는 이점이 있다.   
1. 클래스들을 ODE의 소스 코드에 직접 추가하라. 이것은 C++를 사용할 수 있어서 구현이 더 명확해질 수 있는 이점이 있다. 또한 당신의 충돌 클래스가 유용하다면 더 나은 방법이다. 

다음은 사용자 정의 기하 구조 클래스에 대한 C API이다.

모든 사용자 정의 기하 구조 클래스는 유일한 정수를 가지고 있다. 새로운 기하 구조 클래스(이것을 'X'라고 부르자)는 ODE에 다음을 제공해야한다: 

1. X와 하나 이상의 클래스들 사이의 충돌 검사와 접촉 생성을 처리하는 함수들. 이 함수들은 dColliderFn 타입이어야 한다. 

```cpp
typedef int dColliderFn (dGeomID o1, dGeomID o2, int flags,
                        dContactGeom *contact, int skip);
```

이것은 [dCollide](#dCollide)와 똑같은 인터페이스를 가진다. 각 함수는 특수한 경우의 충돌을 처리할 것이다. 특수한 경우란 o1의 타입이 X이고 o2가 다른 타입인 경우를 말한다. 

2."선택자" 함수이다. dGetColliderFnFn타입이며, 다음과 같이 정의된다 

typedef dColliderFn \* dGetColliderFnFn (int num);

이 함수는 클래수 번호(num)을 받고, X와 클래스 num의 충돌을 처리하는 함수를 리턴한다. X가 클래스 num과 어떻게 충돌할지를 모르면 0을 리턴해야 한다. 클래스 X와 Y가 충돌한다면, 오직 한쪽에서 다른 한쪽과 충돌하는 함수를 제공할 필요가 있다. 

이 함수는 자주 호출되지 않는다 - 리턴 값은 저장되고 재사용된다. 

3. 이 클래스의 인스턴스의 축 정렬 경계 상자(AABB)를 계산하는 함수. 이 함수는 dGetAABBFn타입이어야 하고, 다음과 같이 정의된다. 

typedef void dGetAABBFn (dGeomID g, dReal aabb\[6\]);

이 함수는 타입 X를 가진 g를 받고, g의 축 정렬 경계 상자를 리턴한다. aabb 배열은 (minx, maxx, miny, maxy, minz, maxz) 요소들을 가진다. 꽉 끼는 경계 상자를 계산하고 싶지 않으면, 각 방향으로 +/- 무한대를 리턴하는 [dInfiniteAABB](#dInfiniteAABB)에 대한 포인터를 제공할 수 있다. 

4. 이 클래스의 인스턴스가 필요로하는 "클래스 데이타"의 바이트 수. 예를 들어 구는 자신의 반지름을 클래스 데이타 영역에 저장하고, 상자는 측면 길이를 저장한다. 

기하 구조 클래스에 대해 다음은 선택적이다: 

1. 클래스 데이타를 소멸시키는 함수. 대부분의 클래스는 이 함수가 필요없다. 그러나 몇몇은 힙 메모리를 해제하거나 다른 자원들을 해제하기를 원할 수 있다. 이 함수는 dGeomDtorFn타입이어야 하고, 다음과 같이 정의된다 

typedef void dGeomDtorFn (dGeomID o);

인자 o는 타입 X를 가진다. 

2. 주어진 경계 상자(AABB)가 X의 인스턴스와 충돌하는 지를 검사하는 함수. 이것은 공간 충돌 함수에서 early-exit 검사에 사용된다. 이 함수는 dAABBTestFn타입이어야 하고, 다음과 같이 정의된다 

typedef int dAABBTestFn (dGeomID o1, dGeomID o2, dReal aabb2\[6\]);

인자 o1는 타입 X를 가진다. 이 함수가 제공되면, o1가 기하 구조 o2와 충돌할 때 [dSpaceCollide](#dSpaceCollide)에 의해 호출된다. aabb2가 o1과 교차하면 1를 리턴하고, 그렇지 않으면 0을 리턴한다.

이것은 큰 지형들에 유용하다. 지형은 일반적으로 충돌 검사에 크게 쓸모가 없는 매우 큰 경계 상자들을 가지고 있다. 이 함수는 다른 오브젝트의 경계 상자를 지형에 대해 검사할 수 있다. 특수한 충돌 함수를 호출하여 계산상 어려움을 꺽지 않아도된다. 이것은 GeomGroup 오브젝트들에 대해 검사할 때 큰 도움이 된다.

다음은 사용자 정의 클래스를 관리하는 함수들이다: 

int dCreateGeomClass (const dGeomClass \*classptr);

classptr로 정의된 새로운 기하 구조 클래스를 등록한다. 새로운 클래스의 번호가 리턴된다. ODE에서 사용하는 규칙은 dXxxClass이름을 가진 전역 변수에 클래스 번호를 할당하는 것이다. 여기서 Xxx는 클래스 이름이다 (예 dSphereClass). 

다음은 dGeomClass 구조체의 정의이다: 

```cpp
struct dGeomClass {
 int bytes;                  // bytes of custom data needed
 dGetColliderFnFn *collider; // collider function
 dGetAABBFn *aabb;           // bounding box function
 dAABBTestFn *aabb_test;     // aabb tester, can be 0 for none
 dGeomDtorFn *dtor;          // destructor, can be 0 for none
};
```

void \* dGeomGetClassData (dGeomID);

주어진 기하 구조에 대해, 클래스의 사용자 정의 데이타에 대한 포인터를 리턴한다. 

<a id="dCreateGeom"></a>  
dGeomID dCreateGeom (int classnum);

주어진 클래스 번호의 기하 구조를 생성한다. 사용자 정의 데이타 블럭은 초기에 0으로 설정될 것이다. 이 오브젝트는 [dSpaceAdd](#dSpaceAdd)에 의해 공간에 추가될 수 있다. 

새로운 클래스를 구현할 때 다음의 기능을 하는 함수를 작성할 것이다: 

1. 클래스가 아직 생성되지 않았다면, 생성한다. 클래스를 한번만 생성하도록 주의한다.   
1. 클래스의 인스턴스를 만들기 위해 [dCreateGeom](#dCreateGeom)를 호출한다.   
1. 사용자 데이타 영역을 설정한다.

### 복합 오브젝트 {#복합-오브젝트}

다음 오브젝트들을 생각해보자: 

* 맨위에 상자 한개가 있고 각 다리가 상자로 만들어진 테이블.   
* 몇개의 원통들이 서로 연결되어 이루어진 나무의 가지.   
* 각각의 전자를 표현하는 구들을 가진 분자. 

이 오브젝트들이 단단하다면, 이들을 표현하는데 하나의 강체를 사용할 필요가 있다. 그러나 충돌 검사를 수행하는 것이 문제인 것처럼 보인다. 왜냐하면 탁자나 분자 같은 복잡한 모양을 나타내는 단일한 기하 구조 클래스는 없기 때문이다. 해결책은 몇가지 기하 구조를 혼합한 복합(composite) 충돌 오브젝트를 사용하는 것이다. 

복합 오브젝트를 관리하기 위해 추가 함수가 필요하지 않다: 간단히 각 요소 기하 구조를 생성하고 그것을 같은 강체에 부착하면 그만이다. 같은 오브젝트 안의 별개의 기하 구조들을 이동하고 회전하기 위해서, 그들을 감싸는 기하 구조 변환을 사용할 수 있다. 이게 전부다! 

그러나 한가지 주의를 요한다: 너무 가까이 모여있어서 충돌점이 발생되는 복합 오브젝트를 결코 생성해서는 안된다. 예를 들면, 위의 탁자를 생각해 보자. 만약 다리들이 위의 상자와 닿아 있고 탁자가 땅에 옆으로 뉘어져 있다면, 상자들에 대해 발생되는 접촉점들이 다리와 탁자 위 상자가 연결되는 부분과 일치할 것이다. ODE는 현재 동시 발생하는 접촉점들을 최적화하지 않는다. 그래서 이 상황은 수치적 오류와 이상한 동작을 유발할 수 있다. 

이 예에서 탁자의 다리가 닿지 않도록 기하 구조가 조정되어야 한다. 이로써 동시 발생하는 접촉점들이 훨씬 덜 생겨나게 한다. 일반적으로, 접촉면들이 겹치는 것을 피해야 한다. 

### 유용한 함수 {#유용한-함수-2}

```cpp
void dClosestLineSegmentPoints (const dVector3 a1, const dVector3 a2,
               const dVector3 b1, const dVector3 b2,
               dVector3 cp1, dVector3 cp2);
```

주어진 두개의 선분 A(a1-a2)와 B(b1-b2)가 있을 때, A와 B위의 점들 중 서로에 가장 가까이 있는 점을 각각 cp1과 cp2에 리턴한다. 평행 선분의 경우 다수의 해가 존재하므로 최소한 한쪽의 끝점을 포함한 해가 리턴된다. 길이가 0인 선분들에 대해서도 제대로 작동할 것이다. 

```cpp
int dBoxTouchesBox (const dVector3 _p1, const dMatrix3 R1,
                   const dVector3 side1, const dVector3 _p2,
                   const dMatrix3 R2, const dVector3 side2);
```

주어진 상자 (p1,R1,side1)과 (p2,R2,side2)가 있을 때, 교차하면 1를, 그렇지 않으면 0을 리턴한다. p는 상자의 중심이고, R는 상자의 회전 행렬, 그리고 side는 x/y/z 측면 길이를 나타내는 벡터이다. 

<a id="dInfiniteAABB"></a>  
void dInfiniteAABB (dGeomID geom, dReal aabb\[6\]);

이 함수는 너무 딱 들어맞는 축 정렬 경계 상자를 계산하고 싶지 않을 때 사용하는 함수이다. 이것은 각 방향으로 +/- 무한대를 리턴한다.

### 구현 노트 {#구현-노트}

#### 거대한 환경 {#거대한-환경}

충돌 월드는 종종 강체와 결합되지 않은 정적 환경의 일부분인 많은 오브젝트들을 포함한다. ODE의 충돌 검사는 시간을 절약하기 위해 움직이지 않는 기하구조를 검사하고 이러한 오브젝트들에 대해 가능한 많은 정보를 미리 계산하기 위해 최적화되어 있다. 예를 들어, 경계 상자들과 내부 충돌 데이타 구조체들이 미리 계산된다. 

#### 다른 충돌 라이브러리 사용하기 {#다른-충돌-라이브러리-사용하기}

ODE의 충돌 검사를 사용하는 것은 선택적이다 - 접촉 관절을 초기화하기 위해 dContactGeom구조체를 제공할 수만 있으면 다른 충돌 라이브러리를 사용할 수 있다. 

ODE의 동역학의 핵은 사용되는 충돌 라이브러리와 거의 독립적이다, 다음 4가지를 제외하고: 

1\. 각 강체가 연결된 첫번째 기하 구조 오브젝트에 대한 포인터를 저장할 수 있도록, dGeomID 타입이 정의되어야 한다. 

2\. 다음의 원형을 가진dGeomMoved() 함수가 정의되어야 한다: 

void dGeomMoved (dGeomID);

이 함수는 강체가 움직일 때마다 동역학 코드에 의해 호출된다: 이것은 강체와 결합된 기하 구조 오브젝트가 새로운 위치에 있는지를 알려준다. 

3\. 다음의 원형을 가진 dGeomGetBodyNext() 함수가 정의되어야 한다: 

dGeomID dGeomGetBodyNext (dGeomID);

이 함수는 각 강체에 결합된 기하 구조 리스트를 순회하기 위해 동역학 코드에 의해 호출된다. 강체에 부착된 다음 기하 구조를 리턴한다. 기하 구조가 더이상 존재하지 않으면 0을 리턴한다. 

4\. 다음의 원형을 가진 dGeomSetBody() 함수가 정의되어야 한다:

void dGeomSetBody (dGeomID, dBodyID);

이 함수는 강체의 소멸자 코드에서 호출된다 (두번째 인자는 0으로 설정). 기하 구조로부터 강체에 대한 모든 참조를 제거한다. 

ODE로부터 강체 이동 통지를 얻기 위해서 다른 충돌 라이브러리를 쓰고 싶다면, 이러한 타입과 함수를 적절하게 정의해야한다.

## 좋은 시뮬레이션을 만드는 방법 {#좋은-시뮬레이션을-만드는-방법}

\[당분간은 그냥 메모들\] 

### 적분기의 정확성과 안정성 {#적분기의-정확성과-안정성}

* 적분기는 정확한 해를 주지 않는다   
* 안정성이 무엇인가   
* 적분기 타입들 (exp & imp, order)   
* 정확성, 안정성 그리고 작업사이의 교환 거래 

### 행동은 스텝 크기에 달려 있다 {#행동은-스텝-크기에-달려-있다}

* 더 작은 스텝 = 더 정확하고, 더 안정적으로   
* 10\*0.1 는 5\*0.2 와 다르다   
* 마지막 프레임율에서 조정 

### 더 빠르게 하기 {#더-빠르게-하기}

실행 속도는 어떤 요인들에 의해 좌우될까? 각 관절은 시스템으로부터 많은 자유도(DOFs)들을 제거한다. 예를 들어 구상 관절은 3개, 그리고 경첩 관절은 5개를 제거한다. 관절에 연결된 각각 별개된 그룹의 강체들에 대해: 

* m<sub>1</sub>는 그룹내 관절들의 수이다,   
* m<sub>2</sub>는 관절들에 의해 제거된 자유도들의 총 수이다, 그리고   
* n는 그룹내 강체들의 수이다, 

그러한 그룹에 대해 스텝당 계산 시간(computing time per step)는 다음에 비례한다: 

''k<sub>1</sub> O(m<sub>1</sub>) + k<sub>2</sub> O(m<sub>2</sub><sup>3</sup>) + k<sub>2</sub> O(n)''

ODE는 현재 제거된 각 자유도에 대해 한 행/열을 가진 "시스템" 행렬의 인수 분해에 의지한다. (여기에서 나온것이 O(m23)이다). 구상 관절을 사용하는 10개의 강체로 된 사슬에서, 대략 30-40%의 시간이 이 행렬을 채우는 데 소비되고, 30-40%의 시간이 그것을 인수 분해하는 데 소비된다. 

따라서, 시뮬레이션의 속도를 높이기 위해서 고려할 것은: 

* 더 적은 수의 관절을 사용 - 흔히 작은 강체와 강체에 결합된 관절들은 물리적 현실성에 해를 주지않고 순수하게 운동학 "속임수들"로 대체될 수 있다.   
* 복합 관절들을 더 간단한 관절들로 대체한다. 이것은 더 특화된 관절 타입이 정의되면 더 쉬워진다.   
* 더 적은 수의 접촉을 사용.   
* 쿨롱 마찰 접촉(3개의 자유도를 제거한)보다 마찰이 없거나 점성의 마찰 접촉(1개의 자유도를 제거한)을 선호. 

앞으로 ODE는 관절 수에 대해 더 나은 기술들을 구현할 것이다. 

### 안정적으로 만들기 {#안정적으로-만들기}

* 너무 쎈 탄력 / 너무 쎈 힘은 나쁘다.   
* 딱딱한 구속은 좋다.   
* 적분 시간 스텝(integration timestep)에 의존.   
* 가능하면 힘이 없는 관절, 관절 한계, 내장 탄력을 사용하라, 명시적으로 적용된 힘은 피하라.   
* 질량 비율 - 채찍. 크고 작은 질량들을 같이 연결하는 관절은 오류를 낮추기 어렵다.   
* 강체가 시간 스텝(timestep) 동안 정상적인 경우보다 더 빨리 움직인다면   
* 긴 축을 가진 관성 

전역 CFM을 증가시키는 것은 시스템을 수치적으로 더 튼튼하게 하고 안정성 문제에 덜 민감하게 한다. 이것은 또한 시스템을 더 "스펀지"처럼 보이게 한다. 그리고 그만한 대가가 있게 마련이다. 

불필요한 구속들("시도하고 결국은 같은 짓을 하는(try and do the same job)" 둘 이상의 구속들)이 서로 싸우다가 안정성 문제를 일으킬 것이다. 이 문제의 수치적 요인은 시스템 행렬의 특이성이다. 이것의 한 예는 두 구속 관절들이 같은 점에서 같은 강체 쌍을 연결하는 경우이다. 또다른 예는 가상 경첩 관절이 경첩축으로 부터 멀리 떨어져 있는 두개의 구상 관절들로 연결된 두 강체 사이에서 생성된 경우이다(이것은 2개의 구상 관절이 시스템으로부터 6개의 자유도를 제거하려고 하지만 실제 경첩 관절은 5개만 제거하기 때문에 나쁘다). 

불필요한 구속들은 서로 싸우다가 수직력을 파멸시키는 이상한 힘들을 시스템 내에 발생시킨다. 예를 들어, 영향을 받은 강체는 완전히 중력을 무시하고 마치 생명이 있는 것처럼 날아 다니게 될 것이다. 

### 구속 힘 혼합 (CFM) 사용하기 {#구속-힘-혼합-(CFM)-사용하기}

* 특이한 배열을 허용   
* 효과들: 오류 증폭 때문에 불안정하거나 이상한 힘들, LCP solver는 느려질 수 있다   
* 순종적인 관절들을 허용(이것 또한 바람직하지 않을 수 있다) 

### 특이성 피하기 {#특이성-피하기}

* 특이성은 강체의 운동을 구속하기 위해 필요한 수보다 더 많은 수의 관절이 존재할 때 나타난다.   
* 강체들 사이의 다수의 (상반된) 관절들, 특히 관절 + 접촉(서로 연결된 오브젝트들에 부딪치지 않는다).   
* CFM값을 늘린다   
* 의도하지 않은 - 바닥의 상자 사슬, 다른 조립 부품들   
* 적절한 행동을 위해 최소한의 관절을 사용. 적절한 관절들을 사용   
* 전역 CFM을 추가하는 것이 일반적으로 도움이 된다 

### 기타 {#기타}

* 너무 멀리 밀어버릴 때 접촉이 불안정 - 해결: 유연함(softness)을 사용   
* 길이와 질량을 1에 근사한 값으로 유지   
* 경첩 관절의 한계는 +/- pi 

## FAQ {#FAQ}

이번 장은 몇가지 흔한 질문과 답변을 모은 것이다. 더 많은 정보를 얻고 싶으면, 커뮤니티가 지원하는 [ODE Wiki](http://q12.org/cgi-bin/wiki.pl?ODE\_Wiki\_Area)를 확인해 보기 바랍니다.

### 관절을 가지고 강체를 어떻게 정적 환경에 연결합니까? {#관절을-가지고-강체를-어떻게-정적-환경에-연결합니까?}

인자를 (body,0) 또는 (0,body)로 해서 [dJointAttach](#dJointAttach)를 사용하세요. 

### ODE는 그래픽 라이브러리를 필요로 하거나 사용합니까? {#ODE는-그래픽-라이브러리를-필요로-하거나-사용합니까?}

아닙니다. ODE는 계산 엔진이고 그래픽 라이브러리와는 완전히 별개입니다. 그러나 ODE 예제들은 OpenGL를 사용합니다. 그리고 ODE의 가장 흥미로운 사용은 그래픽 라이브러리를 이용해서 시뮬레이션을 사용자에게 보여주는 것입니다. 하지만 그것은 전적으로 당신의 문제입니다. 

### 왜 강체들이 충돌시 되튀거나 관통할까요? 복원력이 0이에요! {#왜-강체들이-충돌시-되튀거나-관통할까요?-복원력이-0이에요!}

가끔 강체들이 복원력없이 충돌할 때, 조금 관통하는 것처럼 보입니다. 그리고 부딪히기만해도 멀리 떨어져 버립니다. 시간 스텝이 클수록 문제는 점점 악화됩니다. 왜 그럴까요? 

접촉 관절 구속은 충돌이 감지된 후에야 적용됩니다. 만약 고정된 시간 스텝이 사용되는 중이라면, 충돌이 감지될 때쯤에 강체는 이미 관통해버렸을 것입니다. 오류 감소 메카니즘이 강체들을 서로 떨어뜨릴 것입니다. 하지만 이것은 ERP 값에 따라 약간의 시간 스텝을 거칠 수 있습니다. 

이러한 관통과 밀어서 떨어뜨리는 것은 때때로 강체들이 되튀는 것처럼 보이게 합니다. 복원력이 켜져있거나 꺼져있는 것과는 전혀 상관없이요. 

다른 시뮬레이터들은 너무 많이 관통하는 것을 막기위해 각각의 강체들이 가변적인 스텝 크기를 거치도록 합니다. 그러나 ODE는 고정 크기의 step들을 거칩니다. 자동적으로 스텝 크기를 선택하는 것은 관절이 있는 강체 시뮬레이터에는 문제가 되기 때문입니다. 

이 문제에 대한 세가지 해결 방법이 있습니다: 

* 더 작은 시간 스텝을 거치세요.   
* 문제가 잘 보이지 않게 ERP를 늘리세요.   
* 어떻게든 자신 만의 가변 크기의 시간 스텝화를 하세요. 

### 어떻게 움직이지 않는 강체가 생성될 수 있습니까? {#어떻게-움직이지-않는-강체가-생성될-수-있습니까?}

달리 말해서, 어떻게 움직이지 않으면서 다른 강체와 상호작용하는 강체를 생성할 수 있냐는 거지요? 답은 대응하는 강체 오브젝트없이 기하 구조만 달랑 생성해서 그런겁니다. 이 기하 구조는 ID가 0인 강체와 결합되어 있는 겁니다. 그러면 접촉 콜백 함수 안에서 ID가 0이 아닌 기하 구조와 0인 기하 구조 사이에 충돌을 감지했을 때, 평소처럼 [dJointAttach](#dJointAttach)함수에 두 ID를 넘겨 주게 됩니다. 이것은 강체와 정적 환경 사이에 접촉을 생성할 것입니다. 

같은 효과를 얻기위해 "부동의" 강체에 아주 높은 질량/관성을 설정하고 매 시간 스텝마다 강체의 위치/회전을 재설정하려고 노력하지 마세요. 아마 예상치 못한 시뮬레이션 오류가 발생할 겁니다. 

### 왜 항상 ERP를 1보다 작게 설정합니까? {#왜-항상-ERP를-1보다-작게-설정합니까?}

ERP값의 정의로부터, ERP를 1로 설정하는 것이 최선의 방법인 것 같습니다. 왜냐하면 모든 관절 오류는 매 시간 스텝마다 완전히 수정되기 때문이죠. 그러나 ODE는 적분기(integrator)에서 다양한 근사를 사용한다. ERP=1 는 관절 오류를 100% 고쳐주지는 않을 겁니다. ERP=1 는 몇몇 경우에는 작동하겠지만, 몇몇 시스템들에서는 불안정한 결과를 낳을 수 있습니다. 이러한 경우에 시스템이 더 잘 작동하도록 ERP 값을 줄일 수 있습니다. 

### 강체에 힘이나 돌림힘을 적용하는 대신 강체의 속도를 직접 설정하는 것이 바람직할까요? {#강체에-힘이나-돌림힘을-적용하는-대신-강체의-속도를-직접-설정하는-것이-바람직할까요?}

시스템을 초기 설정할 때에만 강체의 속도를 직접 설정하세요. 예를 들어 모션 캡처 데이타로부터 매 시간 스텝마다 강체의 속도를 설정한다면, 물리적 모델을 남용하고 있는 것입니다. 즉, 시스템이 자연스럽게 움직도록 하기보다는 원하는 방식으로 움직이도록 강요하는 것입니다. 

시뮬레이션 중에 강체의 속도를 설정하는 더 나은 방법은 관절 모터를 사용하는 것입니다. 한 시간 스텝에 강체의 속도를 바라는 값으로 설정할 수 있습니다. 힘/돌림힘 한계가 충분히 높다면 말이죠. 

### 강체의 속도를 직접 설정할 때, 다른 강체에 연결될 때 왜 강체의 속도가 더 느려지게 되나요? {#강체의-속도를-직접-설정할-때,-다른-강체에-연결될-때-왜-강체의-속도가-더-느려지게-되나요?}

어떤 강체에 연결된 강체들의 속도도 설정하지 않고 그 하나의 강체에만 속도를 설정하는 경우가 있을 수 있습니다. 이렇게 하면, 다음 시간 스텝에서 강체가 관절에서 흩어지는 시스템 오류가 발생합니다. 오류 감소 메카니즘이 결국 이것을 수정하고 다른 강체를 끌어 당길 것입니다. 하지만 이것은 약간의 시간 스텝을 소모하고 원래 강체에 눈에 띄는 "저항(drag)"를 일으킬 것입니다. 

한 강체의 속도를 설정하는 것은 그 강체에만 영향을 줄 것입니다. 만일 다른 강체들에 연결된 상태라면, 이러한 행동을 막기 위해 각각의 강체에 속도를 적절히 설정해야 합니다. 

### 축척의 단위를 약 1.0 정도로 해야 합니까? {#축척의-단위를-약-1.0-정도로-해야-합니까?}

수 밀리미터와 수 그램의 축척에서 몇가지 행동을 시뮬레이션할 필요가 있습니다. 이러한 작은 길이와 질량은 보통 ODE에서 아무 문제없이 잘 작동할 것입니다. 그러나 인수 분해기(factorizer)에서 정확성의 부족으로 발생되는 안정성 문제를 가끔 경험할 것입니다. 이러한 경우라면, 여러분의 시스템에서 길이와 질량의 단위를 대략 0.1..10 정도로 해 보세요. 따라서 시간 스텝 또한 축척되어야 합니다. 큰 수의 길이와 질량이 사용되는 경우에도 같은 지침이 적용됩니다. 

일반적으로, 길이와 질량 값은 약 0.1..1.0 이 더 좋습니다. 단정도(single precision)가 사용되고 있을 때는 이러한 지침이 특히 도움이 됩니다. 

### 자동차를 만들었는데 바퀴가 제대로 머물러 있지 않아요! {#자동차를-만들었는데-바퀴가-제대로-머물러-있지-않아요!}

차 시뮬레이션을 만든다면, 일반적으로 차틀 강체를 만들고 4개의 바퀴 강체를 부착합니다. 그러나, 차를 운전할 때 바퀴가 잘못된 방향으로 회전하는 것을 발견할지도 모릅니다. 마치 관절이 다소 효과가 없어지는 것처럼요. 차가 빨리 움직이고(그래서 바퀴가 빠르게 회전하고), 코너를 돌 때 문제가 관찰됩니다. 바퀴는 "굴대(axles)"가 마치 구부러진 것처럼 적절한 구속을 잃고 회전하는 것처럼 보입니다. 바퀴가 천천히 회전하는 중이거나, 코너를 천천히 돈다면 문제가 덜 드러나 보입니다. 

바퀴의 빠른 회전 속도로 인해 수치적 오류가 발생하는 것이 문제입니다. 이 문제를 해결하기 위해 제공되는 2개의 함수가 있습니다: [dBodySetFiniteRotationMode](#dBodySetFiniteRotationMode)와 [dBodySetFiniteRotationAxis](#dBodySetFiniteRotationAxis)이죠. 바퀴 강체들은 유한 회전 모드를 설정해야 하고, 바퀴의 유한 회전 축들은 경첩축들을 일치시키기 위해 매 시간 스텝마다 설정되어야 합니다. 이것은 대부분의 문제를 희망적으로 고칠 것입니다. 

### <a id="어떻게-"일방적인(one-way)"-충돌-상호-작용을-만들-수-있을까요?"></a>어떻게 "일방적인(one way)" 충돌 상호 작용을 만들 수 있을까요? {#어떻게-"일방적인(one-way)"-충돌-상호-작용을-만들-수-있을까요?}

2개의 강체(A와 B)를 충돌시킨다고 가정합시다. A의 운동은 통상 B의 운동에 영향을 주지만, B는 A에 전혀 영향을 주지 못합니다. 이것이 필요할 때가 있습니다. 예를 들면, 가상 현실 환경에서 B가 물리적으로 시뮬레이션되는 카메라인 경우에 말이죠. 카메라가 실수로 어떤 장면 오브젝트안으로 들어가지 못하게 충돌 반응이 필요하겠지요. 그러나 카메라의 움직임이 시뮬레이션에 영향을 주어선 안되겠지요. 이걸 어떻게 구현할 수 있을까요? 

여기 좋은 방법이 있습니다: 충돌이 감지될 때, A와 B 사이에 접촉 관절을 생성하지 마십시요. 대신 접촉 관절을 B와 0(정적 환경) 사이에 부착하시는 겁니다. 이렇게 하면 강체 A는 B에게 정적이거나 고정된 것처럼 보일 것입니다. 이 방법은 A와 B 사이에 일부 관통을 초래할지도 모르나, 많은 응용 프로그램들에서 별 문제가 되지 않을 것입니다. 

### ODE의 윈도우 버전이 큰 시스템과 충돌하네요. {#ODE의-윈도우-버전이-큰-시스템과-충돌하네요.}

`dWorldStep`을 가진 ODE는 대략 O(''n'')+O(''m''<sup>2</sup>)의 순서에 스택 공간을 필요로 합니다. 여기서, ''n''는 강체의 수이고 ''m''는 모든 관절 구속 차원들의 합입니다. ''m'' 이 크다면, 이것은 아주 많은 공간입니다! 

유닉스 같은 운영 체제는 일반적으로 스택 공간을 필요한 만큼만 할당합니다. 상한은 수백 Mb가 될 수 있습니다. 윈도우 컴파일러가 보통 훨씬 더 작은 스택을 할당합니다. 큰 시스템을 운영할 때 충돌을 경험했다면, 스택의 크기를 늘려보세요. 예를 들어, MS VC++ 컴파일러는 상한을 설정하기 위해 /Stack:num 플랙을 받아들입니다. 

또다른 방법은 `dWorldQuickStep`로 바꾸는 것입니다. 

### 단순한 강체의 회전이 불안정해요! {#TOC--24}

각 면의 길이가 다른 상자를 가지고 있고, 자유 공간에서 그것을 회전시켰다면, 상자가 영원히 같은 속도로 구르기만 하는지를 조사해봐야 합니다. 하지만 때때로 ODE에서 상자는 스스로 속도를 얻어서 "폭주"해버릴 때까지 점점 더 빨리 회전할 것입니다. 여기 그 이유를 설명해 드리겠습니다: 

ODE는 첫번째 반-불명확한 적분기(semi-implicit integrator)를 사용합니다. "반 불명확(semi implicit)"이란 어떤 힘은 불명확한 적분기가 사용되는 것처럼 계산되고, 다른 힘은 적분기가 명확한 것처럼 계산된다는 의미입니다. (구속을 같이 유지하기 위해 강체들에 적용되는) 구속력들은 불명확하고, (사용자에 의해 적용되는) "외부의" 힘들은 명확합니다. 이제, 불명확한 적분기의 부정확성이 에너지의 감소로써 명백해집니다. - 바꿔 말하면 적분기가 시스템을 무디게 한다는 겁니다. 명확한 적분기의 부정확성은 정반대의 효과를 가집니다 - 이것은 시스템의 에너지를 증가시킵니다. 이것은 명확한 1차 적분기를 가지고 시뮬레이션되는 시스템이 폭주할 수 있는 이유입니다. 

그래서, 공간에서 구르는 단일 강체는 효과적으로 명확하게 통합됩니다. 만약 강체의 관성 모멘트가 동일하다면 (즉, 강체가 구라면), 회전축이 일정하게 유지될 것이고, 적분기의 오류는 미약할 것입니다. 강체의 관성 모멘트가 동일하지 않다면, 모멘트가 다른 회전 방향사이에서 옮겨짐에 따라 회전축이 흔들거릴 것입니다. 이것은 물리적으로 올바른 행동입니다만, 적분기의 더 큰 오류를 유발합니다. 이 경우에 적분기는 명확해서 오류가 에너지를 증가시킵니다. 이것은 갈수록 더 빠른 회전을 일으키고, 더 많은 오류를 발생시켜 폭주로 유도합니다. 이 문제는 특히 긴 얇은 오브젝트들에 명백히 나타납니다. 그러한 오브젝트들에서 3개의 관성 모멘트들이 동일하지 않습니다. 

이를 막기위해, 다음 중에서 한 개 이상을 실행하세요: 

*   자유롭게 회전하는 강체들이 동적으로 대칭적인지(즉, 모든 관성 모멘트가 같은지 - 관성 행렬은 상수와 단위 행렬의 곱)를 확실히 하세요. 여러분은 마치 긴 얇은 상자가 구의 관성을 가진 것처럼 여전히 그것을 렌더링하고 충돌할 수 있습니다. 
*   자유롭게 회전하는 강체들이 너무 빨리 돌지 않는지(즉, 큰 돌림힘을 적용하지 않거나 추가적인 감쇠력을 공급하지 않는지)를 확인하세요. 
*   추가 감쇠 요소를 환경에 추가하세요. 즉, 에너지를 반사할 수 있는 탄력있는 충돌을 사용하지 마세요. 
*   더 작은 시간 스텝들을 사용하세요. 이것은 두가지 나쁜 점이 있습니다: 이것은 느리고, ODE가 오직 1차 적분기를 가지고 있어서 추가된 정확성이 극히 미미합니다. 

*   더 높은 차수의 적분기를 사용하세요. ODE에서 이것은 아직 선택 사항이 아닙니다. 

향후에 선택된 강체가 어떠한 회전 오류도 갖지 않도록 제가 회전 동역학을 수정할지도 모르겠습니다. 

### 바퀴같은 구르는 강체가 가끔 기하 구조들 사이에 박혀서 꿈쩍을 안하네요. {#TOC-.}

다수의 기하 구조 오브젝트들로 이루어진 환경 위로 강체들이 굴러 다니는 시스템을 생각해보세요. 예를 들면, 이것은 지형 위를 움직이는 차일 수도 있습니다. 회전하는 강체들은 바퀴가 되겠지요. 강체들이 한 기하 구조 오브젝트에서 다른 한 쪽으로 굴러 갈 때나 다수의 접촉점을 받아들일 때 희한하게 멈춰버리는 것을 발견한다면, 다른 접촉 마찰 모델을 사용할 필요가 있습니다. 

#### 문제 {#TOC--25}

그런 시스템의 예가 그림 13에 나와있습니다. 이 그림은 공 하나가 비탈길을 굴러 내려오다가 땅에 닿는 장면을 보여줍니다. 

[![ODE V0.5 사용자 가이드 그림 13](/ko/galexandria/ode-v05-users-guide/images/rollingcontact.jpg)](/ko/galexandria/ode-v05-users-guide/images/rollingcontact.jpg)

**그림 13:** 구르는 접촉에 관한 문제 

통상, 공은 땅을 따라 오른쪽으로 계속 굴러가야 합니다. 그러나, ODE의 기본 접촉 마찰 모드가 사용중이면, 공은 땅에 닿을 때 완전히 멈추게 될 것입니다. 왜 그럴까요? 

ODE는 마찰을 근사하는 두가지 방법을 가지고 있습니다: 기본 방법(상수-힘-한계 근사 또는 "상자 마찰"이라 불리는)과 개선된 방법("마찰 피라미드 근사1"이라 불리는)이 있습니다. 접촉 관절의 표면 모드 필드에 dContactApprox1 플랙을 설정해서 개선된 방법을 사용할 수 있습니다. 

위의 그림을 보세요. 두개의 접촉점들이 있지요, 하나는 공과 비탈길 사이에, 다른 하나는 공과 땅 사이에 있습니다. 만약 상자 마찰 모드가 두 접촉 모두에 사용되고 mu 매개 변수가 dInfinity에 설정된다면, 공이 접촉점에서 비탈길이나 땅에 대하여 미끄러질 수 없습니다. 

공 접촉점에서 어떠한 미끄러짐도 가능하지 않다면, 공의 중심이 접촉점 주변의 호를 그리는 경로를 따라 움직여야 합니다. 따라서 공의 중심은 "호1"과 "호2" 경로를 동시에 움직여야 합니다. 양쪽 경로를 한번에 만족시켜주는 유일한 방법은 움직임을 완전히 멈추는 것입니다. 

이것은 ODE에서 버그가 아닙니다 - 그럼 이게 어떻게 된 일일까요? 실세계에서 오브젝트들은 이렇게 붙잡혀 있지 않습니다. 단순한 "상자" 마찰 근사에서 미끄러짐을 막는 접촉 구속에 사용할 수 있는 접선력이 관통을 방해하는 수직력과 별개인 것이 문제입니다. 이것은 실 세계의 물리적 현상이 아닙니다. 그래서 우리는 비 현실적인 운동이 일어나더라도 놀라서는 안됩니다. 

mu가 0으로 설정되면 이런 문제가 일어나지 않는다는 점에 주의하십시요. 하지만 우리는 실세계를 모델링하기 위해 어느 정도의 마찰량이 필요하기 때문에 이것이 그리 도움이 되는 해결책은 아닙니다. 

#### 해결책 {#TOC--26}

해결책은 접촉면 모드 필드에 dContactApprox1 플랙을 사용하고 mu를 0과 무한대 사이의 적절한 어떤 값으로 설정하는 것입니다. 이 모드는 접촉 수직력이 0이 아니라면 접촉점에서 미끄러짐 방지 접선력만이 있을 것이라는 것을 확실해 줍니다. 위의 예에서 contact-1이 0의 수직력을 가질거라는 것이 밝혀졌습니다. 그래서 contact-1에 적용되는 어떠한 힘도 없고, 문제는 해결될 것입니다! (공은 땅을 따라 적절히 굴러 갈 것입니다.) 

dContactApprox1 모드가 모든 상황에 적합하지 않을 것입니다. 이것이 선택적인 이유입니다. 비록 더 나은 마찰 근사법이 있다고해도, 그것은 진짜 쿨롱 마찰이 아니라는 것을 기억하는 것이 중요합니다. 따라서 여전히 비 물리적인 행동을 나타내는 일부 예에 직면할 수 있습니다.

## 알려진 이슈 {#TOC--27}

*   강체에 질량을 대입할 때 질량의 중심이 강체의 위치에 상대적으로 (0,0,0)이어야 합니다. 사실 이 제한은 처음부터 ODE에 존재해 왔으므로 ODE의 특징 중 하나라고 얼버무려 보는 건 어떨까요? ^^

## ODE 내부 {#TOC-ODE-5}

\[당분간은 메모들\] 

*   모든 6x1 공간속도와 가속도는 내부적으로 위치와 각도로 나뉨. 즉, (위치x, 위치y, 위치z, 각도)의 형태를 가진 4x1 벡터들로 저장됨.
*   Trinkle과 Stewart에 따른 라그랑지 승수속도기반 모델
*   Baraff에 따른 마찰 
*   정확성보다 안정성
*   다른 가능한 방법들을 설명할 것. 실시간 구속들이 어떻게 문제를 더 난하하게 만드는지 논해볼 것. 
*   인수 분해기
*   LCP solver
*   운동 방정식 
*   마찰모델과 근사

적절한 마찰 피라미드나 마찰 원(Baraff의 버전)을 구현하지 않는 이유? 정적/동적인 마찰을 계산할 때 비대칭적인(그리고 아마도 불명확한) 행렬들을 인수분해해야 하기 때문. 속도에 더 중점을 뒀음. 현재의 마찰근사는 오직 대칭적 인수분해만을 필요로 하므로 약 2배정도 빠름. 

### 행렬저장 규칙 {#TOC--28}

인수분해와 같은 행렬 연산들은 매우 느립니다. 따라서 행렬코드에 가장 도움이 되는 방식으로 데이터를 저장해야 합니다. 추후에 4-way SIMD 최적화를 지원할 수 있도록, 한 행씩 행렬을 저장하고 각 행의 요소 수를 4의 배수로 맞췄습니다. 각 행/열의 끝에 추가로 채워넣는 요소들은 반드시 0이어야 합니다. 이것을 "표준포맷"이라 합니다. 앞으로 미래의 CPU들이 4-way SIMD를 지원하감에 따라(특히 빠른 3D 그래픽스를 위해) 이러한 포맷이 진가를 발휘할 것이라 생각합니다. 

예외: 행이나 열이 하나뿐인 행렬(벡터)들은 표준행 포맷을 사용하여 그 요소들을 연속적으로 저장합니다.  즉 중간에는 0을 채원허지 않고, 오직 끝에만 0을 채워넣습니다. 따라서 모든 3x1 부동소수점 벡터는 (x,x,x,0)의 형태를 가진 4x1 벡터로 저장됩니다.

### 내부 FAQ {#TOC-FAQ}

#### 구조체에 붙는 dx 또는 d 접두어는 무슨 뜻입니까? {#TOC-dx-d-}

dx 접두어는 외부에서 절대 볼 수 없는 내부 구조체들에 사용됩니다. d 접두어는 public 인터페이스의 일부분인 구조체들에 사용됩니다.

#### 벡터 반환 {#TOC--29}

ODE가 벡터를 반환하는 방법은 2가지입니다. 다음의 코드를 보시죠. 

```cpp
   const dReal* dBodyGetPosition (dxBodyID);
   void dWorldGetGravity (dxWorldID, dVector3);
```

두번째 방법이 '공식적인' 방법이며, 첫번째는 내부 데이터 구조체의 포인터를 리턴합니다. 첫번째 방법을 사용할 경우 그 데이터가 소멸하면 포인터는 잘못된 메모리 위치를 가르키게 됩니다. 따라서, 안정된 API를 구축하려면 벡터를 매개변수로 전달하여 ODE가 값을 채우도록 하는 것이 포인터를 반환하는 것보다 더 깔끔한 방법입니다. 그 이유로는 두가지가 있습니다. 

1.  포인터를 반환할 때마다 벡터값을 계산해야 할 수도 있습니다. 이럴 경우 계산결과를 내부적으로 변수 하나에 저장해두고 그 포인터를 방법하는 방법은 유효하지 않습니다.
2.  내부 데이터 구조체가 자리를 옮길 수도 있습니다. 이것은 반환된 포인터를 저장해 두었다가 나중에 사용할 때 문제가 됩니다.

현재 ODE에서는 이 두가지 경우가 일어나지 않습니다. 대부분의 반환된 벡터데이터는 내부적으로 저장되어 있으며 항상 같은 주소에 존재합니다. 하지만 미래에 구현방법을 바꿀 수 있는 가능성을 열어두는 것은 나쁘지 않지요. 현재 ODE API는 속도가 중요한 경우(즉, 몸체변환 행렬을 구할 때)에는 포인터를 반환하므로 이런 경우에도 성능저하를 가져오지 않을 것입니다. 뭐, 제 스스로 정한 규칙을 깬 듯은 하지만요.

## 문서정보 {#TOC--30}

### 원문 {#TOC--31}

*   저자: Russell Smith
*   원문보기: [Open Dynamics Engine v0.5 User Guide](https://ode.org/ode-0.5-userguide.html)
