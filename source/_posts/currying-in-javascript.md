---
title: Currying in JavaScript
date: 2017-01-29 20:38:26
tags:
  - Currying
  - JavaScript
  - 커링
  - 자바스크립트
---

얼마 전 [Curring in JavaScript](https://github.com/kishorsharma/currying-workshopper)를 알게 되었다. [NodeSchool](https://nodeschool.io/)에 공개되어 있는 코스로, JavaScript에서 currying 하는 방법을 알려준다. 총 6개의 문제로 구성되어 있고, currying의 기초를 습득하기 좋은 것 같다.

이것을 시작하는 방법은 매우 간단하다.

1. 설치
```bash
npm i -g currying-workshopper # for npm
yarn global add currying-workshopper # for yarn
```

1. 시작
```bash
curry
```

  - 키보드 방향키 혹은 `j`, `k`로 커서를 이동할 수 있으며, `Enter` 키를 통해 문제를 선택할 수 있다.
1. 문제 풀기
  - 문제 선택 시 터미널 창에 문제 및 설명이 출력되고, 이를 만족하는 코드를 작성하면 된다.
1. 답안 제출
  - 작성한 코드를 파일로 저장한다.
  - 답안을 제출한다.
```bash
curry verify <filename>
```

1. 문제를 다 풀 때까지 위 과정 반복

JavaScript를 다루며 사용하게 되는 것들이 많았고, 알아두면 분명 도움이 될 내용들이다. 이미 알고 있는 내용들이 있어서 생각보다 쉽게 해결할 수 있었던 것 같다. 아래는 내가 작성한 답안인데, 이 코스를 진행할 분들은 문제를 풀고 확인하면 좋을 것 같다.

1. IDENTITY
```js
const identity = (args) => args;

module.exports = identity;
```

1. BINARY
```js
const binary = (...args) => args.reduce((acc, e) => acc + e, 0);

module.exports = binary;
```

1. DELAY INVOCATION
```js
const delayInvocation = (first) => (second) => first + second;

module.exports = delayInvocation;
```

1. LONG DELAY INVOCATION
```js
const longDelayInvocation = (arg) => {
  const invocationIter = (acc) =>
    (e) => e ? invocationIter(acc + e) : acc;

  return invocationIter(0)(arg);
};

module.exports = longDelayInvocation;
```

1. CALL AND APPLY
```js
const callAndApply = {
  caller: (object, method, nameArg, ageArg, tShirtSizeArg) => {
    method.call(object, nameArg, ageArg, tShirtSizeArg);
  },
  applier: (object, method, argumentsArr) => {
    method.apply(object, argumentsArr);
  },
};

module.exports = callAndApply;
```

1. CURRY FUNCTION
```js
const curryFunction = (fx) => {
  const arity = fx.length;
  const args = [];

  const curryIter = (...params) => {
    args.push(...params);

    if (args.length < arity) {
      return curryIter;
    }

    return fx(...args);
  };

  return curryIter;
};

module.exports = curryFunction;
```
