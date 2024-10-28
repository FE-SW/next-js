# 1.Turbopack

Turbopack은 Next.js의 개발 팀에 의해 만들어진 새로운 JavaScript 번들러로, 웹 애플리케이션의 빌드 성능을 크게 향상시키는 것을 목표로 하고 있다. 
빠른 빌드 속도, HMR 지원, Zero Configuration 등 여러 강력한 기능을 통해 개발자 경험을 향상시키고, 더 나은 개발 환경을 제공한다.

### 1. 빠른 빌드 성능:
Turbopack은 Rust로 작성된 번들러로, 빠른 빌드 성능을 제공한다. Webpack에 비해 훨씬 더 빠른 속도로 파일을 처리할 수 있으며, 이를 통해 개발자들이 더 효율적으로 작업할 수 있도록 돕는다.

### 2. HMR(Hot Module Replacement):
Turbopack은 HMR을 지원하여, 코드 변경 시 전체 페이지를 새로 고침하지 않고도 변경 사항을 즉시 반영할 수 있다. 이는 개발 중에 더 빠른 피드백 루프를 제공한다.

### 3. Incremental Build:
Turbopack은 증분 빌드를 지원하여, 변경된 파일만 다시 빌드합니다. 이를 통해 전체 빌드 시간을 줄이고, 대규모 프로젝트에서 특히 유용하다.

### 4. Zero Configuration:
Turbopack은 가능한 한 많은 기본 설정을 제공하여, 개발자가 복잡한 설정 없이 쉽게 사용할 수 있도록 합니다. 즉, 기본적인 사용에 있어 별도의 설정 없이도 작동한다.
Turbopack에 대한 설정을 수정하고 싶으면 next.config.js 파일에서 수정해주면 된다.

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

# 2.App Router, Page Router
Next.js 14에서는 앱 라우터와 페이지 라우터 모두 강력한 기능을 제공하며, 각 라우팅 방식은 특정 요구 사항에 따라 선택하여 사용할 수 있다. 
앱 라우터는 새로운 기능과 유연성을 제공하는 반면, 페이지 라우터는 기존의 안정성을 유지한다.


## App Router
앱 라우터는 Next.js의 새로운 라우팅 시스템으로, 더 나은 모듈화와 재사용성을 제공한다.

### 1.디렉토리 기반 라우팅:
앱 라우터는 파일 시스템을 기반으로 한 라우팅 방식을 사용한다. app 디렉토리 내에 생성된 파일과 폴더 구조에 따라 자동으로 라우트가 생성된다.
예를 들어, app/about/page.js 파일은 /about 경로에 매핑된다.


### 2.서버 컴포넌트와 클라이언트 컴포넌트:
앱 라우터는 서버 컴포넌트와 클라이언트 컴포넌트를 혼합하여 사용할 수 있다. 서버 컴포넌트는 서버에서 렌더링되고, 클라이언트 컴포넌트는 클라이언트에서 실행된다.
이를 통해 데이터 가져오기와 UI 렌더링을 최적화할 수 있다.

```javascript
app/
├── layout.js
├── ClientComponent.js
└── page.js
```

```javascript
export default function Layout({ children }) {
  return (
    <div>
      <header>
        <h1>My Website</h1>
      </header>
      <main>{children}</main>
    </div>
  );
}
```
```javascript
import ClientComponent from './ClientComponent';

export default function HomePage() {
  return (
    <div>
      <h1>Home Page</h1>
      <ClientComponent />
    </div>
  );
}
```
```javascript
// 클라이언트 컴포넌트
'use client';

export default function ClientComponent() {
  return <p>This is a client-side rendered component.</p>;
}
```

### 3.중첩 라우팅:
중첩된 레이아웃을 통해 복잡한 사용자 인터페이스를 쉽게 구성할 수 있다. 각 레이아웃은 독립적으로 관리되며, 재사용이 가능하다.

```javascript
app/
├── dashboard/
│   ├── layout.js
│   └── page.js
└── layout.js
```

```javascript
//app/layout.js:
export default function AppLayout({ children }) {
  return (
    <html>
      <body>
        <header>App Header</header>
        <main>{children}</main>
        <footer>App Footer</footer>
      </body>
    </html>
  );
}
```

```javascript
//app/dashboard/layout.js:
export default function DashboardLayout({ children }) {
  return (
    <div>
      <h2>Dashboard Menu</h2>
      <div>{children}</div>
    </div>
  );
}
```

```javascript
//app/dashboard/page.js:
export default function DashboardPage() {
  return <p>Welcome to the Dashboard!</p>;
}
```

의 구조에서 DashboardPage가 렌더링될 때, 최종적으로 다음과 같은 구조로 렌더링된다:
```javascript
<html>
  <body>
    <header>App Header</header>
    <main>
      <div>
        <h2>Dashboard Menu</h2>
        <div>
          <p>Welcome to the Dashboard!</p>
        </div>
      </div>
    </main>
    <footer>App Footer</footer>
  </body>
</html>
```


