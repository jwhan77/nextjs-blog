---
title: TypeScript 공부 - 모듈, 인터페이스
date: '2022-05-18'
tags: ['100DaysOfCode', 'TypeScript', 'module', 'interface']
draft: false
summary: '[Day 22] 타입스크립트의 모듈과 인터페이스에 대해 배워보자'
---

# 모듈

이건 타입스크립트에만 국한되는 이야기는 아니고, 프로젝트를 효율적으로 관리하기 위해서는 모듈화를 해야한다는 건 모두 알거다.

물론 타입스크립트를 모던 자바스크립트로 컴파일한다고 해서 짜잔하고 구 버전의 브라우저에서까지 사용가능하도록 만들어주진 않는다.

하지만 그건 웹팩에서 더 자세히 알아보고 우선은 기본적인 `tsconfig.json` 파일에 모듈화를 위한 설정을 어떻게 하는지 알아보자.

```json
{
  "compilerOptions": {
    ...
    "target": "es2016",
    "module": "es6",
    ...
  },
  ...
}
```

`target`과 `module`을 위와 같이 바꾸고 `index.html` 파일에서도 아래와 같이 `type="module"`을 추가해준다.
```html
<script type="module" src='app.js'></script>
```

그리고 가장중요한 건 모듈을 import 할 때는 `.ts`파일이라도 아래와 같이 `.js`로 해야한다.
```ts
import { App } from './app.js';
```
왜냐하면 컴파일 후에는 자바스크립트 파일로 바뀌기 때문이다.

<br />

# 인터페이스

인터페이스는 클래스나 오브젝트가 특정한 구조, 즉 어떤 프로퍼티나 메소드를 가질지, 리턴 타입은 어떨지 등을 강제하는 것이다.

```ts
interface IsPerson {
  name: string;
  age: number;
  speak(a: string): void;
  spend(a: number): number;
}
```

인터페이스에는 constructor가 없다. 왜냐하면 새로운 오브젝트를 만들기 위해 사용하는게 아니기 때문이다.

## 인터페이스와 오브젝트

인터페이스의 구조를 따라 오브젝트를 생성하면 아래와 같다.
```ts
const personA: IsPerson = {
  name: 'kim',
  age: 29,
  speak(text: string): void {
    console.log(text);
  },
  spend(amount: number): number {
    console.log(`${this.name} spent ${amount}`);
    return amount;
  }
}
```

인터페이스를 사용하면 코드 구조를 좀 더 명확하고 안정적으로 만들 수 있다. 타입스크립트 자체가 그렇지만 결국 인터페이스도 미래에 만들만한 실수를 방지하는거다.

## 인터페이스와 클래스

위에서는 인터페이스로부터 오브젝트를 생성하는 걸 봤다면 클래스에 사용하는 걸 살펴보자.

```ts
interface Person {
  readonly name: string;
  readonly age: number;
  speak(a: string): void;
}

class Student implements Person {
  constructor(
    readonly name: string,
    readonly age: number
  ) {}

  speak(text: string): void {
    console.log(text);
  }
}

const studentOne = new Student('kim', 17);
```


---

참고

- [TypeScript Tutorial Course](https://youtube.com/playlist?list=PL4cUxeGkcC9gUgr39Q_yD6v-bSyMwKPUI)