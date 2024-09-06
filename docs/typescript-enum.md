# TypeScript Enum: `enum`, `const enum`, 그리고 `as const` 비교

TypeScript에서 `enum`은 고정된 상수 값의 집합을 정의하는 강력한 기능입니다. 하지만 `enum` 사용 방식에 따라 코드의 최적화와 성능에 큰 차이가 발생할 수 있습니다.

특히 `enum`, `const enum`, 그리고 `as const`의 사용 방법에 대한 차이점을 이해하는 것은 중요한데, 이들을 적절히 사용하면 코드 효율성을 높이고 불필요한 번들 크기를 줄일 수 있습니다.

---

## 1. 기본 `enum`
`enum`은 JavaScript에는 없는 TypeScript 고유의 기능입니다. 값을 그룹화하여 가독성을 높일 수 있으며, 숫자와 문자열 기반 `enum`을 모두 지원합니다.

### 1.1. 사용 예시
```typescript
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

console.log(Direction.Up);  // 0
console.log(Direction[0]);  // "Up"
```

트랜스파일된 자바스크립트 코드에서 `Direction`은 즉시 실행 함수로 변환되며, 값과 키의 매핑이 객체로 남습니다.


### 1.2. 특징 - Reverse Mapping

Reverse Mapping은 TypeScript의 기본 enum에서 지원되는 고유한 기능입니다.
이는 값에서 키를 역으로 매핑할 수 있는 기능을 말합니다. 즉, enum의 값을 통해 해당하는 이름을 찾을 수 있는 것이죠.

```ts
enum Direction {
  Up = 1,
  Down,
  Left,
  Right,
}

console.log(Direction.Up);      // 1
console.log(Direction[1]);      // "Up"
```

위 예시에서 Direction.Up은 값 1을 반환하고, 반대로 값 1을 사용하여 "Up"이라는 키를 얻을 수 있습니다. 이처럼 enum의 값에서 그에 대응하는 이름을 얻을 수 있는 기능을 Reverse Mapping이라고 합니다.

### 1.3. 특징 - 번들크기 증가

TypeScript의 **enum**은 런타임에서도 동작하는 자바스크립트 객체로 트랜스파일되기 때문에, 번들 크기가 증가하고 Tree Shaking이 제대로 이루어지지 않는 문제를 일으킵니다. 

- Enum 트랜스파일 방식

기본적으로 TypeScript의 enum은 숫자나 문자열 값과 키 간의 매핑을 지원하기 위해 런타임 객체로 변환됩니다. 이 과정에서 enum은 즉시 실행 함수(IIFE) 형태로 자바스크립트 코드에 남게 됩니다.

```ts
enum Colors {
  Red,
  Green,
  Blue
}
```
이 코드를 트랜스파일하면 다음과 같이 변환됩니다:

```ts
"use strict";
var Colors;
(function (Colors) {
    Colors[Colors["Red"] = 0] = "Red";
    Colors[Colors["Green"] = 1] = "Green";
    Colors[Colors["Blue"] = 2] = "Blue";
})(Colors || (Colors = {}));​
```

- 왜 Tree Shaking이 작동하지 않는가?

**Tree Shaking**은 불필요한 코드, 즉 사용되지 않는 모듈이나 함수 등을 제거해 번들 크기를 줄이는 방식입니다. 그러나 enum은 `런타임 객체`로 변환되기 때문에, 해당 객체 자체가 코드에 남게 됩니다. 예를 들어, enum의 일부 값만 사용하더라도 전체 enum 객체가 번들에 포함되며, 사용되지 않은 값들이 제거되지 않습니다

```ts
console.log(Colors.Red);
```

위 코드에서 `Colors.Red`만 사용되었지만, 변환된 자바스크립트 코드에서는 Colors 객체 전체가 남습니다. 따라서 `Colors.Green`과 `Colors.Blue` 역시 번들에 포함됩니다.

## 2. `const enum`
`const enum`은 기본 `enum`과 유사하지만, **성능 최적화**를 위해 고안되었습니다. `const enum`은 트랜스파일된 자바스크립트 코드에서 완전히 제거되고, 값이 **인라인(inline)**으로 대체됩니다.

### 2.1 특징 - 컴파일 시 inline으로 치환

기본 `enum`과 달리, `const enum`은 컴파일 타임에 직접 해당 값으로 대체됩니다. 즉, `const enum`을 사용하는 부분에서 실제 값(숫자나 문자열 등)이 치환되어 컴파일된 자바스크립트 코드에 삽입됩니다.

```typescript
const enum Direction {
  Up = 1,
  Down,
  Left,
  Right,
}

console.log(Direction.Up);  // 1
```

트랜스파일된 자바스크립트 코드는 다음과 같습니다.
```javascript
console.log(1);  // Direction.Up이 1로 인라인 치환됨
```

여기서 Direction 객체 자체는 존재하지 않으며, 1이라는 값이 직접 치환됩니다. 이는 메모리 사용을 줄이고 런타임 성능을 높이는 효과를 줍니다.

### 2.2 문제점

`const enum`은 성능을 최적화해주긴 하지만 그로 인한 문제점이 존재하여 현재는 잘 사용하지 않습니다. 이러한 문제는 트랜스파일링 중에 일어나는 inline으로 치환하는 것 과 관련이 있습니다.

