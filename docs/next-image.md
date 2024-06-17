# next image

## 1. 개요

next.js에서 제공하는 image component이다. 이미지 리사이징, 이미지 포맷 변경 ,lqip, quality 조절등 이미지 최적화에 대한 여러가지 메서드를 제공한다.

## 2. next image기능

### 2.1 이미지 리사이징

이미지를 최적화할때 가장 크게 적용되는 옵션으로 실제 서비스에서 원하는 이미지 크기에 비해서 불필요하게 클 경우 이미지의 width, height를 조절하여 용량을 줄이는 기술이다.

사용방법은 `next/image`의 width, height 속성을 사용하며, 원본 이미지의 사이즈를 width, height에 맞춰 리사이징을 처리를 진행한다.

```jsx
<Image width={600} height={400} src="https://example.com">
```

### 2.2 이미지 포맷 변경

이미지의 파일 확장자 형식에는 `png` `jpg` `webp` `avif` 등등이 존재하며 각 파일 확장자에 따라서 이미지의 손실압축, 비 손실 압축에 차이가 있으며 압축률에 차이가 존재한다.

next/image는 `unoptimized` 옵션을 사용하지 않는 이상 자동으로 포맷을 각 브라우저의 요청 헤더에 있는 `accept` 값을 받아 브라우저에 지원하는 포맷중 압축률이 가장 높은 확장자로 변경을 진행한다.

그렇기에 avif를 지원하는 chrome 같은 경우는 avif를 지원하지 않는 edge 같은 경우에는 webp 형식으로 변경된다.

### 2.2.1  sharp 라이브러리

next build를 할때 sharp missing error를 마주칠때가 있는데 이는 next/image를 이용할때 sharp 패키지를 이용하라는 에러이다.

next/image에서는 기본적으로 squoosh 패키지를 이용하여 이미지 최적화를 진행하는데 sharp 패키지를 사용하면 squoosh를 사용할때 보다 더 작은 이미지 용량과 더 빠른 응답속도를 보여준다.

### 2.2.2 왜 기본이 sharp가 아니라 squoosh일까

next에서는 개발환경에 적합하고 빠르게 설치할 수 있기에 squoosh를 기본값으로 사용하고 있다. 하지만  production 레벨에서는 sharp 패키지를 사용하는 것을 권장 중 이다.

### 2.3 lqip (low quality image placeholder)

lqip란 low quality image placeholder의 약자이며 이미지가 불러와지는 동안 퀄리티가 매우 낮은 이미지를 보여주는 방식이다.

이를 이용하면 이미지를 불러오는 동안 blur 처리된 이미지를 보게되며 더 좋은 사용자 경험을 가져온다.

next/image 에서는 `placeholder="blur"` 옵션과 `blurDataUrl` 옵션을 제공하여 lqip를 지원한다.

### 2.3.1 blurDataUrl

blurDataUrl에는 base64형식의 data url이 들어가는데 이를 위해  https://plaiceholder.co/docs 를 제공한다.

```jsx
import path from "node:path";
import fs from "node:fs/promises";
 
const getImage = async (src: string) => {
  const buffer = await fs.readFile(path.join("./public", src));
 
  const {
    metadata: { height, width },
    ...plaiceholder
  } = await getPlaiceholder(buffer, { size: 10 });
 
  return {
    ...plaiceholder,
    img: { src, height, width },
  };
};
 
// Usage
const { base64, img } = await getImage("/assets/image/example.jpg");
```

### 2.4 lazy loading

페이지 로드시 현재 보여지지 않은 이미지들은 불러오지 않도록 하는 기술이다.

loading 옵션으로 이용하여 제어가 가능하며 제공하는 값은 `eager` 와 `lazy` 가 존재한다. 이는 next/image 자체기능이 아니라 html 5 img태그의 기능이기에 next image를 사용하지 않더라도 사용이 가능하다.

- eager는 즉시 이미지를 불러오지만 이미지를 불러오는 우선순위를 높이기 위해 이 값을 사용할 경우 성능에 악영향을 미칠 수 있기에 `priority` 옵션을 추천한다.
- lazy는 loading prop의 기본 값이며, 이미지가 보여지는 viewport에 도달했을때 이미지를 로딩한다.

```jsx
<Image src="https://example.com" loading="lazy" />
```

### 2.5 quality

이미지의 품질을 적당히 감소시켜 용량을 줄이는 방법이다. 

quality는 0~100까지의 값이 존재하며 기본값은 75이다. 이는 인간의 눈으로 75% 까지의 손실압축은 100%와 구분하기 힘든 마지노선이기 때문이다.

## 3. 보안

next image 에서는 악의적인 사용자에게서 서비스를 보호하기 위해서 허락한 외부 이미지만 사용가능 하도록 설정을 해야한다.

이는 next/image에서 `remotePattern` 과 `domains` 옵션을 제공한다.

### 3.1 remotePattern

next.config.js의 images 옵션으로 제공되며 protocol, hostname, port, pathname 등을 제공하며 **와일드카드(*, )** 를 사용할 수 있다.

```jsx
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: '**.example.com',
        port: '',
      },
    ],
  },
}
```

하지만 `Next.js 14` 부터 와일드카드가 모든 것을 다 허용하게 할 수 있기 때문에 보안 문제 상 deprecated 되었다.

### 3.2 domains

remotePattern과 유사하지만 와일드카드 문법을 사용할 수 없기에 remotePattern보다 좀 더 엄격하게 사용할 수 있다.

```jsx
module.exports = {
  images: {
    domains: ['assets.acme.com'],
  },
}
```

## 4. next image의 장.단점

### 4.1 장점

next/image 없이 이미지 최적화를 하려면 매우 설정들이 복잡하여 오랜 시간이 소비된다. 하지만 next에서는 이를 next/image 컴포넌트 하나로 모든 것을 할 수 있도록 제공한다.

예를 들면 이미지 리사이징 및 이미지 포맷 변경을 하려면 외부 cdn 서비스를 사용하거나 내부 이미지를 등록할때 이미지 최적화 라이브러리를 이용하여 최적화를 한 후에 업로드를 해야한다.

또한 lqip를 처리할때 blur이미지를 만든 후, picture, source 태그를 사용하여 직접 구현을 해야되는 불편함이 존재한다.

### 4.2 단점

next image를 최적화하는 주체가 nextjs의 내부 서버이기때문에 캐시 설정이 제대로 안되있거나 사용자가 많을때 next 서버에 문제를 줄 수 있다.

페이지에 20개의 이미지를 사용하고 있다면 한 사용자가 페이지를 접속할때마다 이미지 20개에 대한 최적화를 서버에서 진행하고 있다고 생각하면 된다.

이로 인해 next의 서버 사이드 렌더링이 느려질 수 있으며 심한 경우 cpu가 100%가 되어 html자체를 서빙하지 못하는 문제가 발생할 수 있다.

그렇기에 next image의 unoptimized 옵션을 이용하여 next 내부에서 최적화를 진행하지 않도록하고 외부 cdn 서비스 (cloudinary)등으로 이미지 최적화의 주체를 옮기는 방법을 사용하곤 한다.

> vercel 같은 경우 이를 따로 서빙하는 worker를 제공하지만 요금이 청구된다.
> 

## 5. 더 알아보기

- https://nextjs.org/docs/pages/api-reference/components/image
- https://oliveyoung.tech/blog/2023-06-09/nextjs-image-optimization/