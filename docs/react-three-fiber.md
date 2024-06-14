# React-Three/Fiber(r3f)

## 1. 개요

React-three-fiber는 Three.js를 리액트 생태계로 가져와 활용할 수 있게 만든 렌더러이다.

리액트의 상태 관리와 컴포넌트 기능들을 활용할 수 있다. 편의성이나 재사용성 측면에서 기존 바닐라 Three.js에 비해 가지는 장점들이 있다.

## 2. 특징

### **2.1. React 친화적**

React가 가장 많이 사용되는 프론트엔드 라이브러리인 만큼, 개발자들이 보다 쉽게 Three.js를 활용할 수 있다. 리액트 상태 관리와 훅 시스템 사용 경험을 그대로 이어나갈 수 있으며, 3D 객체를 리액트 컴포넌트처럼 관리할 수 있어 코드의 재사용성과 유지보수성이 높다.

### 2.2. 성능 최적화

Three.js를 한 번 더 렌더링을 하여 성능이 저하되는 것처럼 생각할 수 있으나, [공식문서](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction#is-it-slower-than-plain-threejs?)에서는 리액트의 스케쥴링에 의해 규모 측면에서 Three.js보다 뛰어나다고 한다.
