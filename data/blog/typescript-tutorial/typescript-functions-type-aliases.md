---
title: TypeScript 공부 - 함수 기본, 타입 별칭
date: '2022-05-16'
tags: ['100DaysOfCode', 'TypeScript']
draft: false
summary: '[Day 20] 함수 기본과 타입 별칭에 대해서 배워보자'
---

# 타입스크립트 함수

함수는 아래와 같이 선언할 수 있다.

```ts
let greet = (name: string) => {
  console.log(`Hello, ${name}!`);
}
```

명시적으로 선언하고 싶으면 `Function` 타입으로 지정해 준다.

```ts
let greet: Function;
```

함수의 파라미터에 타입을 지정하는 것은 앞에서 배웠다. 여기서 required 파라미터가 아닌 optional 파라미터를 추가하고 싶다면 `?:` 를 사용하거나 default 값을 주면 된다.

주의할 점은 파라미터에 required 파라미터와 optional 파라미터가 둘 다 있을 때, 항상 required 파라미터들을 먼저 적어줘야 한다.

```ts
const add = (a: number, b: number, c?: number | string) => {
    console.log(c);
    return a + b;
}

// or

const add = (a: number, b: number, c: number | string = 10) => {
    console.log(a + b);
    console.log(c);
}
```

아래와 같은 함수에서는 return 타입을 따로 지정해주지 않아도 타입스크립트가 number라고 추론한다.

```ts
const add = (a: number, b: number) => {
    return a + b;
}

let num = add(3, 5);

num = "hello"; // 불가능
```

함수에서 return 값이 없는 경우에는 `void` 타입을 가진다. 


# 타입 별칭 (Type Aliases)

타입 별칭을 사용하면 반복되는 타입 지정 코드를 줄일 수 있다.

`type` 키워드를 사용해서 타입의 별칭을 지정할 수 있다. 예를 들어 string 혹은 number를 가지는 타입 별칭을 아래와 같이 지정할 수 있다.

```ts
type StrOrNum = string | number;
```

Object 타입도 지정할 수 있다.

```ts
type person = { name: string, age: number, uid: StrOrNum };
```


---
참고

- [TypeScript Tutorial Course](https://youtube.com/playlist?list=PL4cUxeGkcC9gUgr39Q_yD6v-bSyMwKPUI)