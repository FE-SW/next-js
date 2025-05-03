# 1.Turbopack

Turbopack은 Next.js의 개발 팀에 의해 만들어진 새로운 JavaScript 번들러로, 웹 애플리케이션의 빌드 성능을 크게 향상시키는 것을 목표로 하고 있다. 
빠른 빌드 속도, HMR 지원, Zero Configuration 등 여러 강력한 기능을 통해 개발자 경험을 향상시키고, 더 나은 개발 환경을 제공한다.

### (1). 빠른 빌드 성능:
Turbopack은 Rust로 작성된 번들러로, 빠른 빌드 성능을 제공한다. Webpack에 비해 훨씬 더 빠른 속도로 파일을 처리할 수 있으며, 이를 통해 개발자들이 더 효율적으로 작업할 수 있도록 돕는다.

### (2). HMR(Hot Module Replacement):
Turbopack은 HMR을 지원하여, 코드 변경 시 전체 페이지를 새로 고침하지 않고도 변경 사항을 즉시 반영할 수 있다. 이는 개발 중에 더 빠른 피드백 루프를 제공한다.

### (3). Incremental Build:
Turbopack은 증분 빌드를 지원하여, 변경된 파일만 다시 빌드한다. 이를 통해 전체 빌드 시간을 줄이고, 대규모 프로젝트에서 특히 유용하다.

### (4). Zero Configuration:
Turbopack은 가능한 한 많은 기본 설정을 제공하여, 개발자가 복잡한 설정 없이 쉽게 사용할 수 있도록 한다. 즉, 기본적인 사용에 있어 별도의 설정 없이도 작동한다.
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

# 2.App Router
Next.js 13에서 도입된 새로운 라우팅 시스템으로, app/ 디렉토리 구조를 기반으로 한다. 서버 컴포넌트와 클라이언트 컴포넌트를 혼합하여 사용할 수 있다.

### (1).디렉토리 기반 라우팅:
앱 라우터는 파일 시스템을 기반으로 한 라우팅 방식을 사용한다. app 디렉토리 내에 생성된 파일과 폴더 구조에 따라 자동으로 라우트가 생성된다.
예를 들어, app/about/page.js 파일은 /about 경로에 매핑된다.


### (2).서버 컴포넌트와 클라이언트 컴포넌트:
앱 라우터는 서버 컴포넌트와 클라이언트 컴포넌트를 혼합하여 사용할 수 있다. 서버 컴포넌트는 서버에서 렌더링되고, 클라이언트 컴포넌트는 클라이언트에서 실행된다.
이를 통해 데이터 가져오기와 UI 렌더링을 최적화할 수 있다.

```javascript
app/
├── layout.js
├── ClientComponent.js
├── ServerComponent.js
└── page.js
```

```javascript
// layout.js (서버 컴포넌트)
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

//page.js (서버 컴포넌트)
import ServerComponent from './ServerComponent';
import ClientComponent from './ClientComponent';

export default function HomePage() {
  return (
    <div>
      <h1>Home Page</h1>
      <ServerComponent />
      <ClientComponent />
    </div>
  );
}

export default async function ServerComponent() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts/1');
  const data = await res.json();

  return (
    <div>
      <h2>Server-Side Data</h2>
      <p>Title: {data.title}</p>
      <p>Body: {data.body}</p>
    </div>
  );
}

'use client';

export default function ClientComponent() {
  return <p>This is a client-side rendered component.</p>;
}
```

### (3).중첩 라우팅:
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
### (4).병렬 라우팅:
병렬 라우팅은 여러 개의 라우트를 동시에 로드하고 렌더링할 수 있는 기능이다. 이를 통해 서로 다른 레이아웃이나 페이지를 독립적으로 관리하면서도 동시에 표시할 수 있다. 이 방식은 복잡한 사용자 인터페이스를 구성할 때 유용하며, 다양한 상태를 동시에 보여줄 수 있다.
병렬 라우팅은 대시보드와 같은 복잡한 인터페이스에서 유용하다. 예를 들어, 대시보드의 왼쪽 사이드바와 오른쪽 콘텐츠 영역을 독립적으로 로드하고 업데이트할 수 있다. 이를 통해 사용자 경험을 향상시키고, 각 부분의 상태를 개별적으로 관리할 수 있다.
병렬 라우팅을 구현하기 위해서는 라우트 그룹을 정의해야 한다. 이때, 폴더 이름 앞에 @ 기호를 붙여야 한다. @로 시작하는 폴더는 병렬 라우트 그룹으로 인식되며, 이 그룹 내의 모든 페이지가 동시에 로드될 수 있다.

```javascript
/app
  ├── @dashboard
  │   ├── layout.tsx
  │   ├── page.tsx
  │   └── sidebar
  │       └── page.tsx
  ├──@settings
  │   ├── layout.tsx
  │   └── page.tsx
  ├──layout.tsx


@dashboard: 대시보드와 관련된 페이지와 컴포넌트를 포함하는 병렬 라우트 그룹.
@settings: 설정과 관련된 페이지와 컴포넌트를 포함하는 병렬 라우트 그룹.
```

```javascript
// app/@dashboard/layout.js
import Sidebar from './sidebar/page';

export default function DashboardLayout({ children }) {
  return (
    <div>
      <Sidebar /> {/* 사이드바 컴포넌트 */}
      <main>{children}</main> {/* 메인 콘텐츠 */}
    </div>
  );
}
```

```javascript
// app/@settings/layout.js
export default function SettingsLayout({ children }) {
  return (
    <div>
      <h2>Settings Header</h2>
      <main>{children}</main> {/* 메인 콘텐츠 */}
    </div>
  );
}
```

```javascript
// app/layout.js

Case.1
export default function RootLayout({ children }) {
  return (
    <div>
      <h1>Application Header</h1>
      <div className="main-content">
        {children} {/* 자식 컴포넌트 */}
      </div>
    </div>
  );
}

Case.2
export default function RootLayout({ dashboard, settings }) {
  return (
    <div>
      <h1>Application Header</h1>
      <div className="main-content">
        <div className="dashboard-section">
          {dashboard} {/* 대시보드 내용 */}
        </div>
        <div className="settings-section">
          {settings} {/* 설정 내용 */}
        </div>
      </div>
    </div>
  );
}

```

