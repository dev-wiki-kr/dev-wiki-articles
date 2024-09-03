# TypeScript의 런타임 및 컴파일 환경

## 1. 타입스크립트의 타입 체크 환경

TypeScript는 정적 타입 시스템을 제공하는 언어로, **컴파일** 시점에 코드의 타입을 검사하여 타입 오류를 발견하고 이를 개발자에게 알려줍니다. 

이 덕분에 TypeScript의 타입 시스템은 코드가 실제로 실행되기 전에 타입 오류를 잡아낼 수 있도록 설계되었습니다.

이러한 타입 검사는 코드의 안정성을 높이고, 런타임 오류를 사전에 방지하는 데 큰 도움이 됩니다.

## 2. TypeScript의 런타임 타입 검사 한계

하지만 타입스크립트는 **런타임**에서는 아무런 타입 검사도 수행되지 않습니다.

이 때문에, 외부 API 호출, 사용자 입력등과 같이 동적으로 데이터를 처리할 때, 타입 안전성을 보장하기 어렵습니다.

### 2.1 예시: 런타임에서의 타입 문제

```typescript
interface User {
    id: number;
    name: string;
    age: number;
}

async function fetchUser(): Promise<User> {
    const response = await fetch('/api/user');
    const data = await response.json();
    return data; // 타입 검사는 컴파일 시점에만 이루어지고, 런타임에서는 타입이 검증되지 않음
}

const user = await fetchUser();
console.log(user.name.toUpperCase()); // 런타임 오류 발생 가능: user.name이 실제로 string이 아닐 수 있음
```

위 코드에서 TypeScript는 `fetchUser` 함수가 `User` 타입을 반환한다고 생각하지만, 실제 API 응답이 해당 구조를 따르지 않는다면 런타임 오류가 발생할 수 있습니다.

## 3. 런타임 타입 검사를 위한 도구들

그렇기에 런타임에서 타입을 검증하고자 할 때, TypeScript로는 검증이 어려워 [Zod](https://www.npmjs.com/package/zod), [Yup](https://www.npmjs.com/package/yup), [Joi](https://www.npmjs.com/package/joi) 등의 schema validation 라이브러리를 사용할 수 있습니다.

이 도구들은 런타임에서 객체의 구조와 타입을 검증하는 기능을 제공합니다.
