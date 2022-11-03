# Simple React Router v6.4

Ejemplo sencillo de como usar React Router v6.4

## Instalación

```bash
npm install react-router-dom
```

Versión específica

```bash
npm install react-router-dom@6.4
```

## Agregar enrutador

Lo primero que debemos hacer es crear un enrutador de navegador y configurar nuestra primera ruta. Esto habilitará el enrutamiento del lado del cliente para nuestra aplicación web.

[createBrowserRouter](https://reactrouter.com/en/main/routers/create-browser-router)

Este es el enrutador recomendado para todos los proyectos web de React Router. Utiliza [DOM history API](https://developer.mozilla.org/en-US/docs/Web/API/History) para actualizar la URL y administrar la pila de historial.

También habilita la data API v6.4 como [loaders](https://reactrouter.com/en/main/route/loader), [actions](https://reactrouter.com/en/main/route/action), [fetchers](https://reactrouter.com/en/main/hooks/use-fetcher) y más.

router/index.jsx

```jsx
import { createBrowserRouter } from "react-router-dom";
import Home from "../pages/Home";
import About from "../pages/About";
import Blog from "../pages/Blog";

export const router = createBrowserRouter([
    {
        path: "/",
        element: <Home />,
    },
    {
        path: "/about",
        element: <About />,
    },
    {
        path: "/blog",
        element: <Blog />,
    },
]);
```

RouterProvider

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";

import { router } from "./router";
import { RouterProvider } from "react-router-dom";

ReactDOM.createRoot(document.getElementById("root")).render(
    <React.StrictMode>
        <RouterProvider router={router} />
    </React.StrictMode>
);
```

## useRouterError

```jsx
import { useRouteError } from "react-router-dom";

const NotFound = () => {
    const error = useRouteError();
    console.log(error);

    return (
        <div>
            <h1>404</h1>
            <p>Page not found</p>
            <p>{error.statusText || error.message}</p>
        </div>
    );
};
export default NotFound;
```

router/index.jsx

```jsx
export const router = createBrowserRouter([
    {
        path: "/",
        element: <Home />,
        errorElement: <NotFound />,
    },
    {
        path: "/about",
        element: <About />,
        errorElement: <NotFound />,
    },
    {
        path: "/blog",
        element: <Blog />,
        errorElement: <NotFound />,
    },
]);
```

## Rutas anidadas

layout/LayoutPublic.jsx

```jsx
import { Outlet } from "react-router-dom";

const LayoutPublic = () => {
    return (
        <div>
            <nav>Navbar</nav>
            <Outlet />
            <footer>Footer</footer>
        </div>
    );
};
export default LayoutPublic;
```

router/index.jsx

```jsx
export const router = createBrowserRouter([
    {
        path: "/",
        element: <LayoutPublic />,
        errorElement: <NotFound />,
        children: [
            {
                index: true,
                element: <Home />,
            },
            {
                path: "/about",
                element: <About />,
            },
            {
                path: "/blog",
                element: <Blog />,
            },
        ],
    },
]);
```

## Navbar

components/Navbar.jsx

```jsx
import { Link } from "react-router-dom";
const Navbar = () => {
    return (
        <nav className="navbar navbar-dark bg-dark">
            <div className="container">
                <Link to="/" className="btn btn-outline-primary">
                    Home
                </Link>
                <Link to="/about" className="btn btn-outline-primary">
                    About
                </Link>
                <Link to="/blog" className="btn btn-outline-primary">
                    Blog
                </Link>
            </div>
        </nav>
    );
};

export default Navbar;
```

layout/LayoutPublic.jsx

```jsx
import { Outlet } from "react-router-dom";
import Navbar from "../components/Navbar";

const LayoutPublic = () => {
    return (
        <div>
            <Navbar />
            <main className="container">
                <Outlet />
            </main>
            <footer className="container">Footer</footer>
        </div>
    );
};
export default LayoutPublic;
```

## NavLink

[NavLink](https://reactrouter.com/en/main/components/nav-link) es un componente especializado para crear enlaces de navegación. Este componente se utiliza para crear enlaces de navegación que se activan cuando la ruta coincide con la URL actual.

De forma predeterminada se utiliza la clase CSS `active` para marcar el enlace como activo.

components/Navbar.jsx

```jsx
<NavLink to="/" className="btn btn-outline-primary">
    Home
</NavLink>
<NavLink to="/about" className="btn btn-outline-primary">
    About
</NavLink>
<NavLink to="/blog" className="btn btn-outline-primary">
    Blog
</NavLink>
```

isActive

```jsx
<NavLink
    to="tasks"
    className={({ isActive }) => (isActive ? activeClassName : undefined)}
>
    Tasks
</NavLink>
```

## Loader y useLoaderData

Hay dos API que usaremos para cargar datos [loader](https://reactrouter.com/en/main/route/loader) y [useLoaderData](https://reactrouter.com/en/main/hooks/use-loader-data). Primero crearemos y exportaremos una función de cargador en el módulo raíz, luego la conectaremos a la ruta. Finalmente, accederemos y renderizaremos los datos.

```jsx
const Blog = () => {
    return "Blog";
};
export default Blog;

export const loaderBlogs = async () => {
    const data = await fetch("https://jsonplaceholder.typicode.com/posts");
    const blogs = await data.json();
    return { blogs };
};
```

router/index.jsx

```jsx
import Blog, { loaderBlogs } from "../pages/Blog";

{
    path: "/blog",
    element: <Blog />,
    loader: loaderBlogs
},
```

useLoaderData

```jsx
import { Link, useLoaderData } from "react-router-dom";

const Blog = () => {
    const { blogs } = useLoaderData();
    console.log(blogs);

    return (
        <ul>
            {blogs.length > 0 ? (
                blogs.map((blog) => (
                    <li key={blog.id}>
                        <Link to={`/blog/${blog.id}`}>{blog.title}</Link>
                    </li>
                ))
            ) : (
                <li>No blogs found</li>
            )}
        </ul>
    );
};
export default Blog;

export const loaderBlogs = async () => {
    const data = await fetch("https://jsonplaceholder.typicode.com/posts");
    const blogs = await data.json();
    return { blogs };
};
```

## rutas con parámetros

```jsx
{
    path: "/blog/:id",
    element: <Post />,
},
```

Post.jsx

```jsx
const Post = () => {
    return "Post";
};
export default Post;

export const loaderPost = async ({ params }) => {
    const data = await fetch(
        `https://jsonplaceholder.typicode.com/posts/${params.id}`
    );
    const post = await data.json();
    return { post };
};
```

```jsx{4}
{
    path: "/blog/:id",
    element: <Post />,
    loader: loaderPost,
},
```

## useRouteData

```jsx
import { useLoaderData } from "react-router-dom";

const Post = () => {
    const { post } = useLoaderData();

    return (
        <div>
            <h1>
                {post.id} - {post.title}
            </h1>
            <p>{post.body}</p>
        </div>
    );
};
export default Post;

export const loaderPost = async ({ params }) => {
    const data = await fetch(
        `https://jsonplaceholder.typicode.com/posts/${params.id}`
    );
    const post = await data.json();
    return { post };
};
```

## useNavigation

[useNavigation](https://reactrouter.com/en/main/hooks/use-navigation) Este gancho le dice todo lo que necesita saber sobre la navegación de una página para crear indicadores de navegación pendientes y una interfaz de usuario optimista sobre mutaciones de datos.

navigation: Devuelve el estado de navegación actual: puede ser uno de "idle" | "submitting" | "loading".

```jsx
import { Outlet, useNavigation } from "react-router-dom";
import Navbar from "../components/Navbar";

const LayoutPublic = () => {
    const navigation = useNavigation();

    return (
        <div>
            <Navbar />
            <main className="container">
                {navigation.state === "loading" && (
                    <div className="alert alert-info my-5">Loading...</div>
                )}
                <Outlet />
            </main>
            <footer className="container">Footer</footer>
        </div>
    );
};
export default LayoutPublic;
```

## error data

Si la solicitud falla, podemos activar un error de datos para que el usuario pueda volver a intentarlo.

Post.jsx

```jsx
if (!data.ok)
    throw {
        status: data.status,
        statusText: "Code: " + data.status,
    };
```

[Compartir layout en error](https://reactrouter.com/en/main/start/tutorial#pathless-routes)

```jsx
export const router = createBrowserRouter([
    {
        path: "/",
        element: <LayoutPublic />,
        errorElement: <NotFound />,
        children: [
            {
                errorElement: <NotFound />,
                children: [
                    {
                        index: true,
                        element: <Home />,
                    },
                    {
                        path: "/about",
                        element: <About />,
                    },
                    {
                        path: "/blog",
                        element: <Blog />,
                        loader: loaderBlogs,
                    },
                    {
                        path: "/blog/:id",
                        element: <Post />,
                        loader: loaderPost,
                    },
                ],
            },
        ],
    },
]);
```

## React query vs React Router

Desde el lanzamiento de v6.4, algunas personas se preguntan si React Router está intentando reemplazar bibliotecas como React Query , useSwr , etc.

**La respuesta es no.** React Router no está intentando reemplazar ninguna biblioteca de datos. React Router es una biblioteca de enrutamiento y no tiene nada que ver con la gestión de datos.

React Query es una biblioteca de gestión de datos y no tiene nada que ver con el enrutamiento.

React Router y React Query son bibliotecas diferentes que se pueden usar juntas.

-   [más info web oficial](https://reactrouter.com/en/main/guides/data-libs)
-   [react query meets react router](https://tkdodo.eu/blog/react-query-meets-react-router)