#### 동작 방식

병렬 라우팅:
사용자가 /dashboard 또는 /settings 경로로 접근할 때, 해당 라우팅 그룹의 레이아웃과 페이지가 동시에 렌더링된다.

```javascript
Case.1

* Dashboard:
/dashboard로 접근하면 DashboardLayout이 렌더링되고, 그 안에 Sidebar와 Main Dashboard Content가 표시

* 결과:
Application Header
Sidebar Content
Main Dashboard Content

==========================================================================================

* Settings:
/settings로 접근하면 SettingsLayout이 렌더링되고, 그 안에 Settings Header와 Settings Content가 표시.

* 결과:
Application Header
Settings Header
Settings Content
```

```javascript
Case.2
/dashboard 또는 /settings로 접근하면

결과:
Application Header
Dashboard Content (대시보드 관련 내용)
Settings Content (설정 관련 내용)
```

#### 중첩 라우팅 vs 병렬 라우팅 차이점 요약

| 항목                       | 중첩 라우팅 (Nested Routing)                     | 병렬 라우팅 (Parallel Routing)                     |
|--------------------------|------------------------------------------------|------------------------------------------------|
| **구조**                   | 부모와 자식 관계를 통해 레이아웃을 중첩함         | 여러 경로를 독립적으로 동시에 로드함              |
| **렌더링 방식**            | 자식 라우트가 부모 라우트의 레이아웃을 상속받아 함께 렌더링 | 각 라우트가 독립적으로 렌더링되며 동시에 표시됨     |
| **사용 사례**              | 같은 레이아웃을 공유하는 여러 페이지가 있을 때 사용    | 복잡한 UI를 구성할 때, 여러 상태를 동시에 보여줄 때 사용 |
| **재사용성**               | 레이아웃과 UI 요소의 재사용이 용이                   | 각 부분이 독립적으로 관리되므로 유연성이 높음       |

### (5).서버 컴포넌트 로딩 및 에러 상태 처리:
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

### (6).app router에서 에러페이지 설정

#### 전역적으로 통일된 에러 페이지 만들기
```javascript
app/
└── (error)/
    ├── error.js
    └── not-found.js


// app/(error)/error.js
export default function GlobalError({ error, reset }) {
  return (
    <div>
      <h1>오류가 발생했습니다!</h1>
      <p>{error.message}</p>
      <button onClick={() => reset()}>다시 시도</button>
    </div>
  );
}

// app/(error)/not-found.js
export default function NotFoundPage() {
  return (
    <div>
      <h1>404 - 페이지를 찾을 수 없습니다</h1>
      <p>요청하신 페이지가 존재하지 않습니다.</p>
    </div>
  );
}
```

#### 경로마다 별도로 에러 페이지 만들기
```javascript
app/
└── user/
    ├── error.js
    ├── not-found.js
    └── [id]/
        └── page.js

// app/user/error.js
export default function UserError({ error, reset }) {
  return (
    <div>
      <h1>사용자 페이지 오류</h1>
      <p>{error.message}</p>
      <button onClick={() => reset()}>다시 시도</button>
    </div>
  );
}

// app/user/not-found.js
export default function UserNotFoundPage() {
  return (
    <div>
      <h1>404 - 사용자 페이지를 찾을 수 없습니다</h1>
      <p>요청하신 사용자 페이지가 존재하지 않습니다.</p>
    </div>
  );
}
```


# 3.Page Router
페이지 라우터는 Next.js의 전통적인 라우팅 시스템 방식이다. 파일기반으로 라우팅되며, pages 디렉토리 내의 파일 구조에 따라 자동으로 라우트가 생성된다. getStaticProps, getServerSideProps와 같은 데이터 패칭 메서드를 사용한다.



### (1).SSR
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

### (2).SSG
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

### (3).ISR
Next.js에서 정적 사이트 생성(SSG)의 장점을 유지하면서도, 페이지를 주기적으로 업데이트할 수 있는 기능을 제공한다. ISR을 사용하면 정적 페이지를 생성한 후, 지정된 시간 간격으로 백그라운드에서 페이지를 재생성할 수 있다.
"백그라운드에서 재생성"된다는 것은 해당 페이지가 요청될 때마다 백그라운드에서 재생성된다는 의미이다. 즉, 페이지가 실제로 요청될 때만 재생성이 트리거된다.

```javascript
// 증분 정적 재생성 (ISR)
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

// 빌드 시 데이터를 가져오고, 주기적으로 재생성
export async function getStaticProps() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts, // 페이지에 전달할 데이터
    },
    revalidate: 60, // 60초마다 페이지를 백그라운드에서 재생성
  };
}
```

#### ISR 동작방식

ex) revaludate 60으로 설정
* 첫 번째 방문: 페이지가 처음 요청되면, Next.js는 빌드 시점에 생성된 정적 HTML을 제공
* 50초 동안 머무름: 이 동안에는 페이지가 그대로 유지된다. revalidate 시간이 아직 지나지 않았기 때문에, 페이지는 여전히 초기 상태
* 페이지를 떠남: 페이지를 떠나도, Next.js는 백그라운드에서 아무 작업도 하지 않다. 재생성은 페이지 요청에 의해 트리거된다.
* 다음 방문: 페이지를 다시 방문하면, Next.js는 revalidate 시간이 지났는지 확인한다.
* 10초 후 재생성: 두 번째 방문 시점에서 60초가 지났다면, Next.js는 백그라운드에서 페이지를 재생성한다. 이때, 사용자는 여전히 기존의 정적 페이지를 보게 된다.
* 업데이트된 페이지 제공: 백그라운드 재생성이 완료된 후, 그 다음 요청부터는 업데이트된 페이지가 제공된다.
따라서, revalidate 시간이 지난 후에 페이지가 요청될 때만 백그라운드에서 재생성이 이루어진다. 이 과정은 사용자가 페이지를 떠나거나 머무르는 것과는 무관하게, 페이지 요청에 의해 트리거된다.

### (4).페이지 랜더링 방식

#### 서버 사이드 렌더링(SSR) - getServerSideProps 사용 시:
* 첫 번째 방문: 페이지가 서버에서 렌더링되어 클라이언트로 전송
* 두 번째 및 이후 방문: 매 요청마다 서버에서 다시 렌더링함. Next.js 자체적으로는 캐싱을 하지 않지만(매번 페이지 요청마다 fetch call 수행) 별도의 캐싱로직을 구현할 수 있다.

#### 정적 사이트 생성(SSG) - getStaticProps 사용 시:
* 첫 번째 방문: 빌드 시점에 미리 렌더링된 정적 HTML 파일이 제공
* 두 번째 및 이후 방문: 동일한 정적 HTML 파일이 서버나 CDN에서 제공되어 매우 빠르게 로드

#### 클라이언트 사이드 렌더링 - ueEffect fetch:
* 컴포넌트 내부에서 useEffect 같은 훅을 사용하여 데이터를 가져오는 경우, 빌드 시점에 미리 렌더링된 정적 HTML을 가져오고, 이후 데이터 패칭은 클라이언트에서 이루어진다. 페이지 자체는 정적으로 제공되지만, 데이터 업데이트는 클라이언트에서 처리된다.

#### 증분 정적 재생성(ISR) - getStaticProps + revalidate 사용 시:
* 첫 번째 방문: 빌드 시점에 미리 렌더링된 정적 HTML 파일이 제공됨.
* 두 번째 및 이후 방문: 지정된 revalidate 시간 간격에 따라 백그라운드에서 페이지가 재생성됨. 이로 인해 최신 데이터를 주기적으로 반영할 수 있음.

#### 그외 - fetch가 없는 코드
* 정적 파일로 처리: API 호출이나 동적 데이터 패칭이 없는 경우, Next.js는 해당 페이지를 정적 파일로 처리한다. 이는 SSG의 기본적인 동작 방식과 동일하며, 페이지는 정적으로 제공된다.


#### 요약
* SSR: 매 요청마다 서버에서 렌더링되며, 별도의 캐싱 로직이 없으면 매번 API 호출이 발생
* SSG: 정적 HTML 파일로 제공되어 빠르게 로드
* ISR: 정적 HTML 파일로 제공되며, 지정된 간격으로 백그라운드에서 페이지가 재생성됨
* CSR: 클라이언트에서 데이터를 패칭하고 업데이트
* 정적 콘텐츠: API 호출이 없는 경우, 정적 파일로 제공

### (5).API Routes:
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

### (6).Page Router에서의 에러 페이지 설정
page router에서는 app router와는 다르게 통일된 에러페이지만 사용할 수 있다

```javascript
pages/
├── 404.js        // 커스텀 404 페이지
└── _error.js     // 서버 장애 및 기타 에러 페이지

// pages/404.js
export default function Custom404() {
  return <h1>404 - 페이지를 찾을 수 없습니다</h1>;
}

// pages/_error.js
export default function Error({ statusCode }) {
  return (
    <p>
      {statusCode
        ? `서버에서 ${statusCode} 오류가 발생했습니다.`
        : '클라이언트에서 오류가 발생했습니다.'}
    </p>
  );
}

Error.getInitialProps = ({ res, err }) => {
  const statusCode = res ? res.statusCode : err ? err.statusCode : 404;
  return { statusCode };
};
```

### (7).Link 컴포넌트
내비게이션을 위한 Link 컴포넌트를 사용하여 페이지 간의 전환을 부드럽게 처리할 수 있다. Link 컴포넌트는 페이지 전환 시 성능을 최적화 한다.

#### 프리페칭(Pre-fetching):
* 설명: Link 컴포넌트는 사용자가 링크에 마우스를 올리거나 페이지가 로드될 때, 해당 링크가 가리키는 페이지의 데이터를 미리 가져온다.
* 장점: 페이지 전환 시 필요한 데이터를 미리 로드하여, 사용자가 링크를 클릭했을 때 즉시 페이지가 로드되도록 한다. 이는 페이지 전환 속도를 크게 향상시킨다.

#### 클라이언트 사이드 네비게이션:
* 설명: Link 컴포넌트를 사용하면 페이지 전환이 클라이언트 측에서 이루어진다. 이는 전체 페이지를 다시 로드하지 않고, 필요한 부분만 업데이트한다.
* 장점: 전체 페이지를 다시 로드하지 않기 때문에, 네트워크 요청과 브라우저의 렌더링 작업이 줄어들어 성능이 향상된다.
* 특징: Link 컴포넌트를 사용한 페이지 전환은 클라이언트 사이드 네비게이션을 제공하지만, 페이지가 SSR로 설정되어 있다면 여전히 서버에서 데이터를 가져와 렌더링한다.

#### 자동 코드 분할:
* 설명: Next.js는 각 페이지를 별도의 청크로 분할하여, 필요한 페이지의 코드만 로드한다.
* 장점: 초기 로딩 시 불필요한 코드를 로드하지 않으므로, 페이지 로딩 속도가 빨라지고, 사용자가 필요로 하는 페이지의 코드만 로드하여 메모리 사용량을 줄인다.

#### 내장된 라우팅 시스템:
* 설명: Next.js의 라우팅 시스템은 Link 컴포넌트를 통해 페이지 전환을 최적화한다.
* 장점: 내장된 라우팅 시스템은 브라우저의 기본 동작을 대체하여, 페이지 전환 시 URL을 업데이트하고, 히스토리를 관리한다.

#### SEO 최적화:
* 설명: Link 컴포넌트는 SEO를 고려하여 설계되었다. 페이지 전환 시 URL이 적절하게 업데이트되며, 검색 엔진이 페이지 구조를 쉽게 이해할 수 있도록 돕는다.
* 장점: 검색 엔진 최적화(SEO)에 유리하며, 검색 엔진이 페이지를 더 잘 크롤링할 수 있도록 한다.

# 4.보호된 파일
보호된 파일(Protected Files)은 각 파일이 특정한 역할을 수행하며, 애플리케이션의 구조와 동작 방식을 정의하는 중요한 요소들이다.

### (1).App Router

#### page.js
* **설명**: 해당 라우팅 경로에 접속했을 때 기본적으로 표시되는 유니크한 페이지를 정의한다.
* **예시**: `app/dashboard/page.js`는 `/dashboard` 경로에 접속할 때 보여지는 대시보드 페이지이다.