### 4.로딩 및 에러 상태 처리:
로딩 UI와 에러 경계를 설정하여 사용자에게 더 나은 피드백을 제공한다. 페이지 전환 시 로딩 상태를 표시하거나, 특정 페이지에서 발생하는 에러를 처리할 수 있다.

1.사용자가 /user/[id] 경로에 접근 <br/>
2.먼저 loading.js가 렌더링되어 로딩 중 메시지를 표시 <br/>
3.데이터가 성공적으로 로드되면 UserPage가 렌더링 <br/>
4.데이터 로드 중 에러가 발생하면 error.js가 렌더링되어 에러 메시지를 표시 <br/>


```javascript
app/
└── user/
    ├── loading.js
    ├── error.js
    └── [id]/page.js
```

```javascript
//app/user/loading.js:
export default function Loading() {
  return <p>Loading user data...</p>;
}

```

```javascript
//app/user/error.js:
export default function Error() {
  return <p>Failed to load user data.</p>;
}

```

```javascript
//app/user/[id]/page.js:
export default async function UserPage({ params }) {
  const userId = params.id;
  const userData = await fetchUserData(userId); // 사용자 데이터 가져오기

  if (!userData) {
    throw new Error('User not found'); // 에러 발생
  }

  return <div>{userData.name}</div>;
}

```

## Page Router
페이지 라우터는 Next.js의 전통적인 라우팅 시스템 방식이다.

### 1.파일 기반 라우팅:
pages 디렉토리 내의 파일 구조에 따라 자동으로 라우트가 생성된다. 예를 들어, pages/contact.js 파일은 /contact 경로에 매핑된다.

### 2.정적 및 동적 페이지 생성:
정적 사이트 생성(SSG) 및 서버 사이드 렌더링(SSR)을 통해 페이지를 생성할 수 있다. getStaticProps, getServerSideProps를 사용하여 데이터를 fetch 한다.

**정적 사이트 생성 (SSG)**
getStaticProps를 사용하여 정적 페이지를 생성하는 예시이다. 이 방법은 빌드 시 데이터를 미리 가져와서 정적 HTML 파일로 생성한다.

```javascript
// 정적 페이지 생성 (SSG)
export default function PostsPage({ posts }) {
  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}

// 빌드 시 데이터를 가져옴
export async function getStaticProps() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts, // 페이지에 전달할 데이터
    },
  };
}
```

**정적 사이트 생성 (SSG)**
getServerSideProps를 사용하여 서버 사이드에서 데이터를 가져오는 예시이다. 이 방법은 페이지 요청 시마다 서버에서 데이터를 가져온다.

```javascript
// 서버 사이드 렌더링 (SSR)
export default function PostPage({ post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  );
}

// 페이지 요청 시 데이터를 가져옴
export async function getServerSideProps({ params }) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${params.id}`);
  const post = await res.json();

  return {
    props: {
      post, // 페이지에 전달할 데이터
    },
  };
}

```

**3.API Routes:** <br/>
페이지 라우터는 API 라우트를 지원하여, 서버 측에서 데이터를 처리할 수 있다. pages/api 디렉토리 내에 API 핸들러를 정의하여 RESTful API를 구축할 수 있다.

```javascript
pages/
└── api/
    └── posts.js
```

```javascript
// 포스트 API 핸들러
export default function handler(req, res) {
  // GET 요청 처리
  if (req.method === 'GET') {
    const posts = [
      { id: 1, title: 'First Post', content: 'This is the first post.' },
      { id: 2, title: 'Second Post', content: 'This is the second post.' },
    ];
    res.status(200).json(posts); // 포스트 데이터 반환
  } else {
    // 다른 HTTP 메서드에 대한 처리
    res.setHeader('Allow', ['GET']);
    res.status(405).end(`Method ${req.method} Not Allowed`);
  }
}
```

```javascript
import { useEffect, useState } from 'react';

export default function HomePage() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    // 포스트 데이터 가져오기
    fetch('/api/posts')
      .then(response => response.json())
      .then(data => setPosts(data));
  }, []);

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}

```

**4.Custom 404 페이지:** <br/>
사용자가 잘못된 경로로 접근할 경우 표시할 커스텀 404 페이지를 쉽게 설정할 수 있다. pages/404.js 파일을 생성하여 사용자 정의 에러 페이지를 제공한다.

**5.Link 컴포넌트:** <br/>
내비게이션을 위한 Link 컴포넌트를 사용하여 페이지 간의 전환을 부드럽게 처리할 수 있다. Link 컴포넌트는 페이지 전환 시 성능을 최적화 한다.
