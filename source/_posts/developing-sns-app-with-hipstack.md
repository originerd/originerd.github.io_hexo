---
title: Hipstack으로 SNS 앱 만들기
date: 2017-03-04 12:02:47
tags:
  - Hipstack
  - SNS
  - React Navtive
  - Redux
  - React Navigation
  - Firebase
  - Prezi
---

Huiseoul에서는 매주 금요일에 `Geektalk`을 한다. `Geektalk`은 사내 세미나로, 구체적인 주제는 없지만 기술 위주로 진행한다. 몇 달 전부터 온오프믹스를 이용해 외부 참여자도 받고 있으며, 몇 주 전부터는 [Meetup](https://www.meetup.com/ko-KR/GeekTalk-with-Huiseoul/)을 사용 중이다. 어제는 내가 발표할 차례였고, 주제는 `Hipstack으로 SNS 앱 만들기`였다.

몇 년 전 개인적으로 공부할 때 봤던 [Ruby on Rails Tutorial](https://www.railstutorial.org/book)에서는 SNS 서비스를 개발하는 걸 알려준다. 그 때 내가 만들었던 서비스가 바로 `5ns(fivens)`였다. 다섯 글자로 이야기를 나누는 것을 숫자로 형상화하고, 숫자 5는 S와 비슷해서 나름 센스있게 만들었던 것 같다…. 그 당시 `Heroku`와 `AWS S3`를 이용해 서비스를 했는데, AWS에 가입한지 1년이 지나... 서비스를 접었다.

그러다가 요즘은 무료로 충분히 서비스를 할 수 있을 것 같다는 생각에 다시 App으로 부활시키고 싶어졌다. 또한, 기존에 사용하지 않았던 새로운 기술을 습득하려는 이유도 있었다.

그렇게 시작된 프로젝트다. 이름은 기존처럼 `5ns`를 할까 하다가, `Surf5`라는 이름을 붙였다. ‘`S`hare yo`ur` `f`eeling in `5` characters’ 라는 약간의 억지라고 할 수 있는 약자를 따서 만들었다. 시작이 반이다.

다시 발표 주제로 넘어가서, `Hipstack`에 대해 설명하자면, `Hipster`와 `Stack`의 합성어로, 선정한 기술들이 나름 힙해보여서 내 맘대로 붙이게 됐다. 실제로 [HipStack.me](http://hipstack.me)라는 서비스도 있다. 아무튼... 선정한 기술들은 [React Native](https://facebook.github.io/react-native/), [Redux](http://redux.js.org), [React Navigation](https://reactnavigation.org), [Firebase](https://firebase.google.com/)가 있다.

각각의 기술들을 선택한 이유는 다음과 같다.

- `React Native`
  - 많은 기업들에서 적용 중이다.
    - Facebook, Instagram, Airbnb에서 사용하고 있다.
  - Huiseoul에서도 적용 중이다.
- `Redux`
  - 상태 관리를 위한 라이브러리
  - Huiseoul에서는 상태 관리 라이브러리로 MobX를 사용 중이다.
    - 굉장히 편리하다!
  - React - Redux 조합이 가장 널리 사용된다.
    - 거의 표준 수준으로 자리잡은 듯 하다.
  - 따라서, 사람들이 열광하는 이유를 알고 싶다.
- `React Navigation`
  - React Native의 Navigation 개발 중단했다.
  - Huiseoul에서는 ex-navigation 사용 중이다.
  - ex-navigation도 개발 중단했다.
  - React Navigation이 새로 부상하고 있다.
- `Firebase`
  - 인증, 실시간 데이터베이스, 저장소, 클라우드 메시징, 호스팅을 한 방에 제공
  - 게다가 무료! (물론 유료지만, 토이프로젝트 수준에서는 무료니까...)

만들려고 이것저것 생각하다보니 타임라인을 어떻게 구현할까에서 막혔다. RDBMS에서는 join으로 손쉽게 구현 가능하고, NoSQL 중 MongoDB에서는 in 메소드 제공한다. 이 메소드를 이용해서 팔로우 하는 사람들의 ID로 글 목록을 얻을 수 있다. 그런데, Firebase는?

그런 건 없다...

생각해본 방법은 두 가지가 있다.

1. 팔로우 중인 유저별 글 목록에서 글 가져오기
  - 타임라인인데... 시간별로 가져올 수 없다.
  - 패스!
2. 각 유저별로 타임라인을 가짐
  - 유저가 글을 쓰면 팔로워들의 타임라인에 모두 쏴주기
  - 팔로우하면 그 유저의 모든 글을 타임라인에 저장한다.
  - 반대로, 팔로우를 끊으면 그 유저의 글을 모두 삭제해야지...
  - 별로 좋아보이지는 않는다...

그런데, 이게 꽤 유명한 방식이었다. `Pan out`이라는 명칭을 가지고 있고, Firebase에서도 이를 지원한다. 조금 찝찝한 마음은 남아있지만, 이 방식으로 결정했다.

다음으로 할 일은? 데이터베이스 구조화다. Firebase는 중첩을 32단계까지 허용해 웬만한 중첩은 충분히 가능하지만, 중첩 시 한 항목을 가져오면 하위 모든 데이터를 가져오게 되고, 실시간으로 처리하게 될 경우 많은 부하가 걸리게 된다. 따라서, 최대한 중첩을 배제해 평면화하는 것이 중요하다. 이 원칙에 따라 구성해보았다.

```js
{
  "allSurfs": {
    "sid": {
      "surf": "It's a surf!",
      "timestamp": "1487350298",
      "uid": "uid"
    },
    "sid2": {}
  },
  "followers": {
    "uid": {
      "uid2": true,
      "uid3": true
    },
    "uid2": {}
  },
  "followings": {
    "uid": {
      "uid2": true,
      "uid3": true
    },
    "uid2": {}
  },
  "likeCounts": {
    "sid": 0,
    "sid2": 3
  },
  "likes": {
    "sid": {
      "uid": true,
      "uid2": true
    },
    "sid2": {}
  },
  "surfs": {
    "uid": {
      "sid": {
        "surf": "It's a surf!",
        "timestamp": "1487350298",
        "uid": "uid"
      },
      "id2": {}
    },
    "uid2": {}
  },
  "timeline": {
    "uid": {
      "sid": {
        "surf": "It's a surf!",
        "timestamp": "1487350298",
        "uid": "uid"
      },
      "id2": {}
    },
    "uid2": {}
  },
  "users": {
    "uid": {
      "name": "Jitae Kim",
      "imageURL": "http://en.gravatar.com/userimage/42185932/007e57fc0c21ede5276d3086d383af93.png?size=200"
    },
    "uid2": {}
  }
}
```

그래, 남은 건 구현 뿐! 뚝딱뚝딱.

짧은 소감을 남겨보자면,

- Redux
  - 소규모에선 MobX로도 충분할 것 같다.
  - Redux는 MobX에 비해 코드량이 많다.
  - 많은 코드량이 꼭 문제는 아닐 것이라 생각한다.
  - 상태가 매우 투명하게 드러나는 것이 장점이다.
  - Pure function 특성상 테스트가 용이하다.
  - 대규모에선 Redux가 확실히 강점을 가질 듯하다.
- React Navigation
  - 전체적으로 약간 아쉬운 면들이 많았다.
  - 아직은 `ex-navigation`이 더 나을 듯하다.
  - Card / Modal 초기 설정 후 변경을 할 수 없다.
  - TabNavigation의 default tab 이동 시 트래킹 되지 않는다.
  - 점점 나아지겠지...
- Firebase
  - 아직은 인증, 실시간데이터 베이스 위주로 써봤다.
  - 역시 생각대로 매우 자연스럽고 깔끔하게 사용 가능하다.
  - 비교적 검색 기능이 빈약하지만 실시간 처리가 뛰어나다.
  - 간단한 서비스는 충분히 Firebase로 처리할 수 있을 듯하다.
  - 게다가, 토이프로젝트로는 충분할 정도의 자원을 지원한다.

프로젝트는 현재 진행형으로, https://github.com/originerd/surf5 에 공개되어 있다.

그리고, [Geektalk에서 발표했던 자료](https://prezi.com/gszsvlbxgwkx)를 Prezi로 만들었다. 처음에는 위치 잡고, 루트 정하고 하느라 오래 걸렸는데, 일단 내용을 미리 만들어두고, 위치 및 루트를 정하는 게 훨씬 나을 거라 생각했다. 그리고, Prezi는 서비스 특성상 이동이 많은데, 약간은 불편해 하시는 분들이 있는 것 같다. 그런 분들을 위해서 약간의 팁을 드리자면, 이동 키를 두 번 연속으로 누르면 빠르게 이동된다. 굳굳
