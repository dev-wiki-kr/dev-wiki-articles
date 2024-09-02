# TypeScript Type Guard

## 1. 개요

TypeScript에서 **Type Guard**는 TypeScript에서 타입을 좁혀주는 중요한 개념으로, 런타임 시점에서 변수의 타입을 확인하고 특정 타입으로 안전하게 처리할 수 있게 도와줍니다.

## 2. 목적

TypeScript의 강력한 타입 시스템을 활용하기 위해서는 특정 상황에서 변수의 타입을 명확히 알아야 할 때가 있습니다.

**Type Guard**는 이러한 상황에서 변수의 타입을 좁혀주어 코드의 안전성을 높이고, 컴파일러가 더 정확한 타입 검사를 할 수 있도록 합니다. 이를 통해 런타임 에러를 방지하고, 코드의 유지보수성을 높일 수 있습니다.

## 3. Type Guard를 하는 방법

Type Guard를 구현하는 방법에는 여러 가지가 있습니다. 가장 일반적인 방법은 `typeof`, `instanceof`, `in` 연산자와 사용자 정의 Type Guard 함수를 사용하는 것입니다.

### 3.1. `typeof` 연산자

`typeof` 연산자는 기본적인 JavaScript 타입을 검사할 때 사용됩니다. 이 연산자는 문자열, 숫자, 불리언 등 원시 타입을 검사할 때 유용합니다.

```typescript
if (typeof someValue === 'string') {
    console.log(someValue.toUpperCase()); // string으로 타입이 좁혀짐
}
```

### 3.2. `instanceof` 연산자

`instanceof` 연산자는 객체가 특정 클래스의 인스턴스인지 확인할 때 사용됩니다. 주로 클래스 기반 객체 지향 프로그래밍에서 유용하며 Error 객체를 확장하여 custom Error 객체를 만들고 이를 구분하는데 사용됩니다.

```typescript
class Dog {
    bark() {
        console.log("Woof!");
    }
}

class Cat {
    meow() {
        console.log("Meow!");
    }
}

function makeSound(animal: Dog | Cat) {
    if (animal instanceof Dog) {
        animal.bark(); // Dog 타입으로 좁혀짐
    } else {
        animal.meow(); // Cat 타입으로 좁혀짐
    }
}

const myDog = new Dog();
makeSound(myDog); // "Woof!" 출력
```

### 3.3. `in` 연산자

`in` 연산자는 객체에 특정 프로퍼티가 있는지를 확인하는 데 사용됩니다. 이 방법은 인터페이스를 기반으로 타입을 좁힐 때 유용합니다.

```typescript
interface Fish {
    swim: () => void;
}

interface Bird {
    fly: () => void;
}

function move(animal: Fish | Bird) {
    if ('swim' in animal) {
        animal.swim(); // Fish 타입으로 좁혀짐
    } else {
        animal.fly(); // Bird 타입으로 좁혀짐
    }
}

const goldfish: Fish = { swim: () => console.log("Swim!") };
move(goldfish); // "Swim!" 출력
```

### 3.4. 사용자 정의 Type Guard 함수

TypeScript에서는 [사용자 정의 Type Guard 함수](https://devwiki.co.kr/post/typescript-is) 를 만들어 복잡한 타입을 안전하게 좁힐 수 있습니다. 이 함수는 반환 타입으로 `paramter is Type` 형태를 사용해야 합니다.

```typescript
interface Car {
    drive: () => void;
}

interface Boat {
    sail: () => void;
}

function isCar(vehicle: Car | Boat): vehicle is Car {
    return (vehicle as Car).drive !== undefined;
}

function operate(vehicle: Car | Boat) {
    if (isCar(vehicle)) {
        vehicle.drive(); // Car 타입으로 좁혀짐
    } else {
        vehicle.sail(); // Boat 타입으로 좁혀짐
    }
}
```

### 3.5. nullable type guard

Nullable Type Guard는 null 또는 undefined와 같은 값이 변수에 포함될 수 있는 경우, 해당 변수를 안전하게 처리하는 방법입니다.

 이러한 상황에서는 `Type Guard`를 사용하여 변수의 타입을 좁히고, null 또는 undefined가 아닌 경우에만 특정 로직을 실행할 수 있습니다.

```ts
function printLength(value: string | null) {
    if (value !== null) {
        console.log(`Length of string: ${value.length}`); // string 타입으로 좁혀짐
    } else {
        console.log("Value is null");
    }
}

let someString: string | null = "Hello";
printLength(someString);

someString = null;
printLength(someString);
```

## 4. 주의할 점

### 4.1. 타입가드를 방해하는 `any` 대신 `unknown`

`any` 타입은 타입 검사에서 제외되므로 Type Guard의 효과가 없습니다. Type Guard는 명시적인 타입 좁히기를 통해 타입 안전성을 보장하지만, `any` 타입은 이를 무시합니다.

```typescript
function processValue(value: any) {
    if (typeof value === 'number') {
        console.log(value.toFixed(2)); // 타입 검사 없음, 위험 가능성 있음
    }
}
```

위 코드에서 `any`를 사용하면 TypeScript의 강력한 타입 시스템을 활용할 수 없으므로, 가급적 `any` 타입을 피하고 `unknown`을 이용하여 정확한 타입을 사용하거나 Type Guard를 사용하는 것이 좋습니다.
