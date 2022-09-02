---
layout: post
title: "[Git] Git Branch 전략"
subtitle:
categories: Git
tags: [Git]
---

> 이번에 프로젝트를 진행할 때 git branch 전략도 있는 것을 알게 되었다. 코드 관리와 협업을 위해 git을 썼는데 정해진 체계없이 써서 git 관리가 잘 안돼 오히려 문제를 해결하는 데에 힘을 더 쏟았던 적이 있다.    
> git branch 전략들을 정리하며 코드관리와 협업을 위해 git을 어떤 flow로 사용해야하는지 확실히 짚고 넘어가자 ! 

## Git branch 전략이란 ?

**브랜치 생성에 규칙을 만들어서 협업을 유연하게 하는 방법론.**

- 여러 개발자가 하나의 저장소를 사용하는 환경에서 저장소를 효과적으로 활용하기 위한 work-flow.

- 브랜치의 생성, 삭제, 병합 등 git의 유연한 구조를 활용해서 각 개발자들의 혼란을 최대한 줄이며 다양한 방식으로 소스를 관리하는 역할을 한다.    

-  대표적으로 **Git flow** 와 **Github flow**가 알려져있다. 


## Git Flow

**#복잡 #체계적 #정기배포**

- git flow 에는 5가지 브랜치 존재
  - master, develop → main 브랜치
  - feature, release, hotfix → 보조 브랜치(merge 되면 사라짐)
1. master : 기준이 되는 브랜치로 제품으로 출시되는 브랜치
2. develop : 다음 출시 버전을 개발하는 브랜치
3. feature : 단위 기능을 개발하는 브랜치
4. 기능 개발이 완료되면 develop 브랜치에 Merge
5. release : 배포를 위해 master 브랜치로 보내기 전에 먼저 QA를 하기 위한 브랜치
6. hotfix : master 브랜치로 배포를 했는데 버그가 생겼을 때 긴급 수정하는 브랜치
    

**git flow 과정**

1. master 브랜치에서 develop  브랜치를 분기한다.
2. 기능 구현이 있는 경우 develop 브랜치에서 feature브랜치를 분기해서 작업한다.
3. 기능 개발이 완료되면 feature 브랜치를 develop 브랜치에 merger한다.
4. 배포를 준비하기 위해 develop 브랜치에서 release 브랜치를 분기한다.
5. 테스트를 진행 하면서 발생하는 버그 수정은 release 브랜치에 직접 반영한다.
6. 테스트가 완료되면 release 브랜치를 master와 develop에 merge 한다.
    - 오류 수정 동기화를 위해 develop에도 merge !
7. 배포 후, 버그가 발생한다면 hotfix 브랜치를 생성하여 버그 픽스를 진행하고 master와 develop 양쪽에 merge 한다.

**어디 ?** 

- 버전 관리가 필요한 앱
- 정기 배포


## Github Flow

**#간단 #단순 #상시배포**

- 1개의 master 브랜치만 유지한다.
- master가 곧 제품이 릴리즈 되는 가장 최신 버전의 브랜치이다.
- master는 언제든지 배포가 가능하다.

**Github flow 과정**

1. 브랜치 생성
    - master를 기반으로 별도 브랜치를 생성하여 기능 개발을 진행한다.
    - Github flow는 브랜치 하나에 의존하게 되기 때문에 브랜치 이름을 통해 의도를 명확하게 드러내는 것이 매우 중요하다
2. 기능 개발 후, commit & push
    - 브랜치는 로컬에서 commit 하고 정기적으로 원격 브랜치에 push 한다.
3. PR 생성
    - 코드 병합 준비가 되었다면 pulll request를 만든다.
4. 코드리뷰 후, 배포 환경에서 테스트
5. 최종 merge
    - 코드 검토 후 master에 merge 한다.
    - 병합한 master를 배포한다.

**어디 ?**

- 상시 배포

## 어떤 전략을 사용해야할까?

- 브랜치 전략에 정답은 없다.
- 개발하는 서비스에 맞는 브랜치 전략을 선택한다.
- ***만약 어울리는 전략이 없다면 새로 만든다.***


<br/>
<br/>
<br/>

> 참고
> 

[화해 기술 블로그[브랜치 전략 수립을 위한 전문가의 조언들]](http://blog.hwahae.co.kr/all/tech/tech-tech/9507/)

[https://nvie.com/posts/a-successful-git-branching-model/](https://nvie.com/posts/a-successful-git-branching-model/)

[https://hyeon9mak.github.io/git-branch-strategy/](https://hyeon9mak.github.io/git-branch-strategy/)

[https://techblog.woowahan.com/2553/](https://techblog.woowahan.com/2553/)

[https://velog.io/@kw2577/Git-branch-전략](https://velog.io/@kw2577/Git-branch-%EC%A0%84%EB%9E%B5)