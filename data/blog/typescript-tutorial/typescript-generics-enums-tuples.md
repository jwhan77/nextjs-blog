---
title: TypeScript 공부 - 제네릭, 이넘, 튜플
date: '2022-05-22'
tags: ['100DaysOfCode', 'TypeScript', 'generic', 'enum', 'tuple']
draft: false
summary: '[Day 23] 타입스크립트 제네릭, 이넘, 튜플'
---

# Generic (제네릭)

제네릭은 다양한 타입과 동작하는 재사용 가능한 컴포넌트를 만들기 위해 사용된다. 

예제를 살펴보며 제네릭이 어떻게 사용되는지 보자.

## 함수와 제네릭

만약 넘겨받은 인자값을 출력 후 그대로 리턴하며 모든 타입을 허용하는 함수가 있다고 가정하자.

```ts
const print = (arg: any):any => {
  console.log(arg);
  return arg;
}
```

위와 같이 구현하는 것을 생각할 수 있겠지만, 이런 경우 리턴 타입이 `any`가 되며 기존 타입의 정보를 잃어버린다. 어떻게 하면 여러 타입을 허용하되 타입 정보 값도 유지할 수 있을까?

이럴 때 우리는 Type 변수를 사용할 수 있다.

```ts
const print = <Type>(arg: Type):Type => {
  console.log(arg);
  return arg;
}
```

여기에서 `<Type>`은 제공받은 값의 타입 정보를 저장하고 사용할 수 있게 해준다. 

```ts
const output1 = print('hello'); // Type = string
const output2 = print(2); // Type = number
const output3 = print<string>('hi');
```

`output3` 에서처럼 타입을 명시해줘도 되고, 타입을 명시하지 않아도 타입스크립트는 알아서 타입을 추론한다.

두번째 예시로 특정한 프로퍼티를 가진 오브젝트 타입으로 제약한다고 가정해보자.

```ts
const printName = <T extends {name: string}>(obj: T):T => {
  console.log(obj.name);
  return obj;
}

let obj1 = printName({name: 'kim', age: 21}); // O
let obj2 = printName({age: 21}); // x
```

이런식으로 제약 조건을 나타내기 위해 `extends`를 사용할 수 있다. 위처럼 작성해도 동작은 하나 보통 인터페이스와 함께 사용하는 듯하다.

```ts
interface Person {
  name: string;
  age: number;
}

const printNameAndAge = <T extends Person>(obj: T):T => {
  console.log(`${obj.name} is ${obj.age} years old`);
  return obj;
}
```

## 인터페이스와 제네릭

함수 뿐만 아니라 인터페이스에서도 제네릭을 사용할 수 있다.

예를 들어 사람 마다 각자 다른 타입의 데이터를 가진다고 가정해보자.

```ts
interface Person<Type> {
  name: string;
  age: number;
  data: Type;
}

const personOne: Person<string> = {
  name: 'kim',
  age: 30,
  data: 'hello world'
}

const personTwo: Person<number> = {
  name: 'lee',
  age: 25,
  data: 300
}

const personThree: Person<string[]> = {
  name: 'choi',
  age: 21,
  data: ['hello', 'hi']
}
```

각자 다른 타입의 `data`를 받는 인터페이스를 따로 만들 필요없이 이런 식으로 제네릭을 사용할 수 있다.

# 
# Enum (이넘)

이넘은 상수 집합을 이름으로 정의할 수 있도록 해준다.

```js
const UP = 0;
const DOWN = 1;
const LEFT = 2;
const RIGHT = 3;
```

이걸 타입스크립트에서는 이넘을 사용해 아래와 같이 사용할 수 있다.

```ts
enum Direction { Up, Down, Left, Right }
```

별다른 명시를 하지 않으면 상수 값을 0부터 자동으로 할당한다.


#
# Tuple (튜플)

튜플은 `array`와 비슷하게 생겼지만 이미 element의 개수와 각 element의 타입을 알 때 사용할 수 있다.

```ts
let tuple: [string, number] = ['hello', 1];

// O
tuple[0] = 'hi';
tuple[1] = 2;

// X
tuple[0] = 2;
tuple[1] = 'hi';
```