#### layout.js
* **설명**: 여러 하위 페이지에 공통적으로 적용되는 레이아웃을 정의하는 파일
* **예시**: `app/dashboard/layout.js`는 대시보드 하위 페이지들(예: `/dashboard/overview`, `/dashboard/settings`)에 공통적으로 적용되는 레이아웃을 제공
* **template 차이점**: 페이지 이동 시 유지된다. 즉, 레이아웃은 한 번 렌더링된 후 페이지 전환 시에도 다시 렌더링되지 않는다.

#### not-found.js
* **설명**: 존재하지 않는 페이지에 접근했을 때 표시되는 오류 페이지를 정의
* **예시**: 사용자가 `/invalid-route`에 접근할 때 보여지는 "페이지를 찾을 수 없습니다" 메시지를 포함

#### error.js
* **설명**: 예상치 못한 오류가 발생했을 때 표시되는 오류 페이지를 정의
* **예시**: 서버 오류가 발생했을 때 사용자에게 보여주는 오류 메시지를 포함

#### loading.js
* **설명**: 페이지 로딩 중에 표시되는 로딩 페이지를 정의한다.
* **예시**: 데이터가 로드되는 동안 사용자가 볼 수 있는 스피너나 로딩 메시지를 포함

#### route.js
* **설명**: 서버 측 라우팅을 위한 코드를 포함하는 파일이다. API 라우트를 정의하는 데 사용
* **예시**: `app/api/users/route.js`는 사용자 정보를 가져오는 API 엔드포인트를 정의

```javascript
// app/api/users/route.js

// 사용자 정보를 가져오는 GET 요청 핸들러
export async function GET(req) {
  const users = await fetch('https://api.example.com/users').then(res => res.json());
  return new Response(JSON.stringify(users), {
    headers: { 'Content-Type': 'application/json' },
  });
}

// 새로운 사용자를 추가하는 POST 요청 핸들러
export async function POST(req) {
  const newUser = await req.json();
  // 데이터베이스에 사용자를 저장하는 가상의 함수
  const savedUser = await saveUserToDatabase(newUser);
  return new Response(JSON.stringify(savedUser), {
    headers: { 'Content-Type': 'application/json' },
  });
}
```

#### template.js
* **설명**: 페이지 템플릿을 정의하는 파일
* **예시**: 여러 페이지에 공통적으로 적용되는 템플릿을 정의하여 재사용성을 높인다.
* **layout 차이점**: 페이지 이동할 때마다 새롭게 리렌더링된다. 매 페이지 전환마다 템플릿이 다시 생성된다.

```javascript
// app/template.js

export default function Template({ children, title }) {
  return (
    <div>
      <header>
        <h1>{title}</h1>
        <nav>
          <a href="/">홈</a>
          <a href="/about">소개</a>
        </nav>
      </header>
      <main>{children}</main>
      <footer>
        <p>&copy; 2023 My Company</p>
      </footer>
    </div>
  );
}
```

### (2).Page Router

#### _app.js
* **설명**: Next.js 애플리케이션의 전역 레이아웃을 정의하는 파일이다. 모든 페이지에 공통적으로 적용되는 설정이나 레이아웃을 설정한다.
* **예시**: 전역 스타일, 테마, 상단 내비게이션 바 등을 포함할 수 있다.

#### _document.js
* **설명**: HTML 문서의 구조를 커스터마이즈할 수 있는 파일이다. 서버에서만 렌더링되며, 클라이언트 측에서는 실행되지 않는다.
* **예시**: `<html>`, `<head>`, `<body>` 태그를 커스터마이즈할 수 있다.

#### 404.js
* **설명**: 존재하지 않는 페이지에 접근했을 때 표시되는 404 오류 페이지를 정의한다.
* **예시**: 사용자가 잘못된 URL에 접근했을 때 보여지는 "페이지를 찾을 수 없습니다" 메시지를 포함한다.

#### 500.js
* **설명**: 서버 오류가 발생했을 때 표시되는 500 오류 페이지를 정의한다.
* **예시**: 서버에서 예기치 않은 오류가 발생했을 때 사용자에게 보여주는 오류 메시지를 포함한다.

#### _error.js
* **설명**: Next.js에서 모든 오류 페이지를 커스터마이즈할 수 있는 파일이다. 404, 500과 같은 특정 오류 페이지를 정의하지 않았을 때 기본적으로 사용된다.
* **예시**: 모든 종류의 오류에 대해 사용자에게 보여줄 기본 오류 메시지를 포함한다.

```javascript
// pages/_error.js

function Error({ statusCode }) {
  return (
    <p>
      {statusCode
        ? `서버에서 ${statusCode} 오류가 발생했습니다.`
        : '클라이언트에서 오류가 발생했습니다.'}
    </p>
  );
}

Error.getInitialProps = ({ res, err }) => {
  const statusCode = res ? res.statusCode : err ? err.statusCode : 404;
  return { statusCode };
};
```

#### getStaticProps.js
* **설명**: 정적 사이트 생성(SSG)을 위해 서버 측에서 데이터를 패치하고 페이지를 렌더링하는 함수를 정의한다.
* **예시**: 빌드 시 API에서 데이터를 가져와 정적 페이지를 생성한다.

#### getServerSideProps.js
* **설명**: 서버 측에서 데이터를 패치하고 페이지를 렌더링하기 전에 실행되는 함수를 정의한다.
* **예시**: 사용자의 요청에 따라 실시간 데이터를 가져와 페이지를 생성한다.

### (3).공통

#### middleware.js
* **설명**: 페이지 요청 및 응답을 가로채고 처리하는 미들웨어 함수를 포함하는 파일
* **예시**: 인증 확인, 로깅 또는 요청 변환 등의 작업을 수행할 수 있다.

# 5.이미지 최적화
Next.js에서 제공하는 Image 컴포넌트는 이미지 최적화를 위해 여러 가지 기능을 지원한다. 이를 통해 성능을 향상시키고 사용자 경험을 개선할 수 있다.

### (1).지연 로딩 (Lazy Loading):
loading="lazy" 속성을 통해 이미지가 뷰포트에 들어오기 전까지 로드되지 않도록 설정할 수 있다. 이는 초기 페이지 로딩 속도를 개선하고, 사용자에게 필요한 이미지만 로드하여 대역폭을 절약한다.

