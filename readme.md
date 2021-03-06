# 프로젝트 혼TV(혼모노 병신TV)

## 프로젝트 최종 목표(3Auto)
네이버 상단 뉴스기사를 자동으로 퍼와 자동으로 영상을 만들고 자동으로 유튜브에 업로드 시켜주는 소규모의 파이썬 스크립트를 작성하는것을 목표로 한다.

## 서론


### 기획자 및 참가자 명단
* 전성욱/병신TV (2019.07.01 ~ 2019.07.04)

### 프로젝트 달성으로 얻을 수 있는 단기적 이득
1. 인풋, 아웃풋의 자동화로 손가락 하나 안건들이고 영상을 만들고 업로드 할 수 있음
2. 기존 채널의 운영을 위한 영상편집실력의 스펙트럼의 확대를 노릴 수 있음
3. 반복적이고 간단한 동영상 작성을 자동화하기 위한 지식을 습득 할 수 있음

### 프로젝트 달성으로 얻을 수 있는 장기적 이득
1. 사용가능한 프로그램으로 만들어 뉴스/이슈 유튜버들에게 솔루션으로 비싼값에 팔아치울 수 있음
2. 신규 채널을 만들어 양산형 영상으로 몸집을 키운 뒤, 고연령대의 보수층 고정 시청층이 수요인 고객에게 비싼값에 팔아 치울 수 있음.
3. 성공적인 개인프로젝트로 향후 취업을 위한 포트폴리오에 기재 가능

### 프로젝트 진행에서 우려되는 점
메이저 신문사에서 기사의 사진과 내용을 무단전재시 법적대응의 우려가 있다.
때문에 스크립트가 잘 작동하는것만 확인 후 신문사에 소속된 기자가 아닌
개인이 올린 사설 게시글을 영상으로 만드는것을 목표로 하는쪽이 나을 수 있음.

### 프로젝트 하위 목표
1. 네이버 상단 뉴스기사를 크롤링
2. 뉴스 기사에서 주제 및 주요 키워드를 찾아냄
3. 2번에서 가져온 키워드를 활용해 영상/사진을 구글 이미지 검색에서 크롤링해옴
4. 크롤링한 뉴스기사를 자막사이즈에 맞게 들어가도록 일정한 문단으로 분리
5. 분리한 기사 내용과 타이틀을 TTS로 출력
6. 영상 작성및 출력
7. 유튜브에 출력
 
### 마음가짐
1. 내부모듈만 붙잡고 있지 말고 누가 만들어놓은 라이브러리/소스코드가 있으면 최대한 활용하자
2. 함수 하나의 독립성을 완벽하게 유지하도록 하자
3. 쓸데없이 객체지향 쓴다고 복잡하게 만들지 말자

## 하위 프로젝트 달성 과정

### 네이버 상단 뉴스기사 크롤링
영상 제작 자동화가 끝나고 제일 나중에 해도 되는 작업이다. 우선순위가 떨어지기 때문에 일단 인터넷 검색으로 가져온 뉴스기사를 반환하게 하였다.
### 뉴스 기사에서 주제 및 주요 키워드를 찾아냄
#### 기술스택
* python-library/ KoNLPy
#### 과정
키워드 추출을 위해 사용할 자연어 처리(Natural language processing) 라이브러리인 KoNLPy가 자바로 작성되었다. 
다른 운영체제와의 호환성때문에 그런것같다. 라이브러리 하나를 운용하기 위해 JVM을 요구하기때문에 코드의 중요성에 비해 수행시간이 오래걸렸다.

이 문제를 개선하기 위해 두가지 차선책을 생각하였다.

* **konlpy가 아닌 다른 파이썬 키워드 추출 라이브러리 사용**

soynlp가 있었으나 사전에 라이브러리의 비지도학습을 위해 뉴스기사의 데이터가 필요했다. 
블로그나 개인 사이트에서 키워드추출 스크립트가 있었으나 제대로 동작하지 않았다. 제대로된 키워드 추출 파이썬 라이브러리는 KoNLPy 뿐이었다.

* **직접 키워드를 추출하는 코드 작성**

영어단어의 사용 횟수를 세어 가장 많이 사용된 키워드를 추출하는 방법이면 짧은 코드로 가능하지만, 한국어의 의존형태소는 형태가
다양해 단어만을 추출하기 위해 애로사항이 생긴다.
 
 조사만을 지우고 세는 방법도 있으나 KoNLPy보다 정확도가 훨씬 떨어지고, 
 JVM을 실행하는데 시간자원의 소모는 생각보다 큰 문제가 되지 않기 때문에 KoNLPy를 사용하기로 했다. 
#### 결과
파이썬은 32비트인데 자바 가상머신은 64비트라 java development kit을 다시 설치하였다. 
3200자의 한국어 글에서 30개의 주요 키워드를 추출하는데 5초 가까이 걸렸다. 

