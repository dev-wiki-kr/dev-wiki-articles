# TypeScript의 Soundness (타입 안전성)

## 1. 개요

TypeScript의 타입 시스템은 자바스크립트의 자유로운 특성을 보존하면서도, 정적 타입을 통해 코드의 안전성을 높이기 위해 설계되었습니다. 

여기서 `타입 안전성(Soundness)` 이라는 용어는 프로그램의 타입 시스템이 런타임 오류를 예방하는 데 얼마나 철저한지, 그리고 실제 동작이 타입 시스템에 의해 보장된 것과 얼마나 일치하는지를 의미합니다. TypeScript는 기본적으로 **"점진적 타입 시스템"**을 지향하는데, 이는 자바스크립트와의 호환성을 유지하기 위해 **완전히 타입 안전한 언어**는 아니라는 것을 뜻합니다.

## 2. TypeScript의 Unsoundness (타입 불건전성)

타입 시스템이 **soundness**하다는 것은, 컴파일 타임에 타입 검사가 통과된 코드는 런타임에 타입 오류가 발생하지 않음을 보장한다는 의미입니다.

TypeScript는 강력한 타입 시스템을 제공하지만, **100% sound한 언어는 아닙니다**. 

대표적인 예시로는 **타입 단언(Type Assertion)**, **`any` 타입**, **구조적 타입 시스템** 등을 허용하면서 일부 **Unsoundness**를 허용

## 3. Typescript가 Unsoundness를 허용한 이유

TypeScript의 목표는 자바스크립트의 유연성과 개발자 경험을 해치지 않으면서도 타입 안전성을 최대한 보장하는 것이기 때문에, 일부 상황에서는 sound하지 않게 동작할 수 있습니다.

실제로 [microsoft의 typescript design goals](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals) 문서의 Non-goals를 보면 완전히 **sound**하거나 완전한 정확성을 추구하기보다는 정확성과 생산성의 균형을 맞추는 것을 목표로 하고 있습니다.

## 4. Unsoundness 예시

### 4.1. `any` 타입 사용

**any 타입**은 타입 검사를 완전히 우회하며, 타입 시스템을 무시하는 역할을 합니다. 이것은 매우 유용할 수 있지만, 타입 안전성을 깨뜨리는 대표적인 요소입니다.

any를 사용하면 어떤 타입도 허용되며, 이는 컴파일 타임에 검사를 피하고 런타임 오류를 유발할 수 있습니다.


```typescript
let someValue: any = 5;
someValue = "Hello"; // any 타입으로 인해 어떤 값이든 할당 가능

function printLength(str: any) {
    console.log(str.length); // str이 숫자일 경우 런타임 에러 발생
}

printLength(5); // 런타임 에러 발생
```

### 4.2. 타입 단언 (Type Assertion)

**타입 단언**은 개발자가 TypeScript 컴파일러에게 "이 값은 내가 단언한 타입과 일치한다고 보증한다"고 명시하는 방법입니다.

하지만 잘못된 단언을 사용하면 컴파일러가 타입 검사를 우회하게 되어 런타임에 오류가 발생할 수 있습니다.


```typescript
let someValue: any = "Hello";
let strLength: number = (someValue as number); // 잘못된 타입 단언
```


### 4.3. 구조적 타이핑의 한계

TypeScript는 **구조적 타이핑(Structural Typing)** 을 사용하는데, 이는 객체의 실제 구조(프로퍼티)만을 기준으로 타입을 결정하는 방식입니다.

구조적으로 호환 가능하다면, 객체 간의 할당이 허용됩니다. 하지만 이로 인해 타입이 정확하게 맞지 않더라도 할당이 허용되는 경우가 발생할 수 있습니다.

```typescript
interface Person {
    name: string;
}

interface Developer extends Person {
    skills: string[];
}

let person: Person = { name: "John" };
let developer: Developer = { name: "Jane", skills: ["TypeScript"] };

person = developer; // 구조적으로 호환되므로 허용
```

Person과 Developer는 구조적으로 호환되기 때문에 Developer 타입의 객체를 Person 타입에 할당하는 것이 가능합니다.

하지만 Developer에는 skills라는 추가적인 속성이 있으며, 이는 타입스크립트가 타입 불일치를 제대로 인지하지 못하게 만들 수 있습니다.

### 4.4. 열거형 (Enums)

TypeScript의 열거형(Enums)은 특정 값 집합을 정의하는 데 유용하지만, 열거형은 숫자 값으로 컴파일되기 때문에 잘못된 값을 할당할 수 있어 타입 안전성을 보장하지 못하는 경우가 있습니다.

```ts
enum Color {
    Red,
    Green,
    Blue
}

let color: Color = Color.Red;
color = 10; // 타입스크립트가 허용함
```

열거형 Color의 값으로 숫자 10을 할당하는 것은 컴파일 타임에 에러가 발생하지 않습니다.
이는 열거형이 내부적으로 숫자로 처리되기 때문인데, 이로 인해 타입 안전성이 떨어질 수 있습니다.