---
title: TypeScript 공부 - DOM과 타입캐스팅, 클래스, 접근 수식어 (Access Modifier)
date: '2022-05-16'
tags: ['100DaysOfCode', 'TypeScript']
draft: false
summary: '[Day 21] 타입스크립트에서 DOM 조작하는 법과 클래스 및 접근 수식어에 대해 배워보자'
---

# Dom 조작 / 타입캐스팅

타입스크립트에서도 자바스크립트와 마찬가지로 돔을 조작할 수 있다.

하지만 자바스크립트와 다른 점들도 있다.

그걸 알아보도록 하자.

우선 `index.html` 에 `<a>` 태그와 `<input>` 태그가 있다고 가정하자.

```ts
const anchor = document.querySelector('a');
```

타입스크립트에서도 이렇게 `querySelector`를 사용해서 돔에 접근이 가능하다.

하지만 문제는 Element의 프로퍼티에 접근하려고 할 때다.

```ts
console.log(anchor.href);
```

여기에서 오류가 난다.

왜냐하면 타입스크립트는 개발 타임에 `index.html`에 `<a>` 태그가 있는지 없는지 알 수 없기 때문이다. `<a>` 태그가 없으면 값이 `null`일 것이므로 타입스크립트는 오류를 뱉어낸다.

이걸 해결할 수 있는 방법은 세 가지다.

첫번째, 아래와 같이 null이 아닌 경우에만 프로퍼티에 접근하는 방법
```ts
if (anchor) {
    console.log(anchor.href);
}
```

두번째, 뒤에 `!` 를 붙여주는 것.
```ts
const anchor = document.querySelector('a')!;
```
저렇게 맨 뒤에 `!`를 붙여준다는 의미는 내가 이 변수의 값이 `null`이 아니라는 것을 확실히 알고 있다는 뜻이다.

마지막, 타입캐스팅 (typecasting)
```ts
const anchor = document.querySelector('a') as HTMLAnchorElement;
```

타입스크립트에서는 이렇듯 DOM에서 Element에 접근하면 DOM 타입임을 추론한다.

하지만 만약 태그 이름이 아닌 `id`나 `class`로 element에 접근하면 어떻게 될까?

만약 `index.html`의 `<input id="name" />` 에 접근하려한다 가정하자.
```ts
const name = document.querySelector('#name');
```
이 경우 타입스크립트는 해당 element가 정확히 뭔지 알 수가 없다.

이럴 때도 우리는 타입캐스팅을 이용할 수 있다.

```ts
const name = document.querySelector('#name') as HTMLInputElement;
```

# 클래스

클래스는 아래의 예시와 같이 프로퍼티의 타입을 지정할 수 있다.

참고로 프로퍼티에 default 값을 주지 않을거라면 반드시 constructor가 있어야한다.

```ts
class Student {
    name: string;
    age: number;

    constructor(n: string, a: number) {
        this.name = n;
        this.age = a;
    }

    print() {
        return `${name} is ${age} years old.`;
    }
}
```

타입스크립트의 클래스에는 프로퍼티에 타입을 지정하는 것 이외에도 프로퍼티의 접근을 제어할 수 있다. 바로 접근 수식어(access modifier)를 통해서다.

Access modifier는 세 가지가 있다. `public`, `private`, 그리고 `readonly`.

- `public` : 클래스 외부에서 프로퍼티에 접근 및 변경 가능
- `private` : 클래스 외부에서 프로퍼티에 접근과 변경 모두 불가능
- `readonly` : 클래스 외부에서 프로퍼티에 접근은 가능하지만 변경은 불가능

기본적으로 위와 같이 아무것도 명시하지 않으면 프로퍼티는 `public`이 된다.

예시를 통해 자세히 알아보자.

```ts
class Student {
    readonly name: string;
    private age: number;
    public note: string;

    constructor(n: string, a: number, n: string) {
        this.name = n;
        this.age = a;
        this.note = n;
    }

    print() {
        return `${name} is ${age} years old.`;
    }
}

let studentOne = new Student('kim', 17, 'hello');

console.log(studentOne.name); // 가능
studentOne.name = "lee"; // 불가능

console.log(studentOne.age); // 불가능
studentOne.age = 15; // 불가능

console.log(studentOne.note); // 가능
studentOne.note = "hi"; // 가능
```

그리고 앞에서는 Class에 프로퍼티를 먼저 명시하고 그 다음 constructor에서 생성시 값을 지정하는 코드를 작성했는데, 이게 너무 길다면 아래처럼 사용도 가능하다.

```ts
class Student {
    constructor(
        readonly name: string,
        private age: number,
        public note: string
    ){}
}
```

하지만 이 경우에는 반드시 프로퍼티 앞에 access modifier를 붙여줘야한다.

그렇지 않으면 인스턴스를 통해 프로퍼티에 접근이 불가능하다. 타입 `Student`에는 해당 프로퍼티가 없다라는 에러를 리턴한다. 

---

참고

- [TypeScript Tutorial Course](https://youtube.com/playlist?list=PL4cUxeGkcC9gUgr39Q_yD6v-bSyMwKPUI)