### 영상/사진을 구글 이미지 검색에서 크롤링해옴
#### 기술스택
* python-library/ google_images_download
* python-library/ numpy
#### 과정
키워드 30개를 그대로 검색에 활용할 수 없었기에, 뉴스기사와 사용될 사진사이의 연관성을 높이기 위해 키워드의 중간 작업이 필요했다. 
또한 검색어에 사용되는 기사에 사용된 단어의 빈도를 일정수준으로
유지하기 위해 복잡한 배열처리과정이 필요했다. 그 과정에서 numpy를 사용해 미리 생각해놓은 방법론으로
키워드를 세개 단위로 이어붙여 키워드 30개를 10개로 만들었다. 

인터넷에 있는 이미지를 다운로드 받는데는 큰 어려움이 없었다. 구글 이미지 검색에 사용할 키워드와 추출 갯수만 넣으면
자동으로 이미지가 다운로드되고 이미지의 absolute path가 dict형태로 반환되었기 때문이다. 사용될 이미지의 주소들을 
배열형태로 바꾸어주고 반환하는 코드를 작성하는것으로 작업은 마무리되었다. 

#### 결과
이미지를 크롤링하는데 생각보다 많이걸렸다. 비동기 처리방식을 이용하면 보다 빠르게 크롤링 할 수 있었을건데 
소규모 개인라이브러리라 그런지 이미지 하나하나씩 다운로드되게 한 것 같다.

### 크롤링한 뉴스기사를 일정한 문단으로 분리
자막을 적당한 길이로 자르고 TTS의 파일 길이를 일정수준으로 유지시킬 필요가 있었다. 자막사이즈에 맞게 들어가도록 크롤링한
뉴스기사를 일정 글자의 갯수로 나누었다.영상의 수요층이 고연령대임인것을 감안해 글자 크기를 큼직하게 유지할 필요가 있었다. 
 때문에 한줄에 15자에 두줄, 한번에 30개의 글자가 나오도록 조정하였다. 


### 분리한 기사 내용과 타이틀을 TTS로 출력
코드와 설치된 TTS 사이의 의존성을 줄이기 위해 어디서나 사용할 수 있는 구글 TTS speech-api v1을 사용하였다.
전에 방송에 쓰려고 미리 만들어놓은 TTS음성을 다운로드하고 절대경로를 반환하는 파이썬 코드가 있었기에 그걸 그대로 사용하기로 했다.

### 영상 작성및 출력
#### 기술스택
* python-library/ moviepy

#### 과정
가장 복잡하고 어려우며 시간이 많이걸린 부분이었다. 사전계획이 필요했다. 
영상은 인트로, 본론부분, 아웃트로로 나뉠 예정이다. 인트로 및 아웃트로는 외부 gui 영상 편집툴로 만들어서
사용하면 되기에 나중에 만들기로 했다.

본문은 사진과 자막, tts 총 세가지 요소가 필요했다. bgm이나 다른 추가적인 요소는 나중에 생각하기로 했다.
사진의 표시길이를 일정하게 맞추기 위해 본문파트의 총 재생시간이 필요했다. 재생시간은 tts의 길이와 비례하므로 먼저
음성과 자막파트를 만들기로 했다.

자막과 음성은 간단했다. 자막을 출력하기 위해 필요한 TextClip을 만들고 tts파일을 병합시켰다. 자막사이사이에 1초정도의 
마진을 두었으며 자막은 연령층을 생각해 스타일보다는 가독성을 중요시했다. 폰트는 배달의민족 도현, 글자크기는 100
글자 색깔은 노랑, stroke색은 검정으로 설정했다. 자막의 포지션을 맞추는데 시간이 들었다.

사진컴포짓은 자막의 총 시간을 크롤링해온 사진의 갯수에 나눈다. 이것이 사진 하나하나마다 표시될 시간이었다. 
사진을 조금씩 움직이게 하고싶었는데 다행히 파라미터에 함수를 넣을 수 있었다. 사진이 삐져나와서 검은 배경이 보이지 않는
선에서 사진을 움직이도록 했다.

그렇게 만들어진 컴포짓 인스턴스들을 검정화면, 사진컴포짓, 자막컴포짓으로 합쳤다. 비트레이트는 12000에 x264코덱으로 출력했다.

#### 결과
 실패했다. MemoryError를 출력하며 코드가 제대로 실행되지 않았다. moviepy 이슈탭에 다른 사용자의 팁을 찾아보니
 파이썬의 버전을 높이면 해결할 수 있다고 적혀져 있었다. 파이썬 32비트를 64비트로 올리는것도 가능하다고 하여 
 기존의 파이썬 파일을 지우고 64비트로 재설치하였다.
 
 그렇게 출력하니 다행히도 오류는 뿜지 않았지만 복잡할게 없는 작업임에도 불구하고 3프레임을 출력하는데 1초가 걸렸다.
 그럼에도 코드를 실행하는데 메모리를 3기가씩 잡아먹었다. 자동화라고는 해도 영상 하나 만드는데
 세시간씩 걸리면 수지타산이 맞지 않는다. 
 
 

### 유튜브에 출력
상기에 서술한대로 영상제작에 차질이 발생하였기 때문에 작성하지 않았다. 

## 결론
씨발

