# TypeScript "is" Keyword (User-Defined Type Guards)

## 1. 기존 문제점

TypeScript의 타입가드에서는 오직 `타입`이 아닌 `값`만을 처리할 수 있기에 2개이상의 User-Defined 타입에서 타입가드를 할 수 없습니다.

```typescript
type Cat = { name: string; meow: () => void };
type Dog = { name: string; bark: () => void };

type Pet = Cat | Dog;

function example() {
  if (typeof pet === Fish) { // 왼쪽은 값이고 오른쪽은 타입이기에 비교 불가능
    pet.swim();
  } else {
    pet.fly();
  }
}

```

이러한 방식은 타입 추론을 충분히 활용하지 못하며, 타입스크립트가 제공하는 정적 타입 검사의 장점을 놓치게 됩니다.

## 2. 목적
다음 문제를 해결하기 위해 [타입스크립트 1.6버전](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-6.html)에서 `is` 키워드가 나오게 됩니다.

`is` 키워드는 사용자 정의 타입 가드(User-Defined Type Guards)를 정의하는 데 사용됩니다. 위에서 

이는 특정 함수가 반환할 때, 타입스크립트에게 인자가 특정 타입이라고 알려줌으로써 타입스크립트의 타입 추론을 돕고, 코드의 안전성을 높입니다. 이를 통해 조건부 로직이 있는 코드에서도 타입 안정성을 보장받을 수 있습니다.

## 3. 사용 예제

### 3.1 기본 사용 예제
```typescript
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

function printUpperCase(value: unknown) {
  if (isString(value)) {
    // 여기서 value는 string 타입으로 좁혀짐
    console.log(value.toUpperCase());
  } else {
    console.log('Not a string');
  }
}

printUpperCase("Hello World"); // "HELLO WORLD"
printUpperCase(42); // "Not a string"
```
위 예제에서 `isString` 함수는 `value`가 `string` 타입인지 확인합니다. 이 함수가 `true`를 반환하면, 타입스크립트는 `value`를 `string`으로 간주하여, 이후 코드에서 `string` 메서드를 안전하게 사용할 수 있습니다.

### 3.2 고급 사용 예제: 복합 타입 검사
```typescript
type Cat = { name: string; meow: () => void };
type Dog = { name: string; bark: () => void };

function isCat(animal: Cat | Dog): animal is Cat {
  return (animal as Cat).meow !== undefined;
}

function makeSound(animal: Cat | Dog) {
  if (isCat(animal)) {
    animal.meow(); // 여기서 animal은 Cat 타입으로 간주됨
  } else {
    animal.bark(); // 여기서 animal은 Dog 타입으로 간주됨
  }
}

const cat: Cat = { name: 'Whiskers', meow: () => console.log('Meow!') };
const dog: Dog = { name: 'Rover', bark: () => console.log('Woof!') };

makeSound(cat); // "Meow!"
makeSound(dog); // "Woof!"
```

이 예제에서는 `Cat | Dog`와 같은 유니언 타입을 처리하기 위해 `isCat`이라는 사용자 정의 타입 가드를 사용합니다.

`isCat` 함수는 `animal`이 `Cat` 타입인지 확인하며, `true`를 반환할 경우 타입스크립트는 이를 `Cat`으로 간주하여 `meow()` 메서드를 사용할 수 있게 됩니다.


## 4. 주의할 점

### 4.1. parameter와 반환 type의 관계

is 타입 가드를 정의할 때, 매개변수의 타입과 반환 타입 간의 관계를 명확히 이해해야 합니다. 일반적으로 `parameter is Type` 형태로 사용할 때, 매개변수는 Type의 슈퍼타입이어야 합니다. 즉, 매개변수가 검사하려는 타입을 포함하는 유니언 타입이어야 합니다.

- 올바른 예제
```ts
type Animal = Cat | Dog;

function isCat(animal: Animal): animal is Cat {
  return (animal as Cat).meow !== undefined;
}
```

- 잘못된 예제

```ts
// 여기서 parameter는 Cat 타입이지만, 반환 타입도 Cat으로 지정
function isCat(animal: Cat): animal is Cat { // 불필요한 타입 가드
  return true;
}
```
위의 잘못된 예제에서는 매개변수가 이미 Cat 타입이기 때문에 타입 가드의 의미가 없어집니다. 따라서, 매개변수는 검사하려는 타입을 포함하는 슈퍼타입이어야 합니다.

```ts
function isCat(animal: string): animal is Cat { 
  return true;
}
```

위의 예제는 반환타입과 parameter의 타입은 string이기에 Cat의 타입을 포함하지 않아 적절하지 않은 예제입니다.

### 4.2. 타입 가드의 정확성

타입 가드 함수는 정확하게 타입을 식별해야 합니다. 잘못된 타입 가드는 TypeScript의 타입 추론을 혼란스럽게 만들어 런타임 오류를 초래할 수 있습니다.

```ts
type Cat = { meow: () => void };
type Dog = { bark: () => void };

function isCat(pet: Cat | Dog): pet is Cat {
  // 잘못된 타입 가드: 항상 true를 반환
  return true;
}

function getPetAction(pet: Cat | Dog) {
  if (isCat(pet)) {
    pet.meow(); // Cat이 아닌 Dog일 경우 런타임 오류 발생
  } else {
    pet.bark();
  }
}
```

위의 예제는 isCat 함수가 항상 true를 반환하기에 Dog타입인 객체에서도 `pet.meow()` 를 호출하는 런타임 오류가 발생합니다. 

is 키워드를 통하여 컴파일시 타입은 제대로 추론하지만 실제로는 런타임 오류가 날 수 있기에 return 문 내에서 실제 타입을 정확하게 식별하도록 구현해야 합니다.