### (2).자동 크기 조정:
srcset 속성은 다양한 화면 크기에 맞게 이미지를 자동으로 조정한다. 예를 들어, 모바일 기기와 데스크톱 기기에서 각각 최적의 크기의 이미지를 로드하도록 지원한다.

### (3).포맷 변환:
Next.js는 브라우저의 지원 여부에 따라 이미지를 .webp와 같은 최적화된 포맷으로 변환하여 제공할 수 있다. 이는 이미지 로딩 속도를 더 개선할 수 있다.

### (4).우선순위 설정:
priority 속성을 사용하면 특정 이미지를 우선적으로 로드할 수 있다. 이 속성이 설정된 이미지는 지연 로딩이 적용되지 않으며, 페이지가 로드될 때 즉시 렌더링된다.

### (5).개발자 도구에서의 렌더링
```javascript
import Image from 'next/image';
import Link from 'next/link';
import classes from './main-header.module.css';

export default function MainHeader() {
  return (
    <header>
      <Link className={classes.logo} href="/">
        <Image
          src={logoImg}
          alt="A plate with food on it"
          loading="lazy" // 지연 로딩 설정
          width={1024} // 이미지 너비
          height={1024} // 이미지 높이
          priority // 우선 로딩 설정
        />
        NextLevel Food
      </Link>
    </header>
  );
}
```
개발자 도구에서 렌더링된 <img> 요소를 보면 다음과 같은 속성을 확인할 수 있다 <br/>
* loading="lazy": 지연 로딩이 적용되었음을 나타낸다.
* srcset: 다양한 해상도에 맞는 이미지를 제공하는 속성이다.
* src: 최적화된 이미지의 URL이 포함되어 있다.

# 6.Server Actions
Server Actions는 Next.js 13에서 도입된 기능으로, 클라이언트에서 서버의 함수를 직접 호출할 수 있게 해주는 기능이다.

```javascript
// app/actions.js
'use server'

export async function createTodo(formData) {
  const title = formData.get('title')
  await db.todo.create({ title })
}
```

```javascript
// app/components/TodoForm.js
'use client'

import { createTodo } from '../actions'

export default function TodoForm() {
  return (
    <form action={createTodo}>
      <input type="text" name="title" />
      <button type="submit">Add Todo</button>
    </form>
  )
}
```
### (1).장점:
* 보안: 서버 코드가 클라이언트에 노출되지 않음
* 성능: 클라이언트-서버 통신 최적화
* 개발 경험: API 엔드포인트 생성 없이 서버 함수 직접 호출

### (2).사용 사례:
* 폼 제출
* 데이터베이스 조작
* 파일 업로드
* 인증/인가 처리

# 7.Metadata API
Metadata API는 Next.js 13에서 도입된 새로운 메타데이터 관리 시스템으로, SEO와 소셜 미디어 공유를 위한 메타데이터를 더 쉽게 관리할 수 있게 해준다.
Metadata API를 사용할 때, 메타데이터를 설정하기 위해 export만 해주면 된다. Next.js는 이 메타데이터를 자동으로 처리하여 HTML 문서의 <head>에 반영한다. 따라서, 별도의 추가 작업 없이도 메타데이터가 페이지에 적용된다.

### (1).정적 메타데이터
```javascript
// app/layout.js
export const metadata = {
  title: 'My Website',
  description: 'Welcome to my website',
  openGraph: {
    title: 'My Website',
    description: 'Welcome to my website',
    images: [
      {
        url: 'https://example.com/og.jpg',
        width: 1200,
        height: 630,
      },
    ],
  },
  twitter: {
    card: 'summary_large_image',
    title: 'My Website',
    description: 'Welcome to my website',
    images: ['https://example.com/og.jpg'],
  },
}

export default function Layout({ children }) {
  return (
    <div>
      <header>
        <h1>My Website</h1>
      </header>
      <main>{children}</main>
      <footer>
        <p>&copy; 2023 My Website</p>
      </footer>
    </div>
  );
}

// app/page.js
import Layout from './layout';

export default function HomePage() {
  return (
    <Layout>
      <h2>Welcome to the Home Page</h2>
      <p>This is the main content of the home page.</p>
    </Layout>
  );
}
```

### (2).동적 메타데이터
Next.js 13부터는 generateMetadata 함수를 페이지 컴포넌트와 같은 파일에 export하기만 하면 자동으로 메타데이터가 적용된다.

```javascript
// app/products/[id]/page.tsx
export async function generateMetadata({ params }) {
  const post = await getPost(params.id)
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.ogImage],
    },
    twitter: {
      card: 'summary_large_image',
      title: post.title,
      description: post.excerpt,
      images: [post.ogImage],
      creator: '@yourTwitterHandle',   
      site: '@yourWebsiteTwitter',     
    },
  }
}

export default function ProductPage({ params }) {
  const [product, setProduct] = useState(null);

  useEffect(() => {
    async function fetchProduct() {
      const data = await getPost(params.id);
      setProduct(data);
    }
    fetchProduct();
  }, [params.id]);

  if (!product) return <p>Loading...</p>;

  return (
    <div>
      <h1>{product.title}</h1>
      <p>{product.description}</p>
      <img src={product.ogImage} alt={product.title} />
    </div>
  );
}
```

* 1.Next.js가 자동으로 generateMetadata 함수를 인식
* 2.페이지가 요청될 때 메타데이터를 생성
* 3.생성된 메타데이터를 페이지의 <head> 섹션에 자동으로 주입

별도의 호출이나 설정 없이도 메타데이터가 적용된다. 이는 Next.js의 파일 기반 라우팅 시스템과 자동 메타데이터 주입 기능 덕분이다.

# 8.Streaming SSR
Streaming SSR은 Next.js 13에서 도입된 기능으로, 서버에서 렌더링된 HTML을 점진적으로 클라이언트에 전송하는 방식이다.
설명만 보면 기존의 SSR과의 차이점을 느끼기가 힘든데, 차이점은 아래와 같다.

### (1).기존 SSR
```javascript
// 기존 SSR 방식
export default async function ProductPage() {
  // 모든 데이터를 기다린 후에야 페이지 렌더링 시작
  const product = await fetchProduct()     // 1초 소요
  const reviews = await fetchReviews()     // 2초 소요
  const recommendations = await fetchRecommendations() // 3초 소요

  // 총 6초 후에야 사용자에게 페이지가 보임
  return (
    <div>
      <ProductInfo product={product} />
      <Reviews reviews={reviews} />
      <Recommendations recommendations={recommendations} />
    </div>
  )
}
```
### (2).Streaming SSR

```javascript
// Streaming SSR 방식
import { Suspense } from 'react'

export default async function ProductPage() {
  // ProductInfo는 즉시 렌더링 (1초)
  const product = await fetchProduct()

  return (
    <Suspense fallback={<div>Loading entire page...</div>}>
      <div>
        <ProductInfo product={product} />
        
        <Suspense fallback={<ReviewsSkeleton />}>
          <Reviews />
        </Suspense>

        <Suspense fallback={<RecommendationsSkeleton />}>
          <Recommendations />
        </Suspense>
      </div>
    </Suspense>
  )
}
```

### (3).렌더링 방식
* 기존 SSR: 모든 데이터가 준비될 때까지 기다린 후 한 번에 페이지 전체를 렌더링
* Streaming SSR: 준비된 컴포넌트부터 순차적으로 렌더링하고 스트리밍

### (4).사용자 경험
```javascript
// 기존 SSR의 타임라인
0초: 빈 화면
6초: 전체 페이지 표시

// Streaming SSR의 타임라인
0초: 빈 화면 또는 서버에서 준비된 부분만 표시
1초: ProductInfo 표시
2초: Reviews 로딩 UI 표시 → Reviews 표시
3초: Recommendations 로딩 UI 표시 → Recommendations 표시
```

### (5).시각적 비교
```javascript
// 기존 SSR
┌────────────────────┐
│                    │
│        빈화면        │ (6초 동안)
│                    │
└────────────────────┘
           ↓
┌────────────────────┐
│    Product Info    │
│      Reviews       │ (6초 후 한번에 표시)
│  Recommendations   │
└────────────────────┘

// Streaming SSR
┌────────────────────┐
│    Product Info    │ (1초)
│    Loading...      │
│    Loading...      │
└────────────────────┘
           ↓
┌────────────────────┐
│    Product Info    │
│     Reviews        │ (2초)
│    Loading...      │
└────────────────────┘
           ↓
┌────────────────────┐
│    Product Info    │
│     Reviews        │ (3초)
│  Recommendations   │
└────────────────────┘
```

### (86.Streaming SSR에서 Suspense의 역할
* 점진적 렌더링: Suspense를 사용하면 페이지의 일부를 먼저 렌더링하고, 나머지 부분은 데이터가 준비되는 대로 렌더링할 수 있다. 이는 사용자에게 더 빠른 초기 로딩 경험을 제공한다.
* 비동기 데이터 로딩: Suspense는 비동기적으로 데이터를 로딩하는 컴포넌트를 감싸고, 데이터가 준비될 때까지 대체 UI(예: 로딩 스피너)를 보여줄 수 있다.
* 사용자 경험 개선: 페이지의 중요한 부분을 먼저 보여주고, 덜 중요한 부분은 나중에 로딩함으로써 사용자 경험을 개선할 수 있다.

# 9.Hydration
하이드레이션은 서버에서 렌더링된 HTML을 클라이언트 측에서 React로 활성화하는 과정이다. 이 과정은 클라이언트 측에서 React 컴포넌트가 초기화되고, 서버에서 렌더링된 HTML과 연결되어 상호작용이 가능해지는 것을 의미한다.

### (1).하이드레이션의 필요성
* 서버에서 렌더링된 HTML: 서버에서 미리 렌더링된 HTML을 클라이언트로 전송하여 초기 로드 시간을 단축하고, SEO를 개선한다.
* 클라이언트 측 상호작용: 클라이언트 측에서 React가 HTML을 하이드레이트하여, 페이지가 상호작용 가능해진다. 이는 버튼 클릭, 폼 입력 등과 같은 사용자 상호작용을 처리할 수 있게 한다.


### (2).하이드레이션이 발생하는 케이스

#### 서버 사이드 렌더링(SSR):
* 케이스: getServerSideProps를 사용하여 서버에서 페이지를 렌더링한 경우.
* 하이드레이션 과정: 서버에서 렌더링된 HTML이 클라이언트로 전송된 후, 클라이언트 측에서 React가 이 HTML을 하이드레이트하여 상호작용이 가능하게 만든다.

#### 정적 사이트 생성(SSG):
* 케이스: getStaticProps를 사용하여 빌드 시점에 정적 HTML을 생성한 경우.
* 하이드레이션 과정: 정적 HTML이 클라이언트로 전송된 후, 클라이언트 측에서 React가 이 HTML을 하이드레이트하여 상호작용이 가능하게 만든다.

#### 증분 정적 재생성(ISR):
* 케이스: getStaticProps와 revalidate를 사용하여 정적 페이지를 주기적으로 업데이트하는 경우.
* 하이드레이션 과정: ISR로 생성된 정적 HTML이 클라이언트로 전송된 후, 클라이언트 측에서 React가 이 HTML을 하이드레이트한다.

### (3).하이드레이션이 발생하지 않는 케이스

#### 클라이언트 사이드 렌더링(CSR):
* 케이스: useEffect 같은 훅을 사용하여 클라이언트 측에서 데이터를 가져오는 경우.
* 하이드레이션 과정 없음: CSR은 클라이언트에서 처음부터 렌더링되므로, 서버에서 렌더링된 HTML을 하이드레이트할 필요가 없다.

#### 정적 콘텐츠:
* 케이스: API 호출이나 동적 데이터 패칭이 없는 경우, 정적 파일로만 제공되는 경우.
* 하이드레이션 과정 없음: 정적 콘텐츠는 상호작용이 필요하지 않으므로, 하이드레이션이 필요하지 않다.(이벤트 핸들러나 상태관리를 쓰지않는경우)

