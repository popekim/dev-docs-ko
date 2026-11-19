---
title: "NeHe OpenGL 강좌"
date: 2026-11-19
---

이 페이지에 실린 강좌들은 실수나 적절치 못한 주석들을 가지고 있을 수 있으며, OpenGL을 배울 수 있는 최고의 자료는 아닐 것입니다. 이 코드를 어떻게 사용하던 간에 그것은 여러분의 자유입니다. 저는 그저 OpenGL을 처음 접하시는 분들의 학습과정을 조금이나마 쉽게 해주려고 노력할 뿐입니다. OpenGL을 정말 진지하게 배우고 싶으시다면 다음 책들에 자금을 좀 투자하실 것을 추천합니다: 빨간책 1.4버전(제4판, [원서 - ISBN 032117348](http://www.amazon.com/gp/product/0321173481?ie=UTF8&tag=galexandria-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321173481)1, 번역서 - ISBN 8956742243), [파란책 1.4버전(제4판): ISBN 032117383X](http://www.amazon.com/gp/product/032117383X?ie=UTF8&tag=galexandria-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=032117383X), [오렌지책 1.4버전(쉐이더 언어): ISBN 0321197895](http://www.amazon.com/gp/product/0321197895?ie=UTF8&tag=galexandria-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321197895). 신규 OpenGL 프로그래머들에게는 이 책을 이해하는 것이 다소 어려울 수도 있으나 이 책들은 OpenGL을 다루는 현존하는 책 중에 최고입니다. 또 다른 추천할만한 책은 OpenGL 슈퍼바이블([원서 - ISBN 0672326019](http://www.amazon.com/gp/product/0672326019?ie=UTF8&tag=galexandria-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0672326019) , 번역서 - ISBN 8956742529)입니다. 하지만 이 책에 대한 의견은 매우 다양합니다. 또한 사용하시려고 하는 프로그래밍 언어에 대한 완전한 이해도 매우 중요합니다. 저는 GL 코드가 아닌 부분들에 대해서도 주석을 달아서 설명했습니다만 저는 독학으로 공부했습니다. 따라서 제가 쓴글이 항상 맞거나 좋은 코드라고 할수는 없습니다. 이 사이트에서 배우는 것 중에 어떤 것을 취하여 여러분의 프로젝트에 적용할 것인지는 전적으로 여러분에게 달려있습니다. 코드들을 둘러 보시고, 책을 읽어보시고, 필요한 경우 저에게 질문도 해주십시요. 이 사이트의 코드 정도를 이미 정복하신 분들은 OpenGL.org처럼 보다 전문적인 사이트를 확인해보시기 바랍니다. 또한 제 페이지에 있는 여러 OpenGL 링크들도 방문하십시요. 제가 링크해놓은 각 사이트들은 OpenGL 커뮤니티에 있어 믿을수 없을만큼 훌륭한 자산입니다. 이 사이트들 중 대부분이 GL을 잘 알 뿐만 아니라 저보다 훨씬 프로그래밍도 잘하시는 탁월한 개개인들에 의해 운영됩니다. 제 사이트를 살펴보시는 동안 이 모든것들에 대해서 명심해주셨으면 좋겠습니다. 제가 드릴 수 있는 것들을 잘 즐기셨으면 좋겠습니다.

마지막으로 한마디 하겠습니다. 만약 제 코드가 다른 사람의 코드와 비슷하다고 생각하시는 분들은 저에게 연락을 주십시요. 분명히 밝혀두건데 제가 코드를 빌려온 곳은 MSDN과 저와 마찬가지 방법으로 다른 사람들에게 GL을 가르쳐주기 위해 존재하는 웹사이트들 뿐입니다. 저는 결코 의도적으로 코드를 훔쳐오지 않으며, 저작자를 밝히지 않은채 그러는 일도 결코 없을 것입니다. 하지만 제가 코드를 빌려온 사이트가 다른 사람의 코드를 베껴왔을 가능성도 있습니다. 만약 그런 일이 있다면 저에게 연락을 주십시요. 제 코드를 다시 작성하거나 문제되는 코드를 제 프로그램에서 제거하겠습니다. 그러나 대부분의 코드는 저의 독창물일 것입니다. 무언가를 만들려고 할 때 전혀 어떤 아이디어도 없는 경우에만 코드를 빌려왔고, 그러는 경우에도 그 코드를 이해한 뒤에야만 제 프로그램에 포함시켰기 때문입니다. 저의 강좌중에 실수를 발견하신 경우 아무리 조그만 실수라도 저에게 알려주시면 고맙겠습니다.

저의 기반코드에 대해 한가지 말씀드려야 할 중요한 것은 작성년도가 1997년이라는 것입니다. 그 뒤 많은 변경사항들이 있었고, 이것은 절대 다른 사이트로부터 빌려온 것이 아닙니다. 또한 이 코드가 앞으로도 계속 변경되어 나갈 것입니다. 제가 수정을 가하지 않는 경우는 그 변경을 책임진 사람의 이름을 밝혀둘 것입니다.

1.  [OpenGL 윈도우 설정하기](/ko/galexandria/nehe-opengl-tutorial/1-settingup-opengl-window)
2.  [첫 폴리곤 만들기](/ko/galexandria/nehe-opengl-tutorial/2-your-first-polygon)
3.  [색상 추가하기](/ko/galexandria/nehe-opengl-tutorial/3-adding-color)
4.  [회전](/ko/galexandria/nehe-opengl-tutorial/4-rotation)
5.  [3D 도형](/ko/galexandria/nehe-opengl-tutorial/5-3d-shapes)
6.  [텍스처 매핑](/ko/galexandria/nehe-opengl-tutorial/6-texture-mapping)
7.  [텍스처 필터, 조명, 키보드 컨트롤](/ko/galexandria/nehe-opengl-tutorial/7-texture-filter-lighting-keyboard-controls)
8.  [블렌딩](/ko/galexandria/nehe-opengl-tutorial/8-blending)
9.  3D 공간에서 비트맵을 움직여 보자
10.  3D 세계에서의 로딩 및 이동
11.  깃발효과(파도치는 텍스처)
12.  디스플레이 리스트
13.  비트맵 폰트
14.  외곽선 폰트
15.  텍스처매핑 외곽선 폰트
16.  [그럴듯한 안개효과](/ko/galexandria/nehe-opengl-tutorial/16-cool-looking-fog)
17.  2D 텍스처 폰트
18.  쿼드릭
19.  삼각형스트립을 이용한 파티클엔진
20.  마스킹
21.  선, 안티알리어싱, 타이밍, 직각 뷰, 간단한 사운드
22.  범프매핑,멀티 텍스처링,익스텐션
23.  구체 매핑 쿼드릭
24.  토큰, 익스텐션, 가위 테스트, TGA 로딩
25.  모핑, 파일로부터 물체 읽어오기
26.  클리핑,스텐실버퍼를 이용한 반사
27.  그림자
28.  베지어 패치/전체화면 수정
29.  블리터 함수, RAW 텍스처 로딩
30.  충돌검사
31.  모델로딩
32.  피킹, 알파 블렌딩, 알파 테스트, 정렬
33.  압축/비압축 TGA로딩
34.  높이맵을 이용한 지형
35.  AVI파일 재생
36.  방사 블러 & 텍스처로 렌더링하기
37.  셀 쉐이딩
38.  리소스파일로부터 텍스처 읽어오기 & 삼각형 텍스처매핑
39.  물리효과 시뮬레이션 입문
40.  밧줄 물리효과
41.  불륨감 있는 안개 & IPicture 이미지 로딩
42.  다중 뷰포트
43.  FreeType 폰트
44.  3D 렌즈 플레어와 Occlusion 테스트
45.  정점 버퍼 개체
46.  전체화면 안티알리어싱
47.  [CG 정점 쉐이더](/ko/galexandria/nehe-opengl-tutorial/47-cg-vertex-shader)
48.  [아크볼 회전](/ko/galexandria/nehe-opengl-tutorial/48-arcball-rotation)
