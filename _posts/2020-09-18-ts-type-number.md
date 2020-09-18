---
title: "[Typescript] 배열 속의 타입 추출하기"
date: 2020-09-18 14:42:28 +0900
categories: typescript
---

```typescript
interface Foo {
    id: number
}

interface FooList{
    fooList: Foo[]
}

type AnotherFoo = FooList['fooList'][number]
// type AnotherFoo = Foo 와 같다.
```

`[number]` 구문을 이용하면 배열 속의 타입을 가져올 수 있다. 

`['어떤 문자열']`은 그 타입 객체 오브젝트에서 특정 key 값을 추출해낸다.
{: .notice }

### 제너릭 형태로 만들기

```typescript
type Unpacked<T> = T extends (infer U)[] ? U : T
```

`T extends U[]`의 뜻은 T가 배열의 형태를 나타내는지 묻는 것이다. 여기서 extends는 어떻게 해석하느냐에 따라 쉽게 이해할 수도 있다. 나는 extends 와 삼항 연산자가 같이 쓰일 경우, T가 U[]에 속하면? 이라는 뜻으로 해석한다.
결국 T가 어떠한 배열의 형태라면 타입 U를 반환하고, 그렇지 않으면 T를 반환하겠다는 뜻이다.

infer는 extends 구문에서 사용할 수 있는데, 이는 **타입 변수를 참조하기 위해 사용하며** 주로 후반부에 삼항연산자에서 참조할 때 자주 쓰인다.
{: .notice }