### (4).추가 고려사항
* 하이드레이션 성능: 하이드레이션은 클라이언트 측에서 추가적인 JavaScript 실행을 필요로 하므로, 성능에 영향을 미칠 수 있다. 이를 최적화하기 위해 불필요한 JavaScript 로드를 줄이고, 필요한 부분만 하이드레이트하는 전략을 사용할 수 있다.
* 예를 들어, dynamic import를 사용하여 클라이언트 측에서만 필요한 컴포넌트를 로드할 수 있다.

```javascript
// pages/index.js

import dynamic from 'next/dynamic';

// 클라이언트 측에서만 로드되는 컴포넌트
const ClientOnlyComponent = dynamic(() => import('../components/ClientOnlyComponent'), {
  ssr: false, // 서버 사이드 렌더링 비활성화
});

export default function HomePage() {
  return (
    <div>
      <h1>Welcome to the Home Page</h1>
      <ClientOnlyComponent />
    </div>
  );
}
```
  
* 하이드레이션 오류: 서버에서 렌더링된 HTML과 클라이언트에서의 React 상태가 일치하지 않으면 하이드레이션 오류가 발생할 수 있다. 이를 방지하기 위해 서버와 클라이언트의 렌더링 결과가 일치하도록 주의해야 한다.


```javascript
// as-is
import { useEffect, useState } from 'react';

export default function HomePage() {
  // 클라이언트에서만 초기화되는 상태
  const [count, setCount] = useState(() => {
    // 클라이언트에서만 접근 가능한 로컬 스토리지 사용
    if (typeof window !== 'undefined') {
      const savedCount = localStorage.getItem('count');
      return savedCount ? parseInt(savedCount, 10) : 0;
    }
    return 0; // 서버에서 렌더링 시 기본값
  });

  useEffect(() => {
    // 상태가 변경될 때마다 로컬 스토리지에 저장
    localStorage.setItem('count', count);
  }, [count]);

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

```javascript
// to-be
import { useEffect, useState } from 'react';

