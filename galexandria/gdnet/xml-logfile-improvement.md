---
title: "XML 기술을 이용한 로그파일 향상"
date: 2026-11-19
---

## 소개 {#TOC--}

안녕하세요. 이 글은 XML 기술을 사용하여 로그파일을 향상시키는 방법에 대해 다룰 것입니다. 여기가 게임개발 웹사이트이고 모든 사람들이 게임엔진을 작성하는 데에만 혈안이 되어있는 것처럼 보이긴 하지만 이 글에서 다룰 내용을 다른 소프트웨어 프로그램이나 라이브러리에도 사용할 수 있습니다.

이 모든 것은 제가 GameDev.net 웹개발자 포럼\[2\]에 "[정말 가능한 일일까? 그렇다면 어떻게...?](http://www.gamedev.net/community/forums/topic.asp?topic\_id=299160)"라는 글을 올리면서 시작되었습니다. 그 글의 내용은 제가 최근에 참여한 상용게임 프로젝트의 이벤트 로그시스템을 향상시킬 방법을 모색하면서 쓴 글이었습니다. 그 글을 쓸 당시 제 로그 시스템은 결과물을 곧바로 HTML로 출력하여 읽기에 매우 편했지만, 어느 방법으로도 검색 및 정렬을 할 수 없었습니다. 각 프로젝트 팀은 특정 개발분야에 집중하고 있었으므로 로그파일을 필터링할 수 있는 기능이 매우 중요했습니다. 로그 파일이 순식간에 수천 개의 항목을 포함하면서 수 메가의 용량을 가지게 되는 일이 허다했으니까요. Gamedev.net 토론장 회원들의 도움을 받아 제가 가지고 있던 XML지식을 이 문제영역에 적용시켜봤습니다. Oli님이 제공해주신 간단한 예에 감명을 받은 저는 이 주제를 더욱 깊게 파고 들어 비교적 간단하지만 강력한 해법을 찾아내었습니다. 바로 XSLT와 XML 기술을 함께 사용하는 것이지요. 그 결과로 탄생한 시스템은 개발자, 테스터, 그리고 저 자신에게 있어 매우 강력한 시스템일 뿐만 아니라 매우 놀라웠던 점은 그 시스템을 거의 거저로 얻었다는 것입니다. 여기에서 제가 소개할 솔루션은 그 어떤 값비싼 기술이나 극도로 복잡한 학습과정을 필요로 하지 않으며 나머지 개발팀에게도 비교적 쉽게 설치할 수 있습니다.

이 글은 토론장에서 이뤄졌던 토론을 기초로 여러분의 프로젝트에서 이 기술들을 사용하는 방법을 선보일 것입니다. 이 간단한 기술의 능력을 깨달았을 때 매우 흐뭇했던 기억이 납니다. 따라서 교육/학습용 프로젝트이던, 인디게임이던, 본격적인 상용 벤처이던 간에 이 기술들이 해당 프로젝트의 로그 시스템을 향상시켜 줄 것임을 의심치 않습니다.

## 로그 기능이 필요한 이유 {#TOC--1}

로그 기능이란 소프트웨어 공학에서 흔한 것으로 응용프로그램의 문제점을 진단하는 것을 도와줍니다.

로그파일은 보통 시간순으로 정리된 긴 이벤트 목록으로 구성되어 있습니다. 이 이벤트들은 변수의 상태, 결정의 결과, 경고(문제가 될 여지) 및 오류(명백한 문제 발생) 등을 나타냅니다. 대부분의 복잡한 시스템에서 로그파일은 실행중인 프로그램의 다양한 측면을 분석하는 것을 도와주는 귀중한 존재입니다. 또한 콘솔 화면이 아닌 실제 파일에 내용이 저장되므로 이메일 버그 보고서에 첨부하거나 나중에 자료비교를 위해서 자료를 저장해두기도 용이합니다.

그렇다면 왜 이것을 개발할까요? 간단히 텍스트 파일을 열고 중요한 일이 발생할 때마다 한줄의 정보를 스트림에 출력해주면 되지 않을까요? 그렇지 않아도 이미 코드가 복잡해 죽겠는데 로그작성을 하는데 귀중한 개발시간을 투자하는 이유가 무엇일까요?

위에서 말한 스트림 출력은 기초적인 디버깅용으로 그다지 나쁘지 않은 방법입니다. 그러나 소프트웨어가 점차 커지고 복잡해짐에 따라 수백만줄의 코드라인을 읽어 내려가는 것은 시간낭비이기도 하거니와 따분하기도 합니다. 따분해진 사람이 쉽게 오류를 저지를 수 있다는 것 쯤은 아시죠? 그와 반대로 이해하기 쉬운 포맷으로 많은 정보를 가지는 로그파일은 그보다 훨씬 강력한 것이 될 수 있습니다. 적당히 많은 수의 데이터를 가지고 있는 상황을 생각해보십시요. 이런 경우 어떤 것들이 유용한지를 찾는 것보다 어떤 것들이 유용하지 않은지를 찾아서 제거해버리는 것이 쉬운 때가 많습니다. 구조화된 데이터 저장방법을 사용하면 모든 데이이터에 필터링 가능한 값과 속성을 붙여 넣기만 하면 됩니다.

훌륭한 로그 전략을 구현할 수 있는 방법으로는 세가지가 있습니다.

1\. 적절한 파일 포맷, 알고리듬, 기술의 사용  
이것의 예는 프로그램의 아웃풋을 웹서버에 위치한 데이터베이스에 출력하는 것입니다. 이 방법을 SQL 조회와 웹기반 프론트 엔드와 결합시키면 매우 강력해집니다. 하지만 불행히도 이것을 설치 및 관리하는 것은 쉬운 일이 아닙니다.

2\. 자신만의 파일 포맷, 도구, 알고리듬의 사용  
필요할 법한 정보들을 모두 포함 및 포맷하는 파일포맷을 설계하는 것입니다. 그 후 주어진 파일을 읽어와 보고서를 작성할 수 있는 도구를 작성합니다. 이것은 여러분이 노력하는 만큼 강력해질 수 있습니다. 하지만 모든 것을 작성하고 유지해야 한다는 것이 역시 단점이죠.

3\. 표준 파일 포맷과 이미 존재하는 도구의 사용  
공개 파일포맷과 표준화된 도구를 사용하면 플랫폼이나 소프트웨어 개발자 또는 특정한 도구셋에 얽매힐 필요가 없습니다. 다른 사람들이 여러분에게 필요한 일을 미리 해놓았을 뿐만 아니라 누가 작성한 도구를 사용할 것인지를 결정할 기회도 있을 것입니다.  

## 왜 XML인가요? {#TOC-XML-}

XML(확장성 생성 언어)\[3\]은 현대 컴퓨터 분야에서 지난 수십년간 진보해온 유사기술(예, GML과 SGML\[4\])의 최신 버전으로 열린 표준(ISO가 승인함)입니다. XML을 관장하는 기관은 W3 협회\[5\]입니다.

최신 기술인 XML은 잠시의 유행이 되버리고 말 위험도 가지고 있지만 XML은 대부분의 신형 및 구형 플랫폼에서 잘 지원되고 있습니다. 또한 플랫폼간 XML지원에서 중요한 점은 많은 도구들이 공짜라는 점입니다!

만약 이 문서를 온라인 상에서 읽고 계시다면 현재 여러분이 사용하고 있는 프로그램(웹 브라우저)이 XML 데이터도 출력할 수 있을 가능성이 큽니다. XML 파일 자체의 계층적인 뷰와 커스텀 출력 스타일(XSLT)을 통한 뷰가 모두 지원될 것입니다. 또한 텍스트 편집기를 이용하여 실제파일(실행시에 소프트웨어가 생성하는 것 외에)을 모두 작성하는 것도 가능합니다. 물론 텍스트 편집기보다 괜찮은 도구들도 있지만 이것을 사용해야만 하는 것은 아닙니다. 저는 이 글을 쓰는 동안 비주얼 스튜디오 2002(현재 제가 사용하는 개발 IDE입니다)의 자체편집기를 이용하여 HTML과 XSL 파일을 작성하였습니다.

## 최소 요구사항 및 배경 {#TOC--2}

소프트웨어 로그작성에 XML 기술을 적용하는 방법을 본격적으로 소개하기 전에 여러분이 기본적으로 갖춰야할 배경지식들이 있습니다.

모든 기술들을 기초부터 설명하는 것은 이 글의 범위를 벗어납니다. 사실 여기서 언급하는 모든 기술들을 배우기는 매우 쉬우며, 어느정도 지적인 독자분들은 이 글로부터 모든 것을 배울수도 있을 것입니다.

