# Three.js 장단점

## 1. 개요

Three.js의 주요 장점과 단점을 객관적으로 분석하여, 개발자가 이를 활용하여 효과적인 3D 웹 애플리케이션을 개발할 수 있도록 안내한다.

## 2. Three.js의 장점

### 2.1. 간편한 사용법

Three.js는 복잡한 WebGL API를 추상화하여, 3D 그래픽을 쉽게 구현할 수 있도록 도와준다. 이로 인해 개발자들이 원시 WebGL 코드의 세부 사항을 깊이 이해하지 않고도 빠르게 3D 환경을 구축할 수 있다.

예를 들어, Three.js를 사용하면 기본적인 3D 장면을 설정하고 렌더링하는 데 필요한 코드가 간단하며 직관적이다.

- [Beginning with 3D WebGL](https://codepen.io/rachsmith/post/beginning-with-3d-webgl-pt-1-the-scene)
by [Rachel Smith](https://codepen.io/rachsmith/) - [Three.js 공식문서에 소개된 튜토리얼 코스](https://threejs.org/docs/index.html#manual/en/introduction/Useful-links).

프로젝트 초기 단계에서 빠른 프로토타이핑을 가능하게 하고, 복잡한 그래픽 구현에 소요되는 시간을 줄여준다. 또한, 다양한 예제 코드와 샘플이 제공되어, 초보자도 쉽게 따라할 수 있다. 이러한 접근성은 3D 그래픽 개발의 진입 장벽을 낮추고, 더 많은 개발자들이 3D 웹 애플리케이션을 시도할 수 있게 한다.

하지만, 간편하다는 것이 쉽다는 의미는 아니다. Scene, Camera, Geometry, Materials 등의 3D 공간에 대한 개념과 이해도가 반드시 필요하다.

### 2.2. 활발한 커뮤니티

강력한 커뮤니티를 보유하고 있으며, 많은 개발자와 아티스트가 사용하고 있다. 커뮤니티 내에서 다양한 튜토리얼, 도구, 플러그인, 예제 코드가 지속적으로 개발되고 공유되고 있다.

- [디스코드](https://discord.gg/56GBJwAnUS)
- [공식포럼 디스코스(discourse)](https://discourse.threejs.org/)
- [깃허브 이슈](https://github.com/mrdoob/three.js/issues)
- [스택오버플로우](https://stackoverflow.com/questions/tagged/three.js)
- [X(twitter)](https://twitter.com/threejs)
- [레딧 (r/threejs)](https://www.reddit.com/r/threejs/)

### 2.3. 플랫폼 호환성

다양한 웹 브라우저와 모바일 장치에서 호환이 가능하다. 이는 WebGL이 지원되는 모든 플랫폼에서 Three.js 애플리케이션을 실행할 수 있다. Chrome, Firefox, Safari 등 주요 브라우저에서 동일한 코드를 사용할 수 있으며, 이는 개발 시간과 유지보수 비용을 줄여준다.

또한, 모바일 장치에서도 성능 저하 없이 원활하게 동작하여, 다양한 사용자 환경에서 일관된 경험을 제공할 수 있다. 이러한 플랫폼 호환성은 Three.js를 사용한 3D 애플리케이션의 범용성을 높이며, 다양한 디바이스에서 접근할 수 있는 웹 기반 3D 콘텐츠 제작을 가능하게 한다. 이는 특히 크로스 플랫폼 애플리케이션 개발에 큰 이점이 된다.

- [WebGL 브라우저별 호환성](https://caniuse.com/webgl)
- [MDN WebGL API 브라우저별 호환성](https://caniuse.com/webgl)

### 2.4. 확장성

다양한 플러그인과 확장 기능을 지원하여, 특정 기능을 필요로 하는 복잡한 3D 애플리케이션 개발이 가능하다. 물리 엔진이나 [후처리 효과(Post-processing)](https://threejs.org/manual/#en/post-processing)를 추가하여 더욱 현실감 있는 그래픽을 구현할 수 있다.

Three.js는 모듈화된 구조를 가지고 있어, 필요에 따라 기능을 추가하거나 제거할 수 있어 유연한 개발이 가능하다. 이러한 확장성 덕분에, 개발자는 자신만의 커스텀 쉐이더나 특수 효과를 쉽게 통합할 수 있다. 또한, 외부 라이브러리와의 호환성도 높아, 다양한 오픈 소스 프로젝트와의 통합이 용이하다. 이는 복잡한 프로젝트에서도 Three.js를 효과적으로 사용할 수 있게 해준다.

## 3. Three.js의 단점

### 3.1. 성능 이슈

Three.js는 WebGL을 기반으로 하지만, 때때로 복잡한 3D 그래픽을 렌더링할 때 원시 WebGL 코드에 비해 성능이 떨어질 수 있다. 이는 추가적인 추상화 계층 때문에 발생할 수 있는 문제이다.

매우 높은 폴리곤 수를 가진 모델을 렌더링할 때 프레임 속도가 저하될 수 있다. 이는 대규모 씬이나 실시간 상호작용이 중요한 애플리케이션에서는 문제가 될 수 있다.

### 3.2. 학습 곡선

Three.js는 비교적 쉬운 라이브러리이긴 하지만, 3D 그래픽 자체의 복잡성 때문에 초보자가 학습하기에는 어려울 수 있다. 특히, 3D 모델링, 3D 좌표계, 카메라 설정, 조명, 재질, 메시(mesh) 등의 기술을 이해해야 할 필요가 있다.

3D 모델링을 직접 하거나 수정해야 할 경우, 블렌더와 같은 3D 모델링 툴을 다뤄야 할 수도 있다. 만약 3D 디자이너 및 협업할 수 있는 팀원이 없다면 [야크 털깎기](https://www.lesstif.com/software-engineering/yak-shaving-29590364.html)로 빠질 가능성이 있다.

3D 좌표계(X, Y, Z축), 카메라 설정, 조명(Light)과 재질(Mesh, Material) 같은 기본 기능 이외에도 애니메이션과 물리 엔진, 포스트 프로세싱, 성능 최적화와 같은 고급 기능들을 배우는 데에는 상당한 시간이 필요하다.

### 3.3. 문서화와 지원

커뮤니티가 활발하지만, 공식 문서나 지원이 불충분할 수 있다. 특히, 새로운 기능이나 복잡한 이슈에 대한 상세한 설명이 부족할 수 있다. 공식 문서는 기본적인 사용법을 설명하는 데 중점을 두고 있어, 고급 기능이나 특수한 상황에 대한 정보는 부족할 수 있다.

### 3.4. 브라우저 의존성

Three.js는 웹 기술에 크게 의존하기 때문에, 웹 브라우저의 성능과 WebGL 구현에 따라 애플리케이션의 성능이 크게 달라질 수 있다. 예를 들어, 구형 브라우저나 WebGL을 완벽히 지원하지 않는 브라우저에서는 Three.js 애플리케이션이 원활히 동작하지 않을 수 있다. 이는 사용자 경험에 부정적인 영향을 미칠 수 있다. 또한, 브라우저별로 미세한 차이로 인해 동일한 코드가 다르게 동작할 수 있어, 크로스 브라우저 테스트가 필요하다.

### 3.5. Babylon.js에 비교되는 점[^1]

[Babylon.js](https://www.babylonjs.com/)는 Three.js와 자주 비교가 되는 3D 오픈소스 프레임워크이다. [구글 트렌드](https://trends.google.com/trends/explore?date=today%205-y&q=three.js,babylon.js)에 따르면 Three.js에 대한 관심도가 압도적이나, 여러 이유로 인해 Babylon.js를 선택하는 경우가 있다.

이때, Babylon.js를 선택하는 이유가 곧 Three.js의 상대적인 단점이 될 수 있으므로 기술한다.

- Typescript 기반 지원
  - Babylon.js는 Typescript를 기본적으로 지원하여, 타입 안정성과 코드 완성 기능을 제공한다. 이는 대규모 프로젝트에서 코드의 안정성과 유지보수성을 높이는 데 큰 도움이 된다. Three.js도 Typescript 타입 정의를 제공하지만, 기본적으로는 JavaScript 기반이다.
- 게임엔진으로써 포지셔닝
  - Babylon.js는 3D 웹 렌더러로써 동작하는 Three.js에 비해 보다 게임 엔진으로 포지셔닝되어 있으며, 게임 개발에 최적화된 기능들을 많이 포함하고 있다.
- 디버깅에 도움이 되는 고급 도구 내장
  - Babylon.js는 디버깅에 유용한 고급 도구들을 내장하고 있다. 이러한 도구들은 개발자들이 실시간으로 3D 씬을 검사하고 수정할 수 있게 도와주며, 개발 과정에서 발생하는 문제를 신속하게 해결할 수 있게 한다. Three.js는 기본적인 디버깅 도구를 제공하지만, Babylon.js만큼 강력하고 직관적인 도구는 부족할 수 있다.
- 블렌더(Blender) 애드온 지원
  - Babylon.js는 블렌더(Blender)와 같은 3D 모델링 소프트웨어와의 통합을 위해 다양한 애드온을 지원한다. 예를 들어, Blender에서 만든 모델을 쉽게 Babylon.js로 가져와 사용할 수 있는 애드온이 제공된다. 이는 3D 아티스트와 개발자 간의 협업을 더욱 원활하게 만든다. Three.js도 다양한 파일 형식을 지원하지만, Babylon.js는 특히 이러한 통합 부분에서 더 강력한 지원을 제공한다.

## 4. 관련 문서

- [Three.js 공식 홈페이지](https://threejs.org/)
- [Three.js Github](https://github.com/mrdoob/three.js/)

[^1]: [https://www.spotvirtual.com/blog/why-we-use-babylonjs-instead-of-threejs-in-2022](https://www.spotvirtual.com/blog/why-we-use-babylonjs-instead-of-threejs-in-2022)