export default function HomePage() {
  // 서버와 클라이언트에서 동일한 초기 상태를 유지
  const [count, setCount] = useState(0);

  useEffect(() => {
    // 클라이언트에서만 실행되는 로직
    const savedCount = localStorage.getItem('count');
    if (savedCount) {
      setCount(parseInt(savedCount, 10));
    }
  }, []);

  useEffect(() => {
    // 상태가 변경될 때마다 로컬 스토리지에 저장
    localStorage.setItem('count', count);
  }, [count]);

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```
# 10.Catch-All Routes
Next.js의 Catch-All Routes를 사용하여 URL 경로를 처리할 때, 인자의 개수에 따라 다양한 방식으로 경로를 매핑할 수 있다. 예를 들어, /archive와 같은 경로는 인자가 없고, /archive/2024와 같은 경로는 하나의 인자를, /archive/2024/3과 같은 경로는 두 개의 인자를 캐치할 수 있
다. 이를 통해 복잡한 URL 구조를 유연하게 처리할 수 있다.

### (1). Catch-All Route 구조
* 폴더 구조:
/pages/archive/[...params].js와 같은 구조를 사용하여 Catch-All Route를 정의한다.

* 예시 코드:
```javascript
// pages/archive/[...params].js
import { useRouter } from 'next/router';

const ArchivePage = () => {
  const router = useRouter();
  const { params } = router.query; // params는 배열입니다.

  return (
    <div>
      <h1>Archive Page</h1>
      {params ? (
        <div>
          <p>Number of parameters: {params.length}</p>
          <ul>
            {params.map((param, index) => (
              <li key={index}>{param}</li>
            ))}
          </ul>
        </div>
      ) : (
        <p>No parameters</p>
      )}
    </div>
  );
};

export default ArchivePage;
```

### (2).인자에 따른 캐치 차이

* 인자 없음 (/archive):
이 경우, params가 정의되지 않거나 비어 있는 배열이 되며, URL 경로에 아무런 추가 정보가 없음을 나타냄

* 인자 하나 (/archive/2024):
하나의 인자가 캐치된다. 이는 특정 연도(예: 2024)와 같은 의미를 가질 수 있다.

* 인자 두 개 (/archive/2024/3):
두 개의 인자가 캐치된다. 예를 들어, 첫 번째 인자는 연도(2024), 두 번째 인자는 월(3)으로 해석될 수 있다.

# 11.MiddleWare
Next.js의 미들웨어는 요청이 서버에 도달하기 전에 실행되며, 요청을 수정하거나 응답을 조작할 수 있다. 미들웨어는 프로젝트의 루트에 middleware.js 파일을 생성하여 설정할 수 있다. 이 파일은 Next.js의 모든 요청에 대해 실행한다. 
다양한 시나리오에서 유용하게 사용될 수 있으며, 애플리케이션의 보안, 성능, 사용자 경험을 개선하는 데 큰 도움이 된다. 미들웨어를 적절히 활용하여 애플리케이션의 동작을 세밀하게 제어할 수 있다.

### (1).특징
* 경량: 미들웨어는 빠르게 실행되며, 서버리스 환경에서 작동한다.
* 유연성: 요청을 가로채고, 수정하거나, 리다이렉트할 수 있다.
* 보안: 인증 및 권한 부여 로직을 구현하여 보안을 강화할 수 있다.
* HTML 응답 제한: 미들웨어는 HTML 응답을 직접 생성할 수 없습니다. 대신, 요청을 수정하거나 리다이렉트하는 데 사용된다.
* 서버리스 환경: 미들웨어는 서버리스 환경에서 실행되므로, 서버 상태를 유지할 수 없다. 모든 요청은 독립적으로 처리된다.

### (2).사용예시

#### 인증 및 권한 부여
사용자가 특정 페이지에 접근하기 전에 인증 여부를 확인하고, 인증되지 않은 사용자를 로그인 페이지로 리다이렉트할 수 있다.

```javascript
// middleware.js
import { NextResponse } from 'next/server';

export function middleware(req) {
  const token = req.cookies.get('authToken');

  if (!token) {
    const url = req.nextUrl.clone();
    url.pathname = '/login';
    return NextResponse.redirect(url);
  }

  return NextResponse.next();
}
```

#### 로깅 및 분석
모든 요청에 대해 로깅을 수행하여, 요청의 메타데이터를 기록할 수 있다. 이를 통해 사용자 행동을 분석하고, 애플리케이션의 성능을 모니터링할 수 있다.

```javascript
// middleware.js
export function middleware(req) {
  console.log(`Request URL: ${req.url}`);
  console.log(`Request Method: ${req.method}`);
  console.log(`User Agent: ${req.headers.get('user-agent')}`);

  return NextResponse.next();
}
```

#### A/B 테스트
사용자를 두 그룹으로 나누어 서로 다른 페이지를 제공함으로써, A/B 테스트를 수행할 수 있다.

```javascript
import { NextResponse } from 'next/server';

export function middleware(req) {
  const url = req.nextUrl.clone();
  const variant = Math.random() < 0.5 ? 'A' : 'B';

  if (variant === 'A') {
    url.pathname = '/variant-a';
  } else {
    url.pathname = '/variant-b';
  }

  return NextResponse.rewrite(url);
}
```

#### A/B 테스트
사용자의 IP 주소를 기반으로 지역을 감지하고, 해당 지역에 맞는 페이지로 리다이렉트할 수 있다.

```javascript
import { NextResponse } from 'next/server';

export function middleware(req) {
  const country = req.geo.country || 'US';
  const url = req.nextUrl.clone();

  if (country === 'KR') {
    url.pathname = '/ko';
  } else if (country === 'FR') {
    url.pathname = '/fr';
  } else {
    url.pathname = '/en';
  }

  return NextResponse.rewrite(url);
}
```

#### 비동기 작업 

```javascript 
// middleware.js
import { NextResponse } from 'next/server';
import { MongoClient } from 'mongodb';

const uri = 'your-mongodb-connection-string';
const client = new MongoClient(uri);

async function connectToDatabase() {
  if (!client.isConnected()) {
    await client.connect();
  }
  return client.db('your-database-name');
}

async function fetchUserFromDatabase(token) {
  const db = await connectToDatabase();
  const usersCollection = db.collection('users'); 

  const user = await usersCollection.findOne({ authToken: token });

  return user;
}

export async function middleware(req) {
  const token = req.cookies.get('authToken');

  if (!token) {
    const url = req.nextUrl.clone();
    url.pathname = '/login';
    return NextResponse.redirect(url);
  }

  const user = await fetchUserFromDatabase(token);

  if (!user) {
    const url = req.nextUrl.clone();
    url.pathname = '/login';
    return NextResponse.redirect(url);
  }

  // 사용자 정보가 유효한 경우 요청을 계속 진행
  return NextResponse.next();
}
```

### (3).미들웨어의 위치와 적용 범위
middleware.js 파일은 프로젝트의 루트에 위치하며, 기본적으로 모든 경로에 대해 실행된다. 특정 경로에만 미들웨어를 적용하려면, middleware.js 내에서 조건문을 사용하여 경로를 필터링하거나, matcher 옵션을 사용하여 특정 경로에만 미들웨어를 적용할 수 있다.

```javascript 
import { NextResponse } from 'next/server';

export function middleware(req) {
  const { pathname } = req.nextUrl;

  // 특정 경로에만 미들웨어 로직 적용
  if (pathname.startsWith('/protected')) {
    const token = req.cookies.get('authToken');

    if (!token) {
      const url = req.nextUrl.clone();
      url.pathname = '/login';
      return NextResponse.redirect(url);
    }
  }

  // 다른 경로는 그대로 진행
  return NextResponse.next();
}
```

```javascript
   // next.config.js
   module.exports = {
     middleware: {
       matcher: ['/api/:path*', '/dashboard/:path*'], // 특정 경로에만 미들웨어 적용
     },
   };
```

# 12.i18n
Next.js의 국제화 기능은 다국어 웹사이트를 쉽게 구축할 수 있도록 도와줍니다. 기본적으로 next.config.js 파일에서 i18n 설정을 추가하면, Next.js는 자동으로 각 언어에 대한 경로를 생성한다.다국어 콘텐츠를 제공하기 위해서는 몇 가지 추가적인 설정과 코드가 필요하다.

### (1).기본설정

```javascript 
// next.config.js
module.exports = {
  i18n: {
    locales: ['en', 'fr', 'de'], // 지원할 언어 목록
    defaultLocale: 'en', // 기본 언어
  },
};
```

### (2).언어별 콘텐츠 준비
각 언어에 대한 번역 파일을 준비해야 한다. 일반적으로 public/locales 디렉토리에 언어별 JSON 파일을 생성한다.

```javascript
   public/
   └── locales/
       ├── en/
       │   └── common.json
       ├── fr/
       │   └── common.json
       └── de/
           └── common.json
```
```javascript
   {
     "welcome": "Welcome to our website!",
     "description": "This is a sample description."
   }
```

### (3).next-i18next 패키지 설치
Next.js의 i18n 기능을 더 쉽게 사용하기 위해 next-i18next 패키지를 사용할 수 있다. 이 패키지는 번역 파일을 로드하고, 번역을 쉽게 사용할 수 있는 훅을 제공한다.

```javascript 
  npm install next-i18next react-i18next
```

### (4).next-i18next 설정
프로젝트 루트에 next-i18next.config.js 파일을 생성하고, 설정을 추가한다.

```javascript 
  // next-i18next.config.js
   module.exports = {
     i18n: {
       locales: ['en', 'fr', 'de'],
       defaultLocale: 'en',
     },
   };
```

### (5).18n 지원을 위한 HOC 사용
_app.js 파일에서 appWithTranslation HOC를 사용하여 i18n 기능을 활성화한다.

```javascript 
  npm install next-i18next react-i18next
```

### (6).번역 사용
페이지 컴포넌트에서 useTranslation 훅을 사용하여 번역을 적용한다.

```javascript 
   import { useTranslation } from 'next-i18next';

   export default function Home() {
     const { t } = useTranslation('common');

     return (
       <div>
         <h1>{t('welcome')}</h1>
         <p>{t('description')}</p>
       </div>
     );
   }
```

### (7).서버 사이드 번역 지원
각 페이지에서 getStaticProps 또는 getServerSideProps를 사용하여 번역을 미리 로드할 수 있다.

```javascript 
   import { serverSideTranslations } from 'next-i18next/serverSideTranslations';

   export async function getStaticProps({ locale }) {
     return {
       props: {
         ...(await serverSideTranslations(locale, ['common'])),
       },
     };
   }
```
