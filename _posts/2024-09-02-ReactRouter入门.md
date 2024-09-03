---
layout: post   	
catalog: true 	
tags:
    - React
---
[官方文档](https://reactrouter.com/en/main/start/tutorial)
# 1. 创建路由

创建一个路由，将路径和组件做匹配

```javascript
// src/Router.js
import { createBrowserRouter } from "react-router-dom";
const router = createBrowserRouter([
    { path: "/", element: <div>Hello world!</div> },
]);

export default router;
```
# 2. 路由挂载到根DOM

不使用ReactRouter时，是这样挂载的

```javascript
// src/index.js
ReactDOM.render(<App />, document.getElementById("root"));
```

使用ReactRouter时，是这样挂载的
```javascript
// src/index.js
import ReactDOM from "react-dom";
import router from "./Router";
import { RouterProvider } from "react-router-dom";

ReactDOM.render(
    <RouterProvider router={router} />,
    document.getElementById("root")
);
```

# 3. 创建全局布局

```javascript
// src/layouts/MainLayout.js
const MainLayout = ()=> {
    return (
      <>
        <div id="sidebar">
          <h1>React Router</h1>
          <div>
            <form id="search-form" role="search">
              <input
                id="q"
                aria-label="Search contacts"
                placeholder="Search"
                type="search"
                name="q"
              />
              <div
                id="search-spinner"
                aria-hidden
                hidden={true}
              />
              <div
                className="sr-only"
                aria-live="polite"
              ></div>
            </form>
            <form method="post">
              <button type="submit">New</button>
            </form>
          </div>
          <nav>
            <ul>
              <li>
                <a href={`/contacts/1`}>Your Name</a>
              </li>
              <li>
                <a href={`/contacts/2`}>Your Friend</a>
              </li>
            </ul>
          </nav>
        </div>
        <div id="detail">具体页面组件</div>
      </>
    );
  }
  export default MainLayout;
```
把全局布局与根路径匹配

```javascript
// src/Router.js
import { createBrowserRouter } from "react-router-dom";
import MainLayout from './layouts/MainLayout';

const router = createBrowserRouter([
    { path: "/", element: <MainLayout /> },
]);

export default router;
```
# 4. 异常页

定义异常页
```javascript
// src/pages/ErrorPage.js
import { useRouteError } from "react-router-dom";

const ErrorPage = () => {
    const error = useRouteError();
    console.error(error);

    return (
        <div id="error-page">
            <h1>Oops!</h1>
            <p>Sorry, an unexpected error has occurred.</p>
            <p>
                <i>{error.statusText || error.message}</i>
            </p>
        </div>
    );
};
export default ErrorPage;
```

放置异常页，当访问的路径下没有匹配的组件就返回异常页

```javascript
// src/Router.js
import { createBrowserRouter } from "react-router-dom";
import ErrorPage from "./pages/ErrorPage";
import MainLayout from "./layouts/MainLayout";

const router = createBrowserRouter([
    {
        path: "/",
        element: <MainLayout />,
        errorElement: <ErrorPage />
    },
]);
```

# 5. 嵌套路由

在全局布局中，加载其他组件

先把这些组件放在children数组中，和对应的路径匹配好

```javascript
// src/Router.js
import { createBrowserRouter } from "react-router-dom";
import ErrorPage from "./pages/ErrorPage";
import MainLayout from "./layouts/MainLayout";
import Query from "./pages/Query";
import Hello from "./pages/Hello";

const router = createBrowserRouter([
    {
        path: "/",
        element: <MainLayout />,
        errorElement: <ErrorPage />,
        children: [
            {
                path: "query",
                element: <Query />,
            },
            {
                path: "hello",
                element: <Hello />,
            },
        ],
    },
]);
```
然后在全局布局的代码中，引入Outlet，把`<Outlet />`放在全局布局代码中相应的位置
```javascript
// src/layouts/MainLayout.js
import { Outlet } from "react-router-dom";
{/* other code */}
  return (
	  {/* other code */}
      <div id="detail">
        <Outlet />
      </div>
      {/* other code */}
    </>
  );
{/* other code */}
```
# 6. 路由切换

使用`<Link to>`实现路由的跳转

```javascript
// src/layouts/MainLayout.js
{/* other code */}
<nav>
	<ul>
		<li>
			<Link to={`query`}>Your query</Link>
		</li>
		<li>
			<Link to={`hello`}>Your Hello</Link>
		</li>
	</ul>
</nav>
{/* other code */}
```
# 7. 路由参数

# 8. 导航状态

使用`useNavigation` hook，获取导航的状态，包括 `"idle" | "submitting" | "loading"`

可以根据该状态，加载进度条等内容，可以配合React Content Loader 骨架屏使用