여기서 언급하는 모든 기술들을 완전히 이해해야만 강력한 로그작성 도구를 만들수 있는 것은 아닙니다. 그다지 중요하지 않은 부분들도 있기 때문이죠. 예를 들어 우리는 DTD나 스키마를 이용한 검증과정을 완전히 제낄 것입니다. 물론 이것을 별로 달갑게 여기지 않다는 사람이 있다는 것도 알고 있지요. 하지만 이는 컴퓨터 프로그램이 소스 XML을 작성할 것이며, 코드가 제대로 작성되었다면 출력결과도 올바를 것이라는 점을 전제로 한 것입니다. 그러나 이 별도의 검증과정을 원하거나 XML을 좀더 깊이 파고 들고 싶으시다면 로그파일용 DTD를 스스로 작성해 보시라고 권해드리겠습니다.

짧게 요약해 다음과 같은 4가지 영역에 대한 이해가 필요합니다.

1\. **XML**  

*   기초 XML 태그 정의/규칙
*   DTD/스키마 검증은 제외할 것
*   \[3\]과 \[6\]에 나온 정보를 읽어볼 것

2\. **HTML**

*   기초 HTML 문서 구조
*   텍스트 포맷하기(진하게, 밑줄, 이탤릭, 폰트)
*   테이블 구성방법
*   \[7\]의 설명과 \[8\]의 튜토리얼을 읽어볼 것

3\. **XSLT**  

*   HTML을 출력하는 XSLT의 사용법과 기초구조
*   매개변수의 사용
*   조건분기문('choose'와 'if'문)
*   \[9\]의 설명과 \[10\]의 튜토리얼을 읽어볼 것

4\. **JavaScript**  

*   HTML에서 JavaScript를 실행하는 방법
*   기초 JavaScript 함수 정의와 변수선언
*   \[11\]의 정보와 \[12\]의 튜토리얼을 읽어볼 것

위의 내용들에 이미 도가 트신 독자분들은 다음 단락으로 곧바로 가셔도 됩니다. 하지만 그렇지 않으신 분들은 추천링크들을 읽어보실 것을 당부드립니다. 각 링크들은 이 글의 젤 아랫부분에 있습니다.

자 이제 이 글에서 사용할 핵심 기술들에 대해 언급했으니 몇가지 필요한 도구들에 대해 말씀드리겠습니다.

앞서 밝혔듯이 필수도구들은 모두 무료로 사용할 수 있습니다. 여러분에게 필요한 것은 사실 텍스트 편집기와 웹브라우저 뿐입니다. 이정도의 도구들은 이미 여러분들의 컴퓨터에 설치되어 있을 것입니다. 심지어는 개발도구패키지를 사용하실 수도 있습니다.

마이크로소프트의 비주얼 스튜디오 2002 및 그 이후 버전을 사용하시면 브라우저 컴포넌트(일반적으로 MSDN이 등장하는 공간) 안에서 HTML 페이지를 보실 수 있습니다. 그 페이지에는 HTML 소스코드를 보여주는 탭과, XML 소스코드를 보여주는 탭(레퍼런스용), 그리고 XSLT코드를 보여주는 탭이 딸려있을 것입니다. 이들 탭 중의 어느한 곳에서 코드를 변경하고 그 변경사항을 저장한뒤 원래의 브라우저창을 갱신(refresh)하는 방법이 저에게는 매우 적절했습니다.

이보다 간단한 도구세트로는 메모장(또는 유닉스/리눅스에서 그에 갈음하는 프로그램)과 파이어폭스\[13\] 같은 브라우저를 들 수 있습니다.

저는 이 기술을 개발하는 동안 다양한 브라우저간 호환문제 -- 모질라와 인터넷 익스플로러에서 동시에 실행가능하도록 코드를 작성하는 문제 -- 를 겪었습니다. 기똥찬 로그 시스템을 만드는 것은 매우 쉬운 일이나 다른 브라우저에서 실패하는 경우가 허다합니다. 이 글은 윈도우즈XP SP2 환경에 설치된 인터넷 익스플로러 6와 파이어폭스에서 테스트를 마쳤습니다. 한가지 작은 문제는 SP2에서 인터넷 익스플로러는 보안상의 이유로 매우 까탈스러운 행동을 보입니다. 따라서 인터넷 익스플로러가 여러분 자신의 파일을 보여주지 않는 경우도 있을 것입니다!!

## XML파일에 데이터 쓰기 {#TOC-XML-1}

자, 그만 각설하고 이 글의 몸통부분인 XML기반 로그파일을 작성하는 법을 알아봅시다.

제가 이 기술을 구현한 첫번째 방법은 C++에서 단일체(싱글톤) 클래스를 사용하는 것이었습니다. 이 단일체 클래스는 물론 로그기록들을 표현합니다. C++은 또한 C의 사전처리기 명령어라는 막강한 기능도 지원합니다. 이 기능은 로그파일 아웃풋 부분을 코딩하는 것을 매우 쉽게 만들어 줍니다.

그러나 XML 데이터는 단순히 텍스트 파일일 뿐이므로 현존하는 어떤 컴퓨터 언어에서도 XML 데이터를 작성할 수 있습니다. 유니코드/아스키 출력을 처리할 때는 약간 복잡해 질 수도 있지만 이는 지역화(로칼라이제이션)을 할 때나 아스키 테이블로 표현할 수 없는 언어로 로그파일을 출력할 때에만 문제되는 부분일 것입니다.

각 프로그래밍 언어마다 XML의 읽기/쓰기를 도와주는 다양한 라이브러리들이 존재합니다. 대부분의 자바 배포자들은 SAX/DOM(\[14\]와 \[15\]) 해석기(parser)와 작성기(writer)의 임플리멘테이션을 가지고 있습니다. 그리고 TinyXML 라이브러리\[16\]는 C++ 개발자에게 추천할만한 라이브러리입니다. 이런 라이브러리 중 하나를 사용하는 것은 매우 괜찮은 생각이나 반드시 그래야 하는 것은 아닙니다. 스트림(C++)과 Writer(자바)만 사용해도 ".xml" 텍스트 파일에 필요한 정보를 쓰는데 아무런 문제가 없기 때문입니다.

파일을 작성하는 C++ 메커니즘을 설명하기 전에 xml의 출력 포맷부터 살펴봅시다. 다음이 제 로그 시스템에서 사용하는 일반적인 구조입니다.

```cpp
<RunTimeLog>
 <LogHeader>
   …
 </LogHeader>
 <LogEvent>
   …
 </LogEvent>
</RunTimeLog>
```

즉, 최상위 RumTimeLog 노드가 모든 것을 포함하고, 그 아래에 헤더(LogHeader)가 있습니다. 헤더는 일반적인 세션정보나 설정정보를 저장합니다. 실제 로그정보는 여러 개의 LogEvent 요소들에 저장됩니다. 제 프로그램을 짧은 시간동안 "시험주행" 해봤을 때 약 1000개의 LogEvent를 얻었습니다. 따라서 일반적인 테스트 세션은 이보다 1000배 정도 많은 이벤트를 발생시킬 것입니다.

LogHeader의 존재의의는 로그파일을 분류하기 위한 일반적인 정보 및 세션/설정 정보를 포함하기 위해서입니다. 이를 통해 어떤 환경하에서 테스트를 실행했는지 기록할 수 있습니다.

```cpp
<LogHeader>
 <OutputLevel> … </OutputLevel>
 <Session>
   <Started>
     <Time> … </Time>
     <Date> … </Date>
   </Started>
   <Configuration>
     <Environment> … </Environment>
     <Processor>
       <Family> … </Family>
       <Vendor> … </Vendor>
       <Features> … </Features>
       <ClockSpeed> … </ClockSpeed>
     </Processor>
     <Memory>
       <Total> … </Total>
       <Available> … </Available>
     </Memory>
   </Configuration>
 </Session>
</LogHeader>
```

위의 코드에서 볼 수 있듯이 상당한 양의 정보들이 기록됩니다. 이 정보들은 나중에 로그파일을 HTML로 출력할 때 사용할 것입니다. 사실 여기에 채워넣을 수 있는 정보들은 플랫폼에 특정적입니다. 윈도우즈(win32)개발자들은 MSDN 라이브러리에서 상당한 양의 함수들을 찾을 수 있으실 것입니다.

이 정보들은 매우 유용합니다. 저의 경우에는 128mb 윈도우즈 노트북(아~구형이죠?)에서 프로그램을 실행하던 도중에 Memory/Available 필드를 통해 제 플랫폼의 문제를 찾아낼 수 있었답니다. 그 로그파일은 수많은 경고와 오류들도 가득차 있었는데 거의 동일한 다른 시스템에서는 이런 문제를 찾아볼 수 없었습니다. 하지만 환경에 대한 정보를 포함시키자 무엇이 문제를 야기하는지 빨리 찾아낼 수 있었습니다.

