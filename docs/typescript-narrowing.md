# TypeScript Type Narrowing

## 1. 개요

TypeScript의 **Type Narrowing**은 코드 작성 시 변수가 여러 가능한 타입을 가질 수 있을 때, 특정 조건을 통해 변수가 갖는 타입을 좁혀가는 과정입니다.

## 2. 목적

TypeScript에서 Type Narrowing의 목적은 변수의 타입을 명확히 하여 해당 변수에 대한 안전한 접근과 조작을 가능하게 하는 것입니다.

 특히 유니언 타입(`string | number`와 같은)을 다룰 때, Type Narrowing은 타입 오류를 방지하고, 개발자가 의도한 대로 코드를 실행할 수 있도록 도와줍니다. Type Narrowing은 TypeScript 컴파일러가 변수의 타입을 예측하고, 그에 따라 타입 검사를 수행할 수 있도록 돕습니다.

## 3. Type Narrowing을 하는 방법

### 3.1. 타입가드를 통한 Narrowing

가장 기본적인 방식은 `if`, `else`, `switch` 등의 조건문을 사용하여 [타입 가드](https://devwiki.co.kr/post/typescript-type-guard)를 활용하는 것 입니다.  TypeScript는 이러한 조건문을 통해 타입을 추론하고, 특정 조건이 참일 때 해당 블록 내에서 변수의 타입을 좁힙니다.

```typescript
function printValue(value: string | number) {
    if (typeof value === 'string') {
        console.log(value.toUpperCase()); // 이 블록에서는 value가 string으로 좁혀짐
    } else {
        console.log(value.toFixed(2)); // 이 블록에서는 value가 number로 좁혀짐
    }
}
```

### 3.2. Discriminated Unions (구별된 유니온)

[Discriminated Unions](https://devwiki.co.kr/post/discriminated-unions)는 Type Narrowing에서 매우 중요한 패턴입니다. 

Discriminated Union은 각 유니언 멤버에 공통적으로 사용되는 `kind` 또는 `type` 같은 **디스크리미네이터(discriminator) 필드**를 사용하여 타입을 좁히는 방법입니다. 이 방법은 TypeScript가 자동으로 타입을 좁힐 수 있도록 돕습니다.

```typescript
interface Circle {
    kind: 'circle';
    radius: number;
}

interface Square {
    kind: 'square';
    sideLength: number;
}

interface Triangle {
    kind: 'triangle';
    base: number;
    height: number;
}

type Shape = Circle | Square | Triangle;

function getArea(shape: Shape): number {
    switch (shape.kind) {
        case 'circle':
            return Math.PI * shape.radius * shape.radius;
        case 'square':
            return shape.sideLength * shape.sideLength;
        case 'triangle':
            return 0.5 * shape.base * shape.height;
        default:
            const _exhaustiveCheck: never = shape;
            return _exhaustiveCheck; // 모든 경우가 처리되었음을 보장함
    }
}
```

## 4. Type Narrowing과 Type Guard의 차이

**Type Narrowing**은 변수가 가질 수 있는 타입의 범위를 좁히는 과정 그 자체를 의미합니다. Narrowing은 여러 방법을 통해 이루어질 수 있으며, TypeScript가 타입을 자동으로 좁히는 여러 메커니즘을 포함합니다.

반면에 **Type Guard**는 Narrowing의 한 방법입니다. Type Guard는 특정 조건을 기반으로 변수가 특정 타입인지 확인하고, 해당 조건이 참일 때 타입을 좁히는 역할을 합니다. **Type Guard는 Narrowing을 수행하는 도구**라고 할 수 있습니다.
