---
title: "TypeScript 소개"
date: 2024-08-08 18:12:00 +0900
categories: [Development, TypeScript]
tags: [TIL2024, TypeScript]
---
> 타입스크립트 공식 문서[타입스크립트](https://www.typescriptlang.org/)를 참고하여 작성한 내용입니다.

이번에 진행하는 프로젝트에서 백엔드 코드를 TypeScript를 이용하여 작성하고 있다. TypeScript를 오랜만에 하다 보니 헷갈리는 부분도 많고 어려운 부분이 있어 프로젝트를 진행하며 TypeScript 공부를 병행해 보려고 한다.

오늘 작성하는 포스트는 작년에 KWEB 해커톤을 진행하면서 TypeScript를 소개하기 위해 작성했던 자료를 기반으로 한다. 지금 보니 틀린 부분도 있고 해서 그 부분들을 수정하고 코드를 보충하여 블로그에 적어보도록 하겠다.


## TypeScript란?
Microsoft에서 개발한 오픈 소스 프로그래밍 언어로, JavaScript의 모든 기능을 포함하며, 추가적인 기능인 타입 시스템을 제공합니다.

## TypeScript의 등장
JavaScript는 원래 브라우저를 위한 스크립트 언어입닌다.
그렇기에 자바스크립트로 수십 줄 이상의 코드를 적을 일이 많이 없었습니다. 

하지만 자바스크립트의 언어적 완성도가 높아지고 웹이 성장하면서 node.js와 같이 서버 애플리케이션을 만들 수 있는 자바스크립트 런타임이 나오게 되었고, 자바스크립트는 더 이상 웹 브라우저에서만 짧게 사용되는 코드가 아니게 되었습니다.

### JavaScript의 불편한점

**코드를 최대한 실행한다.**

자바스크립트는 코드를 최대한 실행하는 것이 원칙입니다.

```jsx
const obj = { width:10, height:"15"};
const area = obj.width + obj.height;
console.log(area);
```
위와 같은 코드를 실행하면 출력값은 `1015` 가 됩니다. 숫자를 자동으로 문자열로 변형하여 실행하는 것입니다.

```jsx
const num=11;
const strNum="11";
if(num == strNum){
  // 아무 코드 -> 실행됨
}
```
위 코드도 숫자와 문자를 같다고 판단하여 if 문 내의 코드가 실행이 됩니다.

이처럼 개발자가 생각한 방식과는 다르게 돌아가 버그의 원천이 됩니다.


**런타임에서 문제가 될 수 있는 버그를 미리 감지하지 않는다**

JavaScript는 동적 타입 언어이기 때문에 코드 작성 시에 타입 검사를 하지 않습니다.
이로 인해 코드 작성 시점에는 버그가 드러나지 않을 수 있습니다.

하지만 런타임 시점에 예상치 못한 타입이나 값을 만나면, 그때 버그가 발생할 수 있습니다.

### JavaScript의 장점
- 속도가 매우 빠르다.
- 함수형 프로그래밍을 통해 단순하게 코드를 작성 가능하다.
- 라이브러리가 풍부하다.

### 타입스크립트의 등장
위와 같은 이유로 자바스크립트를 거의 그대로 쓸 수 있으면서도 자바스크립트가 가지고 있는 단점인 타입과 코드 지원을 보완해 줄 수 있는 타입스크립트가 등장했습니다.


## JavaScript vs TypeScript
### 코드 지원
아래의 코드는 JavaScript 에디터에서 아무런 오류를 발생시키지 않습니다. 하지만 runtime에 crash가 납니다.

```jsx
function compact(arr) {
	if (orr.length > 10)
		return arr.trim(0,10)
	return arr
}
```

하지만 TypeScript를 사용하면 아래와 같이 에디터에서 에러를 잡아줍니다.

```jsx
function compact(arr) {
	if (orr.length > 10) // [ERROR] Cannot find name 'orr'
		return arr.trim(0,10) // [ERROR] Property 'trim' does not exist on type 'any[]'
	return arr
}
```

따라서 아래와 같이 정상적인 코드만 작동할 수 있게 해줍니다.

```tsx
function compact(arr: string[]) {
	if (arr.length > 10)
		return arr.slice(0,10)
	return arr
}
```

### 생산성
TypeScript는 높은 생산성을 제공합니다.

Javascript로 코드를 작성할 때, 객체의 필드나 함수의 매개변수로 들어오는 값이 무엇인지 알기 위해 여러 파일을 살펴야 했지만 TypeScript를 사용한다면 변수의 이름뿐만 아니라 그 테이터의 자료형까지 알 수 있게 됩니다. 그래서 코드 작성을 쉽고 직관적으로 할 수 있습니다.

## TypeScript 타입 시스템
### Type 정의
타입을 정의하여 디자인 패턴을 쉽게 적용할 수 있습니다.

예를 들어 nickname, id 속성을 가진 user 객체를 만든다고 해봅시다.
```jsx
// 기존 자바스크립트에서도 이렇게 객체를 만들 수 있다.
const user = {
	name: "Hayes",
	id: 0,
};

// 아래와 같이 없는 속성을 사용하려 하면 그냥 undefined 를 리턴한다.
user.username
```

```tsx
interface User {
	name: string;
	id: number;
}

// interface User의 모양을 따라야 하는 user 객체
const user: User = {
	name: "Hayes",
	id: 0,
}

// [ERROR] Property 'username' does not exist on type '{ name: string; id: number; }'.
user.username

// [ERROR] Type '{ username: string; id: number; }' is not assignable to type 'User'.
// Object literal may only specify known properties, and 'username' does not exist in type 'User'.
const user1: User = {
	username: "Hayes",
	id: 0,
}
```

TypeScript를 사용하면 javascript에 비해 유연성이 떨어집니다. 하지만 매우 많은 버그를 정확하게 잡을 수 있고, 이는 개발 과정에서 매우 도움이 됩니다.


### TypeScript의 primitive types
    
기존 JavaScript에서 제공하는 type들은 아래와 같습니다.
- `boolean`, `bigint`, `null`, `number`, `string`, `symbol`, and `undefined`

TypeScript는 위 type들을 모두 제공할 뿐만 아니라 아래 type도 추가로 제공합니다.
- `any` : allow anything
- `unknown` : ensure someone using this type declares what the type is
- `never` : it’s not possible that this type could happen
- `void` : return 값이 없거나 `undefined`인 함수
- `interface` or `type` : type을 정의할 때 사용 가능 (`interface`를 사용하는 것이 권장된다. [근거 참고 링크](https://www.typescriptlang.org/play/?e=83#example/types-vs-interfaces))

### Type 구성하기
간단한 type들을 합쳐 복잡한 type들을 만들 수 있다.

**Unions**
type이 여러 type 중 하나임을 나타낸다.

```tsx
type MyBool = true | false;
type WindowStates = "open" | "closed" | "minimized";
type PositiveOddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;
```

다양한 type을 받을 필요가 있는 함수에 아래와 같이 활용할 수도 있다.

```tsx
function wrapInArray(obj: string | string[]) {
  if (typeof obj === "string") {
    return [obj];
  }
  return obj;
}
```

**Generics**
데이터의 type을 일반화할 수 있다.

주로 array에 쓰이는데, generic이 없는 array는 아무런 객체를 담을 수 있다. generic이 있는 array는 array가 담고 있는 value를 설명할 수 있다.

```tsx
type StringArray = Array<string>;
type NumberArray = Array<number>;
type ObjectWithNameArray = Array<{ name: string }>;
```

```tsx
interface Backpack<Type> {
  add: (obj: Type) => void;
  get: () => Type;
}
 
// This line is a shortcut to tell TypeScript there is a
// constant called `backpack`, and to not worry about where it came from.
declare const backpack: Backpack<string>;
 
// object is a string, because we declared it above as the variable part of Backpack.
const object = backpack.get();
 
// Since the backpack variable is a string, you can't pass a number to the add function.
// [ERROR] Argument of type 'number' is not assignable to parameter of type 'string'.
backpack.add(23);
```

**Enums (typescript 자체 구현 기능)**
(사용 권장 X, [참고 자료](https://velog.io/@jay/typescript-enum-be-careful))
    
```tsx
enum MOBILE_OS {
  IOS = 'iOS',
  ANDROID = 'Android'
}
```

`object literral` 형식을 사용하는 게 좋다!

```tsx
const MOBILE_OS = {
  IOS: 'iOS',
  ANDROID: 'Android'
} as const;
type MOBILE_OS = typeof MOBILE_OS[keyof typeof MOBILE_OS]; // 'iOS' | 'Android'
```

아래와 같이 string union을 사용할 수도 있으나 이는 검색에 불리합니다. (ex. `MOBILE_OS.IOS`와 같은 식으로 사용할 수 없다.)

`type MOBILE_OS = 'iOS' | 'Android'`
