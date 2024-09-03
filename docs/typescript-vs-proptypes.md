# React PropTypes vs TypeScript: 컴파일 vs 런타임 타입 체크

React 애플리케이션에서 타입 안전성을 보장하는 방법으로는 주로 **PropTypes**와 **TypeScript** 이렇게 두 가지가 있습니다.

PropTypes는 런타임에 props의 타입을 검증합니다. 반면, TypeScript는 컴파일 시점에 타입을 검증하여 애플리케이션 전반의 타입 안전성을 보장합니다.
 
이 글에서는 PropTypes와 TypeScript의 차이점, 장단점, 그리고 [이 둘이 어떻게 타입 검사](https://devwiki.co.kr/post/typescript-compile-and-runtime)를 수행하는지에 대해 비교해 보겠습니다.

## 1. PropTypes: 런타임 타입 체크

**PropTypes**는 React에서 컴포넌트의 props가 예상한 타입인지 확인하는 데 사용되는 도구입니다.
 PropTypes는 props가 잘못된 타입일 경우, 런타임에 경고 메시지를 출력합니다.

PropTypes는 컴포넌트의 `propTypes` 속성을 통해 정의됩니다. 각 prop의 예상 타입을 명시하고, 런타임에 해당 prop이 올바른 타입을 가지는지 검증합니다.

```javascript
import PropTypes from 'prop-types';

function UserProfile({ id, name, age }) {
    return (
        <div>
            <h1>{name}</h1>
            <p>Age: {age}</p>
        </div>
    );
}

UserProfile.propTypes = {
    id: PropTypes.number.isRequired,
    name: PropTypes.string.isRequired,
    age: PropTypes.number.isRequired,
};
```

### 1.1. 장점

- **런타임 검증**: PropTypes는 런타임에 props의 타입을 검증합니다. 따라서, 개발자가 실수로 잘못된 타입의 데이터를 전달했을 때, 경고 메시지를 통해 이를 즉시 확인할 수 있습니다.

### 1.2.단점

- **런타임에만 타입 검증**: PropTypes는 런타임에만 타입을 검증하므로, 컴파일 시점에는 타입 오류를 잡아낼 수 없습니다. 이는 실수를 사전에 방지하는 데 한계가 있을 수 있습니다.

- **제한된 타입 지원**: PropTypes는 TypeScript에 비해 타입 시스템이 단순하며, 복잡한 타입 구조나 고급 타입 검증이 어렵습니다. 예를 들어, 유니언 타입이나 인터섹션 타입을 명확하게 표현하기 어렵습니다.

- **런타임 성능 오버헤드**: PropTypes는 런타임에 타입 검사를 수행하므로, 대규모 애플리케이션에서 성능 오버헤드를 초래할 수 있습니다.

## 2. TypeScript: 컴파일 시점 타입 체크

**TypeScript**는 JavaScript에 정적 타입 시스템을 추가한 언어입니다.
TypeScript는 컴파일 시점에 타입을 검증하여 코드의 타입 안전성을 높이며, 런타임 오류를 사전에 방지할 수 있습니다.

```typescript
interface UserProfileProps {
    id: number;
    name: string;
    age: number;
}

function UserProfile({ id, name, age }: UserProfileProps) {
    return (
        <div>
            <h1>{name}</h1>
            <p>Age: {age}</p>
        </div>
    );
}
```

### 장점

- **컴파일 시점 타입 검증**: TypeScript는 컴파일 시점에 타입을 검증하므로, 개발자가 코드를 작성할 때 타입 오류를 즉시 확인할 수 있습니다. 이는 런타임 오류를 사전에 방지하는 데 매우 효과적입니다.

- **강력한 타입 시스템**: TypeScript는 복잡한 타입 구조를 표현할 수 있는 강력한 타입 시스템을 제공합니다. 제네릭, 유니언 타입, 인터섹션 타입, 튜플 등 다양한 타입을 지원하여, 더욱 정교한 타입 검증이 가능합니다.

- **IDE와의 결합**: TypeScript에는 VSCode뿐만 아니라 TypeScript를 지원하는 다양한 IDE와 함께 작동하는 도구도 있기에 개발 생산성 향상에 도움이 됩니다.

### 단점

1. **초기 설정 및 학습 곡선**: TypeScript는 PropTypes에 비해 초기 설정이 복잡하며, 새로운 문법과 개념을 익히는 데 시간이 걸릴 수 있습니다.

2. **런타임 타입 검증 부재**: TypeScript는 컴파일 시점에만 타입을 검증하므로, 런타임에서 API 응답이나 사용자 입력과 같은 동적 데이터를 처리할 때는 별도의 런타임 타입 검증 도구(Zod, Yup 등)가 필요합니다.