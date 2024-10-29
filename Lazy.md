# Lazy

/ Lazy используется для динамической загрузки компонентов или маршрутов, что позволяет уменьшить размер начальной загрузки приложения и улучшить производительность. */ 

Чтобы уменьшить размер пакетов вашего приложения и поддерживать разделение кода между маршрутами, каждый маршрут может содержать асинхронную функцию, которая обрабатывает части определения маршрута, не соответствующие маршруту (loader, action, Component/element, ErrorBoundary/errorElement и т. д.).

Отложенные маршруты разрешаются при начальной загрузке и на этапе loading или submitting при навигации или вызове загрузчика. Вы не можете отложенно определять свойства сопоставления маршрутов (path, index, children, caseSensitive), поскольку мы выполняем функции отложенного маршрута только после сопоставления известных маршрутов.

Эта функция работает только при использовании маршрутизатора, см. «Выбор маршрутизатора»

Каждая lazy функция обычно возвращает результат динамического импорта.

```jsx
let routes = createRoutesFromElements(
  <Route path="/" element={<Layout />}>
    <Route path="a" lazy={() => import("./a")} />
    <Route path="b" lazy={() => import("./b")} />
  </Route>
);
```

Затем в модулях ленивых маршрутов экспортируйте свойства, которые вы хотите определить для маршрута (loader, Component, ErrorBoundary):

```jsx
export async function loader({ request }) {
  let data = await fetchData(request);
  return json(data);
}

export function Component() {
  let data = useLoaderData();

  return (
    <>
      <h1>You made it!</h1>
      <p>{data}</p>
    </>
  );
}

// If you want to customize the component display name in React dev tools:
Component.displayName = "SampleLazyRoute";

export function ErrorBoundary() {
  let error = useRouteError();
  return isRouteErrorResponse(error) ? (
    <h1>
      {error.status} {error.statusText}
    </h1>
  ) : (
    <h1>{error.message || error}</h1>
  );
}

// If you want to customize the component display name in React dev tools:
ErrorBoundary.displayName = "SampleErrorBoundary";
```

Обратите внимание, что в этом файле с отложенной загрузкой нет экспорта по умолчанию. Это связано с тем, что `default` не является допустимым ключом для объекта маршрута. В таких файлах обычно следует экспортировать только те ключи, которые вы определяете для объекта маршрута, например `loader`, `action`, `Component`, `ErrorBoundary` и т. д. Все экспортируемые значения будут распространяться непосредственно на объект маршрута, если вы вручную не вернёте объект из `lazy`.

Статически Определенные свойства
Любые свойства, заданные статически в маршруте, не могут быть перезаписаны функцией lazy, и при попытке их перезаписать вы получите предупреждение в консоли.

Кроме того, в качестве оптимизации, если вы статически определяете loader/action, то он будет вызываться параллельно с функцией lazy. Это полезно, если у вас есть небольшие загрузчики, которые вы не хотите включать в основной пакет, и вы хотите, чтобы они загружали данные параллельно с загрузкой компонента. Это похоже на то, как Remix обрабатывает загрузку, потому что каждый маршрут — это отдельный маршрут API.

```jsx
let route = {
  path: "projects",
  loader: ({ request }) => fetchDataForUrl(request.url),
  lazy: () => import("./projects"),
};
```

Это также позволяет более детально разделять код. Например, вы можете разделить loader и Component на разные файлы для параллельной загрузки:

```jsx
let route = {
  path: "projects",
  async loader({ request, params }) {
    let { loader } = await import("./projects-loader");
    return loader({ request, params });
  },
  lazy: () => import("./projects-component"),
};
```

Несколько маршрутов в одном файле
Хотя lazy обычно используется 1:1 с асинхронным import() для каждого маршрута, вы можете реализовать более продвинутую lazy функцию и просто возвращать свойства, которые вы хотите добавить к этому маршруту. Это открывает некоторые интересные возможности.

Например, если вы хотите избежать загрузки нескольких фрагментов для вложенных маршрутов, вы можете сохранить их все в одном файле и возвращать их для отдельных маршрутов. Современные сборщики будут использовать одно и то же обещание для разных import() вызовов.

```jsx
// Assume pages/Dashboard.jsx has all of our loaders/components for multiple
// dashboard routes
let dashboardRoute = {
  path: "dashboard",
  async lazy() {
    let { Layout } = await import("./pages/Dashboard");
    return { Component: Layout };
  },
  children: [
    {
      index: true,
      async lazy() {
        let { Index } = await import("./pages/Dashboard");
        return { Component: Index };
      },
    },
    {
      path: "messages",
      async lazy() {
        let { messagesLoader, Messages } = await import(
          "./pages/Dashboard"
        );
        return {
          loader: messagesLoader,
          Component: Messages,
        };
      },
    },
  ],
};
```



[Вернуться к содержанию](./index.md)