XML 출력구조의 다음 요소는 기록할만한 가치가 있는 이벤트가 발생할 때마다 정보를 저장합니다.

```cpp
<LogEvent id="…">
 <Type> … </Type>
 <TimeIndex> … </TimeIndex>
 <NameSpace> … </NameSpace>
 <File> … </File>
 <Function> … </Function>
 <LineNumber> … </LineNumber>
 <Message> … </Message>
</LogEvent>
```

이 특정요소의 설계는 여러분의 응용프로그램에 따라 달라질 것이지만 위의 구조가 대부분 사람들의 요구를 충족시켜줄 것입니다. 또한 실행시에 확인할 수 있는 정보의 종류에 따라서도 이 구조가 달라질 것입니다.

이런 구조로 태그들에 둘러싸인 정보들이 나중에 HTML 페이지에 그대로 삽입될 것입니다. XSLT는 수학적인 포맷팅 및 프로세싱 기능을 가지고 있지만 이는 여러분이 생각하는 것보다 훨씬 제한적입니다. 제가 이것을 알아챈 특정한 한 분야는 TimeIndex 필드였습니다. 내부적으로 제 엔진은 밀리초(1000분의 1초)의 정확도를 가진 클럭상에서 실행됩니다. 다시 말해 "1분 13초 228밀리초"는 실제 내부적으로 "73228"이란 말입니다. 이는 인간이 읽을 수 있는 로그파일 포맷을 만들려고 할 때 그리 좋은 방법이 아닙니다. 따라서 이런 내부적인 값들을 인간이 쉽게 읽을 수 있는 값으로 변환하는 것이 좋습니다.

이 데이터를 텍스트 파일로 작성하기 위해 다음과 같은 단순한 함수 하나를 작성할 수 있습니다.

```cpp
void WriteLogEntry(int iEntryType,
                  std::string strNameSpace,
                  std::string strSourceFile,
                  std::string strFunction,
                  int iSourceLine,
                  std::string strMessage );
```

그리고 매우 직관적인 std:ofstream 출력을 몇 개 사용합니다.

```cpp
ofsXMLDatabase << "<NameSpace>"
              << strNameSpace
              << "</NameSpace>"
              << std::endl;

ofsXMLDatabase << "<File>"
              << strSourceFile
              << "</File>"
              << std::endl;

ofsXMLDatabase.flush( );
```

마지막에 flush() 명령어가 있는 것에 주목해 주십시요. 이는 매우 중요하며, 자바 등의 다른 언어에도 이에 상응하는 명령어들이 있습니다. 최근에 쓴 아웃풋을 flush()하면 그 내용이 디스크에 쓰여지며 더이상 런타임/OS의 버퍼에 남아있지 않습니다. 만약 여러분의 프로그램이 많은 양의 크래시가 발생하기 전에 핵심적인 오류 메시지를 내보냈지만 실제로 디스크에 쓰여지지 않았다면 로그파일에서 어떤 명백한 에러메시지도 찾을 수 없을 것입니다. 이는 단순히 OS가 프로세스의 메모리를 다시 취득하기 때문에 여러분이 로그 항목을 작성하였던 버퍼가 실제 디스크에 쓰여지기 전에 제거되기 때문입니다.

다음 부분은 C/C++ 프로그래머에게 특히 의미가 있는 부분입니다. 다른 언어에서도 이 "마법"을 찾을 수 있지만 특히 C/C++에서 널리 사용되는 내용입니다. 그 내용이 무엇이냐 하면 C언어가 제공하는 전처리기 지시어를 사용하는 것입니다. 혹자들은 이것이 코드를 복잡하게 만들고 버그를 찾기 어렵게 만드는 요소라고 혹평합니다. 하지만 저희의 경우에 이를 사용하면 로그파일 출력의 양을 제어할 수 있을 뿐만 아니라 프로그래머가 귀중한 정보들을 로그에 쓰기 위해 행해야 하는 일을 간단하게 만들어 주기도 합니다.

저희의 소프트웨어 라이브러리에는 MasterInclude.h 헤더 파일이 있습니다. 이 파일은 로그파일과 관련된 매크로와 기타 다른 유틸리티 매크로 및 정의들을 포함합니다. 이 헤더파일은 특히 최종 제품을 구성하는 모든 번역 유닛의 젤 윗부분에 포함됩니다. 따라서 몇가지 시간을 절약할 수 있는 장치들이 여기에 있습니다...

```cpp
//기본 사전컴파일 정의
 //0 - 디버깅이 필요없음
 //1 - 매우 기본적인 디버깅 아웃풋
 //2 - 자세한 디버그 아웃풋
 //3 - 모든 아웃풋, 그리고 모든 메시지의 사본
#ifdef _DEBUG
 //'DEBUG' 빌드용 아웃풋 정도를 설정
 #define SYSTEM_DEBUG_LEVEL 2
#else
 //'RELEASE' 빌드용 아웃풋 정도를 설정
 #define SYSTEM_DEBUG_LEVEL 1
#endif
```

이것들을 미리 정의해놓으면 프로젝트의 어느곳에서라도 추가적인 디버그 출력을 더하고 싶을 때마다 다음과 같이 할 수 있습니다.

```cpp
#if SYSTEM_DEBUG_LEVEL >= 2
 //기타 디버그 검증을 여기서 행한다.
#endif
```

이것의 보너스는 프로그램을 평소처럼 실행할 때 레벨을 1로 줄여 핵심 이벤트와 상태만을 묘사하는 150개 정도의 항목과 주요 에러만을 담은 로그파일을 볼 수 있다는 것입니다. 보통 실행상태에서는 대부분의 일들이 잘 진행되기 때문에 이 레벨을 낮출 수 있습니다. 만약 새로운 구성요소를 시험하거나, 문제점이 발견되었다면 출력레벨을 2나 3으로 높여 소프트웨어가 오류/버그 전에 했던 모든 것들을 일일이 살펴볼 수 도 있습니다. 물론 그 특정 구성요소를 재컴파일해야하지만 이것이 저희 팀에게는 큰 문제가 아니였습니다.

제가 C 매크로 언어를 사용하여 부린 다음 재주는 컴파일러가 보여주는 시스템 정의 매크로 값들을 몇몇 사용한 것입니다. 특히 \_\_LINE\_\_과 \_\_FILE\_\_이 그것들입니다. 마이크로소프트의 컴파일러는 또한 \_\_FUNCTION\_\_이란 매크로를 노출하며, 이는 매우 유용합니다. 저는 또한 각 번역 유닛에 대해 \_\_NAMESPACE\_\_ 값도 정의하였습니다.

잘 살펴보신다면 이 매크로들이 저희가 사용하는 XML의 LogEvent 요소에 있는 다양한 필드에 대응하는 값들만 아니라 WriteLogEntry() 함수 원형의 여러 매개변수들을 정의한다는 사실을 알 수 있을 것입니다. 만약 컴파일러가 이 정보들을 만들어 내도록 만들 수 있다면 모든 것이 보다 나을 것입니다! 따라서 저는 다음의 매크로를 정의합니다.

```cpp
#define Log_Write_L1( linetype, linetext ) \
 CLogFile::GetSingletonPtr()->WriteLogEntry( \
     (linetype), \
     __NAMESPACE__, \
     __FILE__, \
     __FUNCTION__, \
     __LINE__, \
     (linetext) )
```

개발과정을 더욱 편하게 만들기 위해 유효한 linetype들을 CLogFile의 상수 구성원들로 포함시켰습니다. 전처리기를 사용하는 마지막 트릭은 다음과 같습니다.

```cpp
#if SYSTEM_DEBUG_LEVEL == 3
 //모든 매크로를 킨다
 #define Log_Write_L1( linetype, linetext ) …
 #define Log_Write_L2( linetype, linetext ) …
 #define Log_Write_L3( linetype, linetext ) …
     
#elif SYSTEM_DEBUG_LEVEL == 2
 //레벨 1..2의 매크로를 킨다
 #define Log_Write_L1( linetype, linetext ) …
 #define Log_Write_L2( linetype, linetext ) …
 #define Log_Write_L3( linetype, linetext )
     
#elif SYSTEM_DEBUG_LEVEL == 1
 //레벨 1 매크로를 킨다
 #define Log_Write_L1( linetype, linetext ) …
 #define Log_Write_L2( linetype, linetext )
 #define Log_Write_L3( linetype, linetext )
     
#else
 //매크로를 끈다
 #define Log_Write_L1( linetype, linetext )
 #define Log_Write_L2( linetype, linetext )
 #define Log_Write_L3( linetype, linetext )
     
#endif
```

