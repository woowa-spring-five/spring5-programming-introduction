# 1주차

## 1주차 학습목표
* 2장에서 궁금한 부분 정리하기

## Maven vs Gradle
* Maven, Gradle을 볼 때 마다 "이게 뭘까?"라는 생각이 들었다.
* "내가 직접 라이브러리를 설치하지 않고, 의존성을 고려하지 않게 도와주는 고마운 친구"라는 간략한 사실만 알고 있었다.
* 제대로 된 개념과 사용법을 몰라서 이번 기회에 한번 정리해보려고 한다.

## 그래서 빌드 도구가 뭐냐?
* 소스 코드를 컴파일, 테스트 등을 실시하여 실행 가능한 어플리케이션으로 자동 생성하는 프로그램
* 라이브러리 자동 추가 및 라이브러리간 의존성 관리
* 라이브러리 버젼 자동 동기화

> 결론적으로, 빌드와 라이브러리 관리를 쉽게 사용하기 위한 도구

## Maven
Maven은 내가 사용할 라이브러리 뿐만 아니라,

해당 라이브러리가 작동하는데 필요한 다른 라이브러리들까지 관리하여 네트워크를 통해 자동으로 다운 받아준다.(책 p35 - 의존 전이)

> Maven은 **프로젝트 빌드의 전체적인 라이프사이클**을 관리하는 도구

### 빌드 라이프사이클?
* maven 에서는 미리 정의하고 있는 빌드 순서가 있으며 이 순서를 라이프사이클이라고 한다.
* 라이프 사이클의 각 빌드 단계를 Phase라고 하는데 이런 Phase들은 의존관계를 가지고 있다.

### Maven Lifecycle
* Clean, Build, Site 세가지 라이프사이클로 크게 나눈다.
![](http://wiki.gurubee.net/download/attachments/2457635/6_MavenLifecycle.png)

### Phase
```
Clean : 이전 빌드에서 생성된 파일들을 삭제하는 단계
Validate : 프로젝트가 올바른지 확인학고 필요한 모든 정보를 사용할 수 있는 지 확인하는 단계
Compile : 프로젝트의 소스코드를 컴파일하는 단계
Test : 유닛(단위) 테스트를 수행하는 단계(테스트 실패시 빌드 실패로 처리, 스킵 가능)
Package : 실제 컴파일된 소스 코드와 리소스들을 jar등의 배포를 위한 패키지로 만드는 단계
Verify : 통합테스트 결과에 대한 검사를 실행하여 품질 기준을 충족하는지 확인하는 단계
Install : 패키지를 로컬 저장소에 설치하는 단계
Site : 프로젝트 문서를 생성하는 단계
Deploy : 만들어진 Package를 원격 저장소에 release하는 단계
```
* Phase는 Build Lifecycle의 각각의 단계를 의미 한다.
* Phase는 특정 순선에 따라서 goal이 실행되도록 구조를 제공 한다.
* Phase 간에는 의존 관계가 있다.
  * 예를 들어 package phase가 수행되기 위해서는 이전 phase가 순서대로 수행된 다음에 실행된다.

### Phase
![](http://wiki.gurubee.net/download/attachments/2457635/6_maven_phase.png)
* Phase는 Maven의 Build LifeCycle의 각각의 단계를 의미한다.
* 각각의 Phase는 의존관계를 가지고있어 해당 Phase가 수행되려면 이전 단계의 Phase가 모두 수행되어야 한다.

### Goal
![](https://t1.daumcdn.net/cfile/tistory/9949AF3359E4167315?download)
* 각 phase는 


## 참조
* https://hyojun123.github.io/2019/04/18/gradleAndMaven/
* https://wikidocs.net/18340
* http://wiki.gurubee.net/display/SWDEV/Maven+Lifecycle
* https://lng1982.tistory.com/294