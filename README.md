# Turbopack

Turbopack은 Next.js의 개발 팀에 의해 만들어진 새로운 JavaScript 번들러로, 웹 애플리케이션의 빌드 성능을 크게 향상시키는 것을 목표로 하고 있다. 
빠른 빌드 속도, HMR 지원, Zero Configuration 등 여러 강력한 기능을 통해 개발자 경험을 향상시키고, 더 나은 개발 환경을 제공한다.

1. 빠른 빌드 성능: 
Turbopack은 Rust로 작성된 번들러로, 빠른 빌드 성능을 제공한다. Webpack에 비해 훨씬 더 빠른 속도로 파일을 처리할 수 있으며, 이를 통해 개발자들이 더 효율적으로 작업할 수 있도록 돕는다.

2. HMR(Hot Module Replacement):
Turbopack은 HMR을 지원하여, 코드 변경 시 전체 페이지를 새로 고침하지 않고도 변경 사항을 즉시 반영할 수 있다. 이는 개발 중에 더 빠른 피드백 루프를 제공한다.

3. Incremental Build:
Turbopack은 증분 빌드를 지원하여, 변경된 파일만 다시 빌드합니다. 이를 통해 전체 빌드 시간을 줄이고, 대규모 프로젝트에서 특히 유용하다.

4. Zero Configuration:
Turbopack은 가능한 한 많은 기본 설정을 제공하여, 개발자가 복잡한 설정 없이 쉽게 사용할 수 있도록 합니다. 즉, 기본적인 사용에 있어 별도의 설정 없이도 작동한다.
Turbopack에 대한 설정을 수정하고 싶으면 next.config.js 파일에 밑에와 같이 수정해주면 된다.

```javascript
// next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    turbopack: true, // Turbopack 활성화
  },
  turbopack: {
    // 필요한 경우 추가 설정

    // 예시: 특정 플러그인 사용
    plugins: [/* 추가 플러그인 */],

    // 예시: 특정 폴더를 제외할 경우
    exclude: ['node_modules', '.next'],

    // 예시: 특정 파일 형식에 대한 설정
    fileExtensions: ['js', 'ts', 'jsx', 'tsx', 'css', 'scss'],
  },
};

module.exports = nextConfig;
```