위의 코드에서 "…" 부분은 위에서 언급한 전체 확장입니다(그러나 약간 다른 접미어를 사용함). 지면을 절약하기 위해 위의 코드에서 이것을 생략하였습니다. 다양한 SYSTEM\_DEBUG\_LEVEL의 아래에 몇몇 매크로들이 실제로 아무것도 확장하고 있지 않다는 것에 주목해주십시요. 즉 이 장소에는 어떤 코드도 삽입되지 않을 것입니다. 이것은 일부로 그런 것입니다. 여러 문자열을 파일에 쓰는 것은 느린 과정입니다(제 플랫폼에서 레벨 3는 레벨 0에 비해 약 40% 정도의 속도를 보였습니다). 따라서 로그 항목들을 실제 원하지 않을 때는 그 항목들이 이 빌드에는 존재하지 않습니다.

실제로 코드를 개발할 떄 프로그래머는 다음과 같은 코드를 작성할 수 있습니다.

Log\_Write\_L3( CLogFile::DEBUG, "함수에 들어옴" );

컴파일러는 함수이름과 위치에 대한 모든 자세한 정보들을 채워줄 것입니다. 프로그래머가 할 일을 줄인다는 것은 종종 실행시에 소프트웨어의 흐름 및 상태를 문서로 남기는 일을 제대로 하지 않을 가능성이 줄어든 다는 것을 의미합니다.

다른 언어에서 개발을 하고 있다면 이와 비슷한 라인을 도입할 방법들이 있습니다. 상수나 정적(static) 불리언 값을 정의하면 컴파일러 스스로가 적절한 경우에 그것들을 "죽은 코드"로 간주하여 제거해줄 것입니다.

## XSLT를 사용하여 XML 이쁘게 꾸미기 {#XSLT를-사용하여-XML-이쁘게-꾸미기}

여기까지 글을 읽으셨다면 여러분의 응용프로그램을 테스트하는 도중 XML 파일을 출력할 수 있으실 것입니다. 그리고 그 .xml 파일의 크기는 적당히 클 것입니다. 다음이 마이크로소프트의 인터넷 익스플로러에서 본 xml파일입니다(물론 모질라 기반의 브라우저에서 비슷한 결과를 볼 수 있을 것입니다).

http://images.gamedev.net/features/programming/xmltech/image001.jpg

인터넷 익스플로러는 문서를 구성하는 다양한 종류의 문법들을 다른 색으로 표현해줍니다. 이것이 하는 가장 유용한 것은 xml 문서를 진정한 계층적 형태로 보여준다는 것입니다. 즉, 부모 요소의 옆에 있는 "+"와 "-"표시를 사용하여 요소들을 확장 및 축소할 수 있습니다.

http://images.gamedev.net/features/programming/xmltech/image002.jpg

제가 이 글에서 앞서 논한 구조가 제 개발 시스템에 관한 정보로 채워졌다는 사실을 아시는 분이 계실지도 모르겠습니다. 이 정보는 Win32 API를 통해 쉽게 얻을 수 있습니다.

위의 계층적(그리고 읽기 전용) 아웃풋은 매우 유용합니다. 이것을 매우 쉽게 탐색할 수 있으니 말입니다. 하지만 약간 느린게 흠이라고 할까요?

그러나 XSLT의 뛰어난 기능을 사용하면 훨씬 훌륭한 아웃풋을 만들수 있답니다. XML문서에 XSLT 파일을 사용하면 주어진 (문법에 맞게 제대로 구성된) XML 로그파일을 미리 결정된 HTML 아웃풋으로 포맷할 수 있습니다.

XSLT를 사용하여 XML 문서를 다른 포맷과 파일종류로 변형시킬 수도 있습니다. 이는 특히 여러분의 모든 아웃풋을 XML로 출력한 뒤에 몇년 후 이를 더 크고 보다 나은 포맷으로 변환하고 싶다면 특히 유용해질 것입니다.

XSLT는 매우 강력한 형식(formatting) 언어이고, 비교적 간단한 스크립트로부터 매우 복잡한 표현물을 만들기 위해 사용될 수 있습니다. 이 글에서는 이를 사용하여 주어진 LogEvent의 내용을 여러가지 색상으로 표현한 표 형태의 아웃풋을 생성할 것입니다.

브라우저가 XSLT 템플릿 매칭을 사용하면 이 문서에 포함된 몇 개의 LogEvent라도 반복하여 처리할 수 있습니다. 이 스타일시트는 한 인스턴스용 형식만 지정해주면 됩니다.

이 글의 앞에서 논한 XML 포맷은 LogHeader와 그 뒤를 따르는 LogEvent들로 구성되어 있습니다. XSLT도 또한 관련 태그에 저장된 매개변수들을 추출하고 그들을 읽기 편한 형태로 삽입하는 방법으로 포맷된 버전의 헤더를 만들 수 있습니다.

다음 스크린샷은 인터넷 익스플로러가 이것을 렌더링하는 모습입니다.

http://images.gamedev.net/features/programming/xmltech/image003.jpg

엷은 회색의 이탤릭체 텍스트는 소프트웨어가 출력한 비 가공 XML으로부터 읽은 것입니다. 이것에 대한 유일한 예외는 마지막 라인인 "Total logged events: 100" 뿐입니다. 이 라인은 XSLT가 파일안에 존재하는 LogEvent 수를 센 것입니다.

다음 코드조각은 LogHeader를 처리하는 XSLT입니다.

```cpp
<xsl:template match="LogHeader">

 <br/>
 <b>
   <font face="Arial" size="3" color="#000000">
     Log file header information:
   </font>
  </b>
  <br/>
  <font face="Arial" size="2" color="#000000">
    Output level:
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="OutputLevel"/>
    </font>
  </i>
  <br/>
  <font face="Arial" size="2" color="#000000">
    Session started at
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="Session/Started/Time"/>
    </font>
  </i>
  <font face="Arial" size="2" color="#000000">
    on
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="Session/Started/Date"/>
    </font>
  </i>
  <br/>
  <font face="Arial" size="2" color="#000000">
    Operating environment:
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="Session/Configuration/Environment"/>
    </font>
  </i>
  <br/>
  <font face="Arial" size="2" color="#000000">
    System processor:
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="Session/Configuration/Processor/Family"/>
    </font>
  </i>
  <font face="Arial" size="2" color="#000000">
    running at approximately
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="Session/Configuration/Processor/ClockSpeed"/>
    </font>
  </i>
  <font face="Arial" size="2" color="#000000">
    Mhz
  </font>
  <br/>
  <font face="Arial" size="2" color="#000000">
    Memory on start-up:
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="Session/Configuration/Memory/Available"/>
    </font>
  </i>
  <font face="Arial" size="2" color="#000000">
    available from
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:value-of select="Session/Configuration/Memory/Total"/>
    </font>
  </i>
  <font face="Arial" size="2" color="#000000">
    installed
  </font>
  <br/>
  <font face="Arial" size="2" color="#000000">
    Total logged events:
  </font>
  <i>
    <font face="Arial" size="2" color="#808080">
      <xsl:copy-of select="count(../LogEvent)"/>
    </font>
  </i>

</xsl:template>
```

위의 XSLT에는 입력값들을 적절히 포매팅하는 것 외에는 특별한 내용이 없습니다. 전에도 말했듯이 마지막 줄은 실행시에 생성되며, 위 XSLT의 마지막 부분에 있는 "count(../LogEvent)"문이 바로 그런일을 합니다. 이것은 이제부터 제가 묘사하려고 하는 템플릿 매치(template match)를 실제 실행합니다. 따라서 LogEvent 템플릿 매치로부터 아웃풋을 보장하지 않는 필터들은 count()에 포함되지 않을 것입니다.

다음으로 흥미로운 부분은 각 LogEvent를 포맷하는 것입니다. 이 초기 루트 템플릿 매치는 일반 HTML을 사용하여 표의 윤곽을 잡습니다.

```cpp
<table border="1" width="100%" cellspacing="0" cellpadding="0"
   bordercolorlight="#000000" bordercolordark="#ffffff" bordercolor="#000000">
 <tr>
   <td width="3%"  bgcolor="#000000">
     <font size="2" face="Arial" color="#FFFFFF">
       <b>
         <center>#</center>
       </b>
     </font>
   </td>
   <td width="20%" bgcolor="#000000">
     <font size="2" face="Arial" color="#FFFFFF">
       <b>
         <center>Time</center>
       </b>
     </font>
   </td>
   <td width="23%" bgcolor="#000000">
     <font size="2" face="Arial" color="#FFFFFF">
       <b>
         <center>File</center>
       </b>
     </font>
   </td>
   <td width="50%" bgcolor="#000000">
     <font size="2" face="Arial" color="#FFFFFF">
       <b>
         <center>Function</center>
       </b>
     </font>
   </td>
   <td width="4%"  bgcolor="#000000">
     <font size="2" face="Arial" color="#FFFFFF">
       <b>
         <center>Line</center>
       </b>
     </font>
   </td>
 </tr>
 <xsl:apply-templates select="RunTimeLog/LogEvent"/>
</table>
```

이것은 이벤트 번호, 시간 인덱스, 소스코드 파일, 함수, 줄번호의 다섯 개의 열을 갖는 표입니다. 일단 표의 첫번째 행이 생성되면 xsl:apply-templates 호출을 통해 LogEvent의 처리가 시작됩니다. 이는 각 LogEvent 템플릿 매치들이 표에 2줄씩 추가되도록 설계되었습니다. 왜 두줄이냐고요? 잠시 뒤에 설명드리겠습니다. 로그파일에 이벤트가 전혀없거나 현재 필터와 일치하는 이벤트가 없다면(이 상황은 나중에 살펴봅니다) 표에는 헤더만 있고 거기에 들어가는 항목은 없을 것입니다.

LogEvent 요소들을 포맷하는 XSLT 코드는 매우 길고 반복적입니다. 따라서 여기서 중요한 부분만을 강조하겠습니다. 다른 부분에 관심이 있으신 독자분들은 나중에 스스로 이 코드를 스스로 살펴보시길 바랍니다.

LogEvent 템플릿은 기본적으로 2개의 행을 표에 추가합니다. 첫번째 행은 실제 이벤트의 자세한 내용이고 두번째 행은 메시지입니다. 약간의 실험과정을 통해 저는 이것이 가장 유용한 결과를 제공한다는 사실을 발견했습니다. 즉, 첨부된 "통계" 데이터는 비교적 일정한 크기인 반면, 로그 메시지는 두어개의 단어부터 긴 문장에 이르기까지 그 길이기 다양해질 수 있기 때문입니다. 표의 한 셀에 담을 데이터의 길이가 가변적이라면 HTML 표의 정확한 치수를 추정하는 일은 약간 까다롭습니다.

LogEvent 템플릿의 기본구조는 다음과 같습니다.

```cpp
<xsl:template match="LogEvent">

 <xsl:choose>

   <xsl:when test="Type='Comment'">
     <tr bgcolor="#80FF80" valign="middle" align="center">

     </tr>
   </xsl:when>

   <xsl:when test="Type='Unknown'">
     <tr bgcolor="#EEEEEE" valign="middle" align="center">

     </tr>
   </xsl:when>

   <xsl:when test="Type='Error'">
     <tr bgcolor="#FF8080" valign="middle" align="center">

     </tr>
   </xsl:when>

   <xsl:when test="Type='Warning'">
     <tr bgcolor="#FFAA80" valign="middle" align="center">
     </tr>
   </xsl:when>
     <xsl:when test="Type='Event'">
     <tr bgcolor="#8080FF" valign="middle" align="center">

     </tr>
   </xsl:when>

   <xsl:when test="Type='Debug'">
     <tr bgcolor="#FFFF80" valign="middle" align="center">

     </tr>
   </xsl:when>

   <xsl:when test="Type='Game Message'">
     <tr bgcolor="#FF8020" valign="middle" align="center">

     </tr>
   </xsl:when>

 </xsl:choose>

</xsl:template>
```

기본적으로 우리는 주어진 행의 색상을 LogEvent/Type 분류에 따라 변화시키는 xsl:choose 구조를 가지고 있습니다. 이 분류에 따른 색상처리는 로그파일에서 많은 양의 데이터를 출력할 때 매우 괜찮은 방법입니다.

주어진 요소(다음 예에서 "comment")에서 사용되는 XSLT/HTML코드의 예는 다음과 같습니다.

```cpp
<tr bgcolor="#80FF80" valign="middle" align="center">

 <td>
   <font size="2" face="Arial" color="#202020">
     <center>
       <xsl:value-of select="@id"/>
     </center>
   </font>
 </td>

 <xsl:apply-templates select="TimeIndex"/>
 <xsl:apply-templates select="File"/>
 <xsl:apply-templates select="Function"/>
 <xsl:apply-templates select="LineNumber"/>

</tr>
<tr bgcolor="#AAFFAA">
 <xsl:apply-templates select="Message"/>
</tr>
```

LogEvent의 개별적인 요소들의 출력은 동일합니다. 따라서 각각에 대해 템플릿 매치를 사용했습니다. XSLT의 이 기능은 반복작업을 줄여주고 하나의 변경만으로도 템플릿 전체에 영향을 미치게 해줄 수 있게 해주므로 매우 유용한 존재입니다. 첫번째 행은 메시지의 종류에 특유한 색상과 더불어 디폴트 템플릿 매치의 헤더에서 지정한 5개 요소를 포함합니다. 두번째 행은 전체표의 크기를 증가시키며 윗 행보다 약간 옅은 색을 띕니다.

지금 여기 보이고 있는 개별 필드용 템플릿 매치는 동일하게 보이고, 다음의 예를 따릅니다.

```cpp
<xsl:template match="TimeIndex">
 <td>
   <font size="2" face="Courier New" color="#404040">
     <center>
       <xsl:apply-templates/>
     </center>
   </font>
 </td>
</xsl:template>
```

http://images.gamedev.net/features/programming/xmltech/image004.jpg

위의 스크린샷에서 처음 여섯 항목을 볼 수 있을 것입니다. 표의 젤 윗부분에 위치하는 헤더는 검은색 배경에 흰색 글자로 표시되어 있습니다. 실제 로그 이벤트들은 앞에서 설명하였듯이 각 이벤트당 두 행으로 표현합니다. 이 문서에서 보여주는 색상들은 아웃풋 종류의 3가지인 Debug(노랑), Event(파랑), Comment(녹색)을 보여줍니다. 이전의 레이아웃에서 보았듯이 다른 색상들도 사용됩니다.

\[17\]을 클릭해보신다면 전체 XSLT 문서를 볼 수 있을 것입니다. \[18\]을 클릭하시면 이 XSLT에 의해 처리된 전체 XML문서의 결과를 보실 수 있습니다.

## JavaScript를 사용하여 XSLT 향상시키기 {#JavaScript를-사용하여-XSLT-향상시키기}

윗 문단에서는 로그파일을 꾸미는데 사용되는 비교적 간단힌 XSLT의 힘을 보셨습니다. 그러나 몇몇 기초 자바스크립트를 사용하면 보다 나은 기능들을 제공할 수 있습니다.

수천개의 이벤트로 구성된 런타임 로그에서 하나나 그 이상의 유닛에 대한 특정한 정보를 찾으려고 할 때나 특정한 종류의 메시지에 관심이 있다면 문제점이 야기됩니다. 현재 형태에서 이 기술은 여러분(분석가)이 동시에 모든 항목들을 뒤져 원하는 것을 찾도록 요구합니다. 이는 비교적 작은 로그 파일에서는 괜찮은 방법이나 매우 빠르기 힘들고 지루해질 것입니다. 에러를 보다 빈번하게 만드는 것은 말할것도 없지요.

XSLT는 요소들을 조건적으로 처리할 수 있는 훌륭한 구조를 제공할 뿐 아니라, 매개변수를 선언할 수 있는 능력도 가지고 있습니다. 이 두 기능들을 합치면 로그파일로부터 LogEvent들을 필터링 하여 전술했던 거대한 문서를 뒤지는 일을 해결할 수 있습니다. 매개변수를 사용하여 이벤트 종류(예: "Debug"와 "Event"), 네임스페이스 그룹 등을 지정하면 그 범주에 해당하는 메시지들만을 로그파일로부터 뽑아낼 수 있습니다.

```cpp
<!-- **********************************************************************************    -->
<!-- 소스코드에서 메시지가 등장하는 일반적인 위치에 따라 필터링을 하기 위해 "namespace"        -->
<!-- 매개변수를 사용한다.                                    -->
<!--                                            -->
<!-- 다음의 특수한 값들이 정의된다:                                -->
<!-- *****************************************                        -->
<!-- 'any'          : 네임스페이스 필터링을 끈다.                        -->
<!--                                            -->
<!-- 다음은 현재 사용중인 네임스페이스 이름들이다:                        -->
<!-- *****************************************************                -->
<!-- default        : 루트(이름 없음) C++ 네임스페이스가 출처임                -->
<!-- Engines        : 코어/기반 엔진코드를 구성하는 파일                    -->
<!-- Game           : 실제 게임을 만드는 파일                        -->
<!-- UI             : 사용자 인터페이스(대부분 컨트롤)을 구성하는 파일            -->
<!-- Vars           : 코드의 데이터저장 구성요소                        -->
<!-- **********************************************************************************    -->
 <xsl:param name="nameSpace" select="'any'"/>
```

