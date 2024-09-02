# TypeScript Discriminated Unions

## 1. 개요

**Discriminated Unions**는 TypeScript에서 유니언 타입을 다룰 때 각 타입을 구별하기 위해 사용되는 패턴입니다.

 이 패턴은 유니언 타입의 각 멤버가 고유한 **디스크리미네이터 필드**를 갖도록 하여, 이 필드를 기준으로 타입을 안전하게 좁히는 데 사용됩니다. Discriminated Unions는 복잡한 타입 구조에서 각 타입을 명확하게 구분하고 처리할 수 있도록 도와줍니다.

## 2. 목적

Discriminated Unions의 목적은 여러 타입이 섞여 있는 유니언 타입에서 특정 타입을 명확하게 구분하고, 각 타입에 맞는 로직을 안전하게 처리하는 것입니다.

## 3. Discriminated Unions를 사용하는 방법

`Discriminated Unions` 패턴을 사용하려면, 유니언 타입에 포함된 각 타입에 공통적으로 사용되는 **디스크리미네이터 필드**를 추가합니다.

이 필드는 일반적으로 문자열 리터럴 타입으로 정의되며, 각 타입에 고유한 값을 갖습니다. 그런 다음, 이 필드를 기반으로 조건문(`if`, `switch` 등)을 사용하여 타입을 좁힙니다.

### 3.1. 기본적인 Discriminated Unions 예제

아래는 Discriminated Unions의 간단한 예제입니다.

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
            return _exhaustiveCheck; // 모든 케이스가 처리되었음을 보장
    }
}
```

### 3.2. 실전 예제: API 응답 처리

Discriminated Unions는 실제 애플리케이션 개발에서 자주 사용됩니다. 예를 들어, API 응답이 성공 또는 실패로 나뉠 때 주로 사용할 수 있습니다.

```typescript
interface SuccessResponse {
    status: 'success';
    data: {
        id: number;
        name: string;
    };
}

interface ErrorResponse {
    status: 'error';
    error: {
        message: string;
        code: number;
    };
}

type ApiResponse = SuccessResponse | ErrorResponse;

function handleApiResponse(response: ApiResponse) {
    if (response.status === 'success') {
        console.log(`Request succeeded with data: ${response.data.name}`);
    } else {
        console.error(`Request failed with error: ${response.error.message} (code: ${response.error.code})`);
    }
}
```

이 예제에서 `status` 필드는 디스크리미네이터 필드로 사용되며, `ApiResponse`의 성공과 실패를 구분하는 역할을 합니다.

### 3.3 `Discriminated Unions` 활용 예시

`Discriminated Unions`는 서로 겹치는 필드가 없는 두 타입의 유니온 일때도 서로 겹치는 필드를 만들어서 사용할 수 있습니다.

```ts
interface Dog {
  bark: () => void;
  breed: string;
}

interface Fish {
  swim: () => void;
  species: string;
}

type Pet = Dog | Fish;
```

위의 `Pet` 타입은 서로 공통된 타입이 없기에 type narrowing의 어려움이 있을 수 있습니다. 하지만 여기에 공통된 필드명을 추가해준다면 `Discriminated Unions`을 사용할 수 있습니다.

```ts
interface Dog {
    type: 'dog';
    bark: () => void;
    breed: string;
}

interface Fish {
    type: 'fish';
    swim: () => void;
    species: string;
}

type Pet = Dog | Fish;

function handlePet(pet: Pet) {
    if (pet.type === 'dog') {
        console.log(`This is a ${pet.breed} dog.`);
        pet.bark();
    } else {
        console.log(`This is a ${pet.species} fish.`);
        pet.swim();
    }
}
```

## 4.Discriminated Unions와 `in` 연산자의 차이

`Discriminated Unions`로 공통 필드를 나눠 구분하는 것과 `in` 키워드를 통해 필드가 있는지 확인하는 방법은 유사하지만 몇 가지 차이점이 있습니다.

### 4.1. **명확한 타입 구분**

Discriminated Unions는 타입을 구분하기 위해 명시적으로 디스크리미네이터 필드를 사용합니다. 이는 코드의 의도를 명확하게 전달하며, 개발자가 코드를 읽을 때 각 타입이 어떻게 구분되는지 쉽게 이해할 수 있습니다.

```typescript
function handleShape(shape: Shape) {
    if (shape.kind === 'circle') {
        console.log(`Circle with radius ${shape.radius}`);
    } else if (shape.kind === 'square') {
        console.log(`Square with side length ${shape.sideLength}`);
    }
}
```

`in` 연산자를 사용하는 방법은 주로 객체에 특정 프로퍼티가 있는지 확인하는 데 초점이 맞춰져 있습니다. 이는 명시적인 타입 구분보다는 동적으로 타입을 좁히는 데 유용합니다.

```typescript
function handleShape(shape: Circle | Square) {
    if ('radius' in shape) {
        console.log(`Circle with radius ${shape.radius}`);
    } else {
        console.log(`Square with side length ${shape.sideLength}`);
    }
}
```

### 4.2. **타입 추론**

Discriminated Unions를 사용하면 TypeScript는 조건문 안에서 자동으로 타입을 좁힙니다. 

예를 들어, `if (pet.type === 'dog')`와 같은 조건문 안에서는 pet이 자동으로 Dog 타입으로 좁혀지며, 이는 TypeScript가 타입 검사를 더 정확하게 수행할 수 있도록 도와줍니다.

```ts
if (pet.type === 'dog') {
    pet.bark(); // TypeScript는 pet이 Dog 타입임을 인식합니다.
}
```

반면에 in 연산자를 사용할 경우에도 타입을 좁힐 수 있지만, **유니온의 개수가 늘어나게 되면 각각의 타입을 필드로 구분하는데 어려움이 생깁니다.**