#### 2.2.1. isolatedModules 옵션과의 충돌

`isolatedModules` 옵션은 TypeScript에서 각 모듈을 개별적으로 트랜스파일할 때 발생하는 제약 사항입니다. 보통 타입스크립트 파일을 만들고 아무런 코드도 `export`를 하지 않는 경우 발생합니다.

`isolatedModules`가 활성화되면 TypeScript 컴파일러는 각 파일을 독립적으로 처리하며, 모듈 간의 종속성을 추적하지 않습니다. 이 과정에서 const enum은 컴파일 과정에서 인라인으로 대체되기 때문에, 모듈 간에 공유될 때 문제가 발생할 수 있습니다.

#### 2.2.2. 외부 선언 파일(.d.ts)과의 호환성 문제

`const enum`은 외부 라이브러리와의 호환성 문제를 일으킬 수 있습니다. 특히, .d.ts 파일과 같은 선언 파일에서 const enum을 사용할 경우 문제가 발생할 수 있습니다.

`const enum`은 선언 파일에서는 실제로 인라인되지 않기 때문에, 라이브러리 간 상호작용에서 불일치가 발생할 수 있습니다.


```ts
// 선언 파일 (.d.ts)
declare const enum Status {
  Active = 1,
  Inactive = 0,
}

// 실제 코드
const status: Status = Status.Active;
```

위 코드가 트랜스파일될 때, `const enum`은 인라인 치환되지만, `.d.ts` 파일에는 이를 관리하는 적절한 메커니즘이 없어 호환성 문제가 발생할 수 있습니다. 외부 모듈과 상호작용하거나 패키지를 배포할 때, 이런 문제는 특히 치명적일 수 있습니다

## 3. `as const` 객체
TypeScript 3.4부터 도입된 `as const`는 객체의 프로퍼티를 상수처럼 다룰 수 있게 해줍니다. 최근에는 `enum`보다 `as const` 객체가 더 선호되는 추세입니다.

`as const`를 사용하면 기본적으로 객체의 각 값을 리터럴 타입으로 고정할 수 있으며, 그 결과로 타입 안정성이 더욱 높아집니다.

### 3.1. 예시

```typescript
// 객체 예시
const DirectionValues = {
  Up: 0,
  Down: 1,
  Left: 2,
  Right: 3,
} as const;

type Direction = typeof DirectionValues[keyof typeof DirectionValues];

console.log(DirectionValues.Up);  // 0
```

```ts
// 배열 예시
const directions = ["Up", "Down", "Left", "Right"] as const;

type Direction = typeof directions[number]; // "Up" | "Down" | "Left" | "Right"
```

### 3.2. enum, const enum을 대체

`as const`는 특히 enum을 대체하는 용도로 많이 사용됩니다. enum은 타입 안전성을 제공하지만, 런타임에 객체로 변환되기 때문에 번들 크기를 증가시키고 `Tree Shaking`을 방해할 수 있습니다.

반면, `as const`는 **런타임에 별도의 변환 없이 간단한 객체로 남아 성능 최적화에 유리합니다.**

## 4. 우선순위 정리

보통 `as const` > `const enum` > `enum` 다음 순서 

TypeScript에서 enum을 사용할 때, 성능 최적화와 코드 유지 보수를 고려한 우선순위는 일반적으로 다음과 같습니다.

TypeScript에서 `enum`을 사용할 때, 성능 최적화와 코드 유지 보수를 고려한 우선순위는 일반적으로 다음과 같습니다:

1. **`as const`**
   
   가장 권장되는 방식입니다. `as const`는 간단하고 유연하며, 런타임에 최소한의 오버헤드를 발생시킵니다. 객체의 값을 리터럴 타입으로 고정할 수 있어, 타입 안전성과 최적화 측면에서 매우 유리합니다.
   - **장점**: 트랜스파일된 자바스크립트 코드가 가볍고, 다양한 타입의 값을 담을 수 있으며, 유연성이 뛰어납니다. 특히, Tree Shaking을 완벽하게 지원하여 불필요한 코드가 번들에 포함되지 않습니다.
   
2. **`const enum`**
   
   성능 최적화를 위해 설계된 `const enum`은 런타임에 코드가 인라인으로 치환되어 번들 크기를 줄일 수 있습니다. 하지만 여러 가지 제약으로 인해 사용에 주의가 필요합니다.
   - **장점**: 값이 직접 인라인되기 때문에 런타임에 `enum` 객체가 남지 않고, 번들 크기를 줄일 수 있습니다.
   - **단점**: **`isolatedModules`** 옵션을 사용할 때 `const enum`이 호환되지 않으며, 컴파일 타임에만 작동하므로 외부 라이브러리나 `.d.ts` 파일에서 사용할 때 문제가 발생할 수 있습니다.
   
3. **`enum`**
  
    기본 `enum`은 `enum` 객체가 런타임에 존재하며, 키와 값 간 양방향 매핑을 제공합니다. 하지만 Tree Shaking이 잘 되지 않기 때문에 번들 크기가 커질 수 있습니다.
   - **장점**: 양방향 매핑을 제공하며, 동적 키-값 매핑이 필요한 상황에서는 유용합니다.
   - **단점**: 트랜스파일된 자바스크립트 코드가 무겁고, 성능 저하를 초래할 수 있습니다.