위의 코드는 XSLT 문서의 다음 수정판에서 가져온 것으로 실제 매개변수 선언에 허용되는 값들을 나타내는 주석입니다. 이 특정 매개변수에 지정된 값은 나중에 이 문서안에서 조건적으로 요소들을 선택하는데 사용할 수 있습니다. nameSpace 매개변수는 조건선택문에서 다음과 같이 사용합니다.

```cpp
<xsl:if test="$nameSpace='any' or $nameSpace=NameSpace">

</xsl:if>
```

만약 LogEvent/NameSpace 필드의 내용이 nameSpace매개변수에서 지정한 것과 일치하거나 'any' 값이 설정되었다면 xsl:if 블럭의 내용이 고려됩니다. xsl:if 블럭의 내용은 앞 섹션에서 사용했던 XSLT와 정확히 일치합니다. 몇개의 매개변수와 적절한 조건을 갖춘 XSLT는 따라가기가 매우 어려워질 수 있습니다. 비슷한 시스템을 설계할 때 이점을 반드시 기억하도록 합시다.

방금 소개한 xsl:params에는 작은 문제가 하나 있습니다. 그들이 실제 XSLT 파일안에 써진다는 것입니다. 매개변수들을 사용하여 아웃풋을 필터링 할 수 있지만 필터링이 작동하는 방법을 변경하고 싶다면 직접 그 매개변수를 편집하고 렌더링된 아웃풋을 리프레시(refresh) 해야만 합니다. 당연히 이것이 편한 방법이라고는 할 순 없겠지요. 그렇지만 해결책은 그리 멀지 않은 곳에 있습니다. JavaScript를 사용하면 XML의 렌더링(XSLT를 통한)을 제어할 수도 있고, 매개변수의 값을 동적으로 선택할 수도 있습니다. XSLT에 저장된 텍스트를 직접 변경하지 않고도 말입니다!

자바스크립트를 사용하기 위해서는 로그파일이 구축되는 일반적인 구조를 변경해야합니다. 여태까지 저희는 .xml파일의 형태로 저장된 소스데이터와 .xsl파일로 저장된 변경데이터만을 사용해왔습니다. 이 두 포맷중 어떤것도 직접적으로 자바스크립트 코드를 사용할 수 있게 허용하지 않습니다. 따라서 저희는 모든것을 한 HTML 파일안에 넣어야 합니다. HTML은 JavaScript를 포함시킬 때 사용할 수 있는 스크립트 태그를 제공하며, 저희가 생성된 로그파일의 동적인 필터링을 더하기 시작할 부분도 바로 이 태그입니다.

다음의 코드는 새로운 래퍼 문서의 기초 구조를 보여줍니다.

```cpp
<html>
 <head>
   <TITLE>
     Log File Viewer
   </TITLE>
   <script language="javascript">

   </script>
 </head>
 <body bgcolor="#FFFFFF" vlink="#000000" alink="#000000"
     link="#000000" onload="javascript:onViewLog( ); javascript:generateLink( );">
   <font size="3" face="Arial" color="#2060AA">
     <b>
       <u>
         Configure the current log-file view:
       </u>
     </b>
   </font>

   …

   <hr>

 </body>
</html>
```

스크립트 블럭은 HTML의 head안에 존재하며, HTML의 표준 body는 다양한 커스텀 필터들을 묘사합니다(이에 대해서는 나중에 설명하죠!). 마지막 부분은 자바스크립트가 산출한 내용을 담는 div태그입니다. 자바스크립트는 표준 HTML 하이퍼링크를 통해 쉽게 호출할 수 있습니다(또한 여는 body 태그에서 "onload" 애트리뷰트를 사용하는 것이 보이죠?). 그럼으로 인해 뷰어가 원하는 설정을 비교적 쉽게 선택할 수 있는 방법을 제공합니다. 이제 자바스크립트는 요청된 속성들을 사용하여 앞에 논했던 xsl:params을 설정하고, 그 문서를 div 타겟안에 로딩합니다.

자바스크립트의 일반적인 설계, 그리고 그것의 인터페이스가 되는 HTML은 산출된 XML 아웃풋 상에서 동작하는 여러 핕러들을 허용합니다. 가장 간단한 임플리멘테이션은 하이퍼 링크를 통해 호출될 때, div 타겟안에 XML 아웃풋을 산출하는 하나의 자바스크립트 메서드를 가지는 것입니다. 그러나 이는 그리 많은 커스텀화 가능성을 제공하지 않습니다. 만약 하이퍼링크로부터 호출된 자바스크립트 메서드가 처음에 필터링에 필요한 매개변수들을 설정한다면 우리는(최소한 원칙상) 어떤 수의 필터라도 설정할 수 있습니다.

```cpp
var g_eventType    = 'all';  //'eventType' xsl:param
var g_nameSpace    = 'any';  //'nameSpace' xsl:param
var g_specificFile = '';     //'specificFile' xsl:param

function setEventType( eventType )
 {

   g_eventType = eventType;
   generateLink( );

 }

function setNameSpace( nameSpace )
 {

   g_nameSpace = nameSpace;
   generateLink( );

 }

function setSpecificFile( fileName )
 {

   g_specificFile = fileName;
   if( g_specificFile == '' )
   {
     currentAdvFilter.innerHTML = "특정 파일명에 기초하여 필터링을 하지 않음.";
   }
   else
   {
     currentAdvFilter.innerHTML = "다음 파일에 있는 엔트리만을 보입니다. '<b>" +
                                    g_specificFile + "</b>'."
   }
   generateLink( );

 }
```

스크립트 태그의 바디(body)에 있는 위의 코드조각은 복수 필터의 개념을 보여줍니다. 이 스크립트는 전역적으로 3개의 변수(g\_eventType, g\_nameSpace, g\_specificFile)을 정의하며 이 변수들은 그에 해당하는 set\*() 메서드 호출로 설정할 수 있습니다.

HTML으로 포맷된 텍스트들이 setSpecificFile안에서 currentAdvFilter.innerHTML에 대입되는 모습을 보실 수 있을 것입니다. 원래의 아웃라인에는 안나와있지만 currentAdvFilter는 바디안에 있는 div 태그들 중 하나의 id속성입니다. 이 메카니즘을 사용하면 우리의 HTML 코드를 렌더링된 아웃풋속으로 div 태그의 위치로서 삽입할 수 있습니다. 이것의 보다 복잡한 예는 또한 위의 코드조각에서 볼 수 있는 generateLink() 메서드입니다. 사용자를 돕기 위하여, 필터 속성이 변경될 때, 디스플레이(div 태그를 통해)가 그에 따라 업데이트되어 현재 조합이 무엇인지 볼 수 있도록 합니다.

```cpp
function generateLink( )
 {

   var linkText = "<br>";

   linkText = linkText + "<a href=\"javascript:onViewLog( );\">";

   linkText = linkText + "<font size=\"2\" face=\"Arial\" color=\"#2060AA\">";
      
   linkText = linkText + "<i>";
            
   linkText = linkText + "Click here to generate a report showing events of type '<b>" +
                g_eventType + "</b>' ";

   if( g_specificFile == '' )
   {
     linkText = linkText + "from <b>all files</b> in the code namespace '<b>" +
                  g_nameSpace + "</b>'.";
   }
   else
   {
     linkText = linkText + "from <b>'";
     linkText = linkText + g_specificFile;
     linkText = linkText + "'</b> in the code namespace '<b>" + g_nameSpace + "</b>'.";
   }

   linkText = linkText + "</i>";

   linkText = linkText + "</font>";

   linkText = linkText + "</a>";

   linkText = linkText + "<br>";

   linkText = linkText + "<br>";

   //컴파일된 문자열을 디스플레이에 맡긴다.
   eventTypeLabel.innerHTML = linkText;

 }
```

위의 방법은 매개변수 3개를 현재상태로 모두 취하며, HTML 하이퍼링크를 메인 디스플레이에 삽입합니다. 이 모든것의 핵심은 필터가 하는 일의 실제 텍스트 요약이 또한 XML을 처리하기 위해 클릭하는 하이퍼링크란 것입니다. 그 예로 다음과 같은 것이 있습니다.

```cpp
Click here to generate a report showing events of type 'Warning' from all files in the code namespace 'Engines'.
```

JavaScript/XML의 실제 "백엔드(back end)"는 극도로 간단하기 때문에(변수 설정과 현재 값을 출력할 뿐입니다), 여러분이 원하는 방법으로 인터페이스를 구현할 수도 있습니다. 다양한 이벤트 종류에 따라 아이콘 및 이미지들을 그려놓아 사용자 친화적인 그래픽 인터페이스를 만드는 것도 좋은 방법일 것입니다. 그러나 이보다 강력한 방법은 HTML 서식 태그(그리고 그에 관련한 요소들)를 이용하는 것입니다.

서식을 사용하면 복잡한 GUI 스타일의 사용자 인터페이스를 만들 수 있습니다. 결과를 저장할 JavaScript코드를 사용할 수만 있다면 이를 통해 똑똑한 필터링 조합을 구현할 수 있을 것입니다. 저 스스로도 이런 방법을 시도해보았으나 여기서 보이는 데모의 디자인을 심플하게 유지하기 위해 그 방법을 사용하지 않기로 결정했습니다. 실제 렌더링(그리고 이것의 컨트롤)은 브라우저와 운영체제에 따라 매우 다양해질 수 있으며, 미적감각이 살아있는 로그파일 필터는 효율성 면에서 그리 좋지 못한듯 싶습니다. 이는 필터링 가능성을 제한하지만 이 중 일부를 사용할 수도 있습니다. 예를 들어 저는 특정 파일을 검색하는 기능을 포함시켰습니다. 다양한 JavaScript 검출 루틴과 CSS를 사용하면 이런 문제점들을 해결할 수 있습니다만 다시 한번 말하건데 여기서 보여드리는 데모는 심플하게 유지하고 싶었습니다. 여러분 스스로 이런 것들을 직접 추가시켜 보십시오.

http://images.gamedev.net/features/programming/xmltech/image005.jpg

인터넷 익스플로러 6에서 캡춰한 위의 이미지에서 이 글의 예제코드에 의해 정의된 HTML 헤더를 볼 수 있으실 것입니다. 미적으로 즐거운 컨트롤들을 만드느라 약간의 시간을 보냈습니다. 위의 디스플레이는 언제나 렌더링 된 페이지의 젤 위에 등장하며, 이미지의 아래쪽 부분에 보이는 수평선은 산출된 출력과(아래쪽)과 컨트롤(위쪽) 사이에 경계선을 하나 긋습니다. 표의 첫째 행("종류")에 쓰인 배경색은 그에 상응하는 항목이 페이지의 아래쪽에 출력될 때의 배경색과 정확히 일치 합니다. 이는 사용자가 원하는 종류의 이벤트를 찾아보는 것을 쉽게 만들어 줄 뿐만 아니라 결과 표에서 항목들을 식별하기가 어려울때 간단한 해결방법으로 쓰이기도 합니다.

여기까지 저는 script 태그 바디에 정의한 JavaScript와 HTML을 사용한 기본 적인 인터페이스 처리를 설명했습니다. 하지만 매우 중요한 부분을 빠드렸습니다. 그것은 바로 필터링된 XML을 출력하는 부분입니다!

앞서 설명한 바와 마찬가지로 XSLT는 xsl:param 표기(그리고 적절한 조건문들)를 사용하여 커스텀화 할 수 있습니다. 그러나 가공되지 않은 XSLT안에서 이 값들은 실제 파일로 고정되어 있었습니다. JavaScript를 사용하면 주어진 브라우저에 맞게 다양한 XML 파서/렌더링 컴포넌트들을 생성하고 적절하게 설정할 수 있습니다. 여기에는 내부 xsl:param 필드를 설정하는 것도 포함됩니다. 이 조각들을 하나로 뭉치면 이제 연결조각을 가지고 있습니다. HTML과 JavaScript를 사용하여 컨트롤들을 만들어 낸다면 변수로 저장된 실제 필터들과 JavaScript를 사용해 XML과 XSLT를 읽어들이고 해당 xsl:param에 저장되어 있던 값들을 사용자가 HTML 인터페이스를 통해 선택한 것으로 바꿀 수 있습니다.

불행이도 여기에는 한가지 문제점이 있습니다. HTML을 다루는 실제 코드가 브라우저에 따라 달라진다는 것입니다. 인터넷 익스플로러 또는 모질라 두개로 브라우저를 크게 나눠볼 수 있으며, 이 둘중의 하나가 현재 널리 쓰이는 모든 브라우저/플랫폼 조합을 대변합니다. 각 플랫폼 용으로 필요한 코드의 양은 매우 적고, 실행도중에 쉽게 선택할 수 있습니다. 따라서 큰 문제는 아닙니다.

```cpp
function onViewLog( )
 {

   var moz = (typeof document.implementation != 'undefined') &&
             (typeof document.implementation.createDocument != 'undefined');

   if (moz)
   {
     onViewLog_Mozilla();
   }
   else
   {
     onViewLog_IE();
   }

 }
```

위의 코드는 모질라/인터넷익스플로러를 실행도중에 구분 할 수 있는 방법을 보여줍니다. 진입점인 onViewLog()가 플랫폼에 맞는 함수를 찾아내는 일을 도맡습니다. 이 외에 다른 플랫폼 및 브라우저를 사용하시는 분들은 이 코드를 고치시거나 별도의 조건 분기문을 추가하셔야 할 수도 있을 것입니다.

어느 플랫폼이건 간에 그 결과 코드는 다음과 같은 4가지 단계의 일을 합니다.

1. 가공되지 않은 XML 데이터를 읽어들인다.  
1. XSLT 변환 스크립트를 읽어들인다.  
1. 실행도중 선택한 값들로 xsl:param들을 덮어쓴다.  
1. XSLT로 처리한 XML을 목표 div에 출력한다.

다음의 코드는 인터넷 익스플로러용으로 이런 일을 합니다.

```cpp
function onViewLog_IE()
 {

   xmlDoc = new ActiveXObject( "MSXML2.DOMDocument.3.0" );
   xslDoc = new ActiveXObject( "MSXML2.FreeThreadedDOMDocument.3.0" );
   var xslTemplate = new ActiveXObject( "MSXML2.XSLTemplate.3.0" );

   //1. 비가공된 XML 데이터를 읽어들임:
   xmlDoc.async = "false";
   xmlDoc.load( 'XMLOutputWithComplexXSLT.xml' );

   //2. XSLT 변환 스크립트를 읽어들임:
   xslDoc.async = "false";
   xslDoc.load( 'ComplexXSLT.xsl' );

   xslTemplate.stylesheet = xslDoc;
   xslProcessor = xslTemplate.createProcessor( );
   xslProcessor.input = xmlDoc;

   //3. 실행도중 선택한 값들로 xsl:params를 덮어씀:
   xslProcessor.addParameter( "eventType",         g_eventType );
   xslProcessor.addParameter( "nameSpace",         g_nameSpace );
   xslProcessor.addParameter( "specificFile",      g_specificFile );

   //4. XSLT로 처리한 XML을 목표 div로 출력함
   xslProcessor.transform();
   logview.innerHTML = xslProcessor.output;
      
 }
```

위는 4단계를 모두 구현하는 비교적 간단한 한 함수입니다. 모질라에 기반한 브라우저용 코드는 이보다 많은 내용이 필요합니다. 모질라가 실행시에 다양한 메서드로 전달하기 위해 콜백 메커니즘을 이용하기 때문입니다.

```cpp
function onViewLog_Mozilla()
 {

   //1. 비가공된 XML 데이터를 읽어들임:
   xmlDoc = document.implementation.createDocument("", "", null);
   xmlDoc.load( 'XMLOutputWithComplexXSLT.xml' );
   xmlDoc.onload = readXML;
 
 }
      
function readXML()
 {

   xslDoc = document.implementation.createDocument("", "test", null);
   xslProcessor = new XSLTProcessor();

   //2. XSLT 변환 스크립트를 읽어들임:
   xslDoc.addEventListener("load", xslLoad, false);
   xslDoc.load( 'ComplexXSLT.xsl' );
 
 }

function xslLoad()
 {

   xslProcessor.importStylesheet( xslDoc );

   //3. 실행도중 선택한 값들로 xsl:params를 덮어씀:
   xslProcessor.setParameter( "", "eventType",     g_eventType );
   xslProcessor.setParameter( "", "nameSpace",     g_nameSpace );
   xslProcessor.setParameter( "", "specificFile",  g_specificFile );

   //4. XSLT로 처리한 XML을 목표 div로 출력함
   xmlOutput = xslProcessor.transformToDocument( xmlDoc );
   var xmlSerializer = new XMLSerializer();
   logview.innerHTML = xmlSerializer.serializeToString( xmlOutput );

 }
```

코드 자체는 본질상 앞의 인터넷익스플로러 코드와 동일하며 다른 문법을 쓸뿐입니다. 두 방법 모두에서 JavaScript가 명시적으로 어떤 .xml과 .xslt파일을 사용할 것인지를 지정한다는 사실에 유념하십시요. 이들은 실제 파일에서 지정된 값과 일치해야 합니다. 어떤 스타일 시트를 사용할 것인지를 실행시에 결정하는 것도 가능합니다. 이것만으로도 다양한 조합들을 시도해볼 수 있으며, 매우 강력한 방법입니다.

이것으로 이 글에서 논하고자 하는 개념과 기술들을 대충 다 다룬것 같습니다. 저는 여기서 사용하는 각 기술들의 기본개념들을 개별적으로 설명하였습니다. 따라서 여러분들이 이 글의 예제코드들을 직접 수정하여 사용하실수 있으실 것입니다. 여기에 포함된 파일들을 모두 살펴보실 것을 강력히 추천합니다. 이 글에서 핵심 코드와 스크립트들을 다 설명하긴 했지만 모든 코드들을 일일이 다 다룬것은 아니니 말입니다.

최종 필터링 로그파일 결과를 보시려면 여기(둘다:\[19\], 인터넷익스플로러\[20\], 모질라\[21\])를 클릭하십시오. 3가지 HTML 문서 버전이 제공됩니다. 이상적으로는 \[19\]가 모든 경우에 잘 동작할 것이나 윈도우즈 XP의 서비스팩 2의 새로운 보안기능이 이 문서의 작동을 멈추는 알수없는 현상이 발생할 수도 있습니다(아마도 인터넷 인스플로러 입장에서는 "알수없는" 모질라 코드의 존재때문인 듯 합니다). 또한 XSLT파일은 \[22\]에서 가공전의 XML은 \[23\]에서 보실 수 있습니다.

최종 결과를 브라우저에서 보면 다음과 같은 모습일 것입니다.

http://images.gamedev.net/features/programming/xmltech/image006.jpg

## 결론 {#TOC--3}

이제 여러분은 여러분의 소프트웨어에서 로그파일을 사용할 수 있는 매우 강력한 새로운 방법을 가지고 있습니다. 그리고 이것은 쉽고 공짜로 사용할 수 있는 비교적 간단한 기술을 통해 성취되었습니다. 여러분은 또한 여러분 팀의 필요에 따라 이를 마음껏 복잡하고 동적으로 바꿀 수 있는 융통성도 가지고 있습니다.

이 글은 4개의 주요 기술들로 XML, XSLT, HTML, JavaScript를 논했습니다만 이들 중 어떤 것도 모든 기능을 다 발휘하지 않았습니다. 다시 말해 고난이도의 내용은 없었다는 말입니다. 여러개의 기술들을 합쳐 비교적 간단히 이용함으로써 상용 소프트웨어에서 매우 일반적인 막강한 솔루션을 만들 수 있다는 사실이 참 재미있지 않습니까?

앞서 언급한 바와 같이 저는 이 글에서 모든 기술들을 깊게 설명하지 않았습니다. 내용이 너무 복잡해지지 않게 하는 것이 이 글의 목적이었습니다. 하지만 여러분이 직접 찾아보면 도움이 될만한 내용들이 몇 있습니다.

가장 명백한 것은 XSLT transform 파일에 xsl:params를 더 추가하여 출력 LogEvents의 범위를 ID번호나 시간 인덱스의 특정 범위로 제한하는 등입니다. XML 아웃풋의 포맷을 커스텀화한다면 별도의 xsl:params를 추가하여 이 속성들의 필터링을 처리하는 것이 좋을 것입니다. 하지만 특히 다지인 단계에서부터 폭발적인 조합의 증가에 주의하세요. 오직 3개의 매개변수만 가지고도 현재의 XSLT 임플리멘테이션은 매우 복잡하게 보일 수 있습니다.

동적으로 사용자가 소스 XML과 XSLT파일들을 지정할 수 있도록 하는 것도 매우 그럴듯한 시나리오입니다. 이는 사용자가 자바스크립트/HTML을 계속하여 사용할 것이란 뜻입니다. 다른 HTML 파일을 열지 않은 채 여러개의 로그파일들을 도렬가며 볼 수 있다면 그보다 좋은 것이 어디있겠습니다. 다른 XSLT 파일들을 지정할 수 있는 기능은 현재 사용자가 원하는 어떤 형태(레이아웃, 기능 등)로도 아웃풋을 포맷할 수 있다는 뜻입니다(예, 시각 장애를 가진 분들을 위한 뚜렷한 대조법 및 큰 폰트 아웃풋)

또다른 가능성은 모든것을 도구 하나로 묶어버리는 것입니다. 대부분의 브라우저들은 응용프로그램안에 임베드시킬수 있는 재사용가능한 컴포넌트들을 가지고 있습니다(예. MSIE ActiveX 컨트롤). 필요한 구성요소들을 모두 모아 배포가능한 형태로 만든다면 지원/설정 문제들을 어느정도 해결할 수 있습니다.

이 기술들을 보다 "웹" 같이 만드는 다른 방법은 로그파일들을 웹서버에 작성 및 저장하고, 클라이언트 컴퓨터에서 로딩할 수 있는 웹페이지를 하나 만들어 뷰어로 사용하는 것입니다. 쿠키를 여기에 결합시켜 디폴트 검색 및 선호 XSLT transform 등의 사용자 설정을 기억할 수도 있습니다. 이 방법은 출력될 XML파일을 처리 및 변형을 서버가 담당하므로 여러 브라우저 호환성 문제점들을 덜어줄 수 있습니다.

이 글을 마무리하며 여러분들이 이 글을 재밌게 보셨으면 좋겠다는 생각을 해봅니다. 물론 여기에 제시된 아이디어를 직접 사용하신다면 더할 나위 없겠지요. 이 글에 관련한 질문 및 의견이 있으신 분들은 GameDev.net 포럼에서 Jack\[24\]이나 Oli\[25\]에게 개인 메시지를 보내시길 바랍니다. 설사 질문이 없으시더라도 다른 분들이 프로젝트에서 이 기술을 사용하는 방법도 듣고 싶으니 연락주십시요.

이 글에 관련된 파일들은 \[26\]에서 모두 다운받으실 수 있습니다.

## 도움 주신 분들 {#TOC--4}

제가 이 글을 쓰는데 도움을 주신 분들이 있습니다. Oli님은 이 아이디어의 기초가 된 기술적 지식과 정보를 제공해 주셨습니다. 그리고 원래의 스레드에 참여해 주신 Degra, Agony, Etnu님에게도 감사의 말씀을 드립니다.

## 참고자료 {#TOC--5}

*   \[1\] [http://www.gamedev.net/community/forums/topic.asp?topic\_id=299160](http://www.gamedev.net/community/forums/topic.asp?topic_id=299160)
*   \[2\] [http://www.gamedev.net/community/forums/forum.asp?forum\_id=59](http://www.gamedev.net/community/forums/forum.asp?forum_id=59)
*   \[3\] http://en.wikipedia.org/wiki/Xml 
*   \[4\] http://en.wikipedia.org/wiki/SGML 
*   \[5\] http://www.w3.org/
*   \[6\] http://www.w3schools.com/xml/default.asp 
*   \[7\] http://en.wikipedia.org/wiki/Html 
*   \[8\] http://www.w3schools.com/html/default.asp 
*   \[9\] http://en.wikipedia.org/wiki/XSLT 
*   \[10\] http://www.w3schools.com/xsl/default.asp 
*   \[11\] http://en.wikipedia.org/wiki/Javascript 
*   \[12\] http://www.w3schools.com/js/default.asp 
*   \[13\] http://www.mozilla.org/products/firefox/ 
*   \[14\] http://en.wikipedia.org/wiki/SAX 
*   \[15\] http://en.wikipedia.org/wiki/Document\_Object\_Model 
*   \[16\] http://sourceforge.net/projects/tinyxml/ 
*   \[17\] [local: BasicXSLT.xsl file](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253BasicXSLT.xsl)
*   \[18\] [local: XMLOutputWithBasicXSLT.xml file](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253XMLOutputWithBasicXSLT.xml)
*   \[19\] [local: JavascriptViewer.html](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253JavascriptViewer.html)
*   \[20\] [local: InternetExplorerJavascriptViewer.html](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253InternetExplorerJavascriptViewer.html)
*   \[21\] [local: MozillaJavascriptViewer.html](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253MozillaJavascriptViewer.html)
*   \[22\] [local: ComplexXSLT.xsl](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253ComplexXSLT.xsl)
*   \[23\] [local: XMLOutputWithComplexXSLT.xml](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253XMLOutputWithComplexXSLT.xml)
*   \[24\] http://www.gamedev.net/community/forums/pmcompose.asp?sendto=3290 
*   \[25\] http://www.gamedev.net/community/forums/pmcompose.asp?sendto=26476 
*   \[26\] [local: CompleteArchive.zip](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253CompleteArchive.zip)

## 원문정보 {#TOC--6}

*   저자: Jack "jollyjeffers" Hoxley, Oli "evolutional" Wikinson
*   원문보기: [Using XML Technologies For Enhancing Log Files](https://gamedev.net/tutorials/programming/general-and-gameplay-programming/using-xml-technologies-for-enhancing-log-files-r2253)
