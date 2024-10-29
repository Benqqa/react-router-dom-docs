
Маршруты, пожалуй, являются самой важной частью приложения React Router. Они связывают сегменты URL с компонентами, загрузкой данных и их изменением. Благодаря вложенным маршрутам сложные макеты приложений и зависимости данных становятся простыми и декларативными.

Маршруты - это объекты, передаваемые функциям создания маршрутизатора

```jsx
const router = createBrowserRouter([
  {
    // it renders this element
    element: <Team />,

    // when the URL matches this segment
    path: "teams/:teamId",

    // with this data loaded before rendering
    loader: async ({ request, params }) => {
      return fetch(
        `/fake/api/teams/${params.teamId}.json`,
        { signal: request.signal }
      );
    },

    // performing this mutation when data is submitted to it
    action: async ({ request }) => {
      return updateFakeTeam(await request.formData());
    },

    // and renders this element in case something went wrong
    errorElement: <ErrorBoundary />,
  },
]);
```

Вы также можете объявлять свои маршруты с помощью JSX и createRoutesFromElements, свойства элемента будут идентичны свойствам объектов маршрута:

```jsx
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      element={<Team />}
      path="teams/:teamId"
      loader={async ({ params }) => {
        return fetch(
          `/fake/api/teams/${params.teamId}.json`
        );
      }}
      action={async ({ request }) => {
        return updateFakeTeam(await request.formData());
      }}
      errorElement={<ErrorBoundary />}
    />
  )
);
```

Ни один из стилей не является предпочтительным, и их поведение одинаково. В большей части этой документации мы будем использовать стиль JSX, потому что большинство людей привыкли к нему в контексте React Router.

При использовании RouterProvider если вы не хотите указывать элемент React (т. е. element={<MyComponent />}), вы можете указать Component вместо него (т. е. Component={MyComponent}), и React Router будет вызывать createElement для вас внутри. Однако это следует делать только для приложений RouterProvider, поскольку использование Component внутри <Routes> снизит эффективность повторного использования созданного элемента в разных рендерах.

Объявление типа

```ts
interface RouteObject {
  path?: string;
  index?: boolean;
  children?: RouteObject[];
  caseSensitive?: boolean;
  id?: string;
  loader?: LoaderFunction;
  action?: ActionFunction;
  element?: React.ReactNode | null;
  hydrateFallbackElement?: React.ReactNode | null;
  errorElement?: React.ReactNode | null;
  Component?: React.ComponentType | null;
  HydrateFallback?: React.ComponentType | null;
  ErrorBoundary?: React.ComponentType | null;
  handle?: RouteObject["handle"];
  shouldRevalidate?: ShouldRevalidateFunction;
  lazy?: LazyRouteFunction<RouteObject>;
}
```

path
Шаблон пути для сопоставления с URL-адресом, чтобы определить, соответствует ли этот маршрут URL-адресу, ссылке или действию формы.

Динамические сегменты
Если сегмент пути начинается с :, то он становится «динамическим сегментом». Когда маршрут соответствует URL-адресу, динамический сегмент извлекается из URL-адреса и предоставляется в виде params другим API маршрутизатора.

```jsx
<Route
  // this path will match URLs like
  // - /teams/hotspur
  // - /teams/real
  path="/teams/:teamId"
  // the matching param will be available to the loader
  loader={({ params }) => {
    console.log(params.teamId); // "hotspur"
  }}
  // and the action
  action={({ params }) => {}}
  element={<Team />}
/>;

// and the element through `useParams`
function Team() {
  let params = useParams();
  console.log(params.teamId); // "hotspur"
}
```

В одном маршруте может быть несколько динамических сегментов:

```jsx
<Route path="/c/:categoryId/p/:productId" />;
// both will be available
params.categoryId;
params.productId;
```

Динамические сегменты не могут быть "частичными":

```jsx
🚫 "/teams-:teamId"
✅ "/teams/:teamId"
🚫 "/:category--:productId"
✅ "/:productSlug"
```

Вы по-прежнему можете использовать такие шаблоны URL, просто вам придётся немного поработать над их анализом:

```jsx
function Product() {
  const { productSlug } = useParams();
  const [category, product] = productSlug.split("--");
  // ...
}
```

Дополнительные сегменты
Вы можете сделать сегмент маршрута необязательным, добавив ? в конец сегмента.

```jsx
<Route
  // this path will match URLs like
  // - /categories
  // - /en/categories
  // - /fr/categories
  path="/:lang?/categories"
  // the matching param might be available to the loader
  loader={({ params }) => {
    console.log(params["lang"]); // "en"
  }}
  // and the action
  action={({ params }) => {}}
  element={<Categories />}
/>;

// and the element through `useParams`
function Categories() {
  let params = useParams();
  console.log(params.lang);
}
```

У вас также могут быть дополнительные статические сегменты:

```jsx
<Route path="/project/task?/:taskId" />
```

Шлепки
Также известны как «универсальные» и «звёздные» сегменты. Если шаблон пути маршрута заканчивается на /*, то он будет соответствовать любым символам после /, включая другие символы /.

```jsx
<Route
  // this path will match URLs like
  // - /files
  // - /files/one
  // - /files/one/two
  // - /files/one/two/three
  path="/files/*"
  // the matching param will be available to the loader
  loader={({ params }) => {
    console.log(params["*"]); // "one/two"
  }}
  // and the action
  action={({ params }) => {}}
  element={<Team />}
/>;

// and the element through `useParams`
function Team() {
  let params = useParams();
  console.log(params["*"]); // "one/two"
}
```

Вы можете деструктурировать *, вам просто нужно присвоить ему новое имя. Обычное имя — splat:

```jsx
let { org, "*": splat } = params;
```

Layout Routes
Omitting the path makes this route a "layout route". It participates in UI nesting, but it does not add any segments to the URL.

```jsx
<Route
  element={
    <div>
      <h1>Layout</h1>
      <Outlet />
    </div>
  }
>
  <Route path="/" element={<h2>Home</h2>} />
  <Route path="/about" element={<h2>About</h2>} />
</Route>
```

В этом примере <h1>Layout</h1> будет отображаться вместе с атрибутом element каждого дочернего маршрута через Outlet маршрута-макета.

index
Определяет, является ли маршрут индексным. Индексные маршруты отображаются в Outlet родительского маршрута по URL родительского маршрута (например, дочерний маршрут по умолчанию).

```jsx
<Route path="/teams" element={<Teams />}>
  <Route index element={<TeamsIndex />} />
  <Route path=":teamId" element={<Team />} />
</Route>
```

Поначалу эти специальные маршруты могут показаться запутанными, поэтому здесь у нас есть руководство по ним: Индексный маршрут.

children
(TODO: нужно поговорить о вложенности, возможно, даже в отдельном документе)

caseSensitive
Указывает, должен ли маршрут соответствовать регистру или нет:

```jsx
<Route caseSensitive path="/wEll-aCtuA11y" />
```

Будет соответствовать "wEll-aCtuA11y"
Не будет соответствовать "well-actua11y"
loader
Загрузчик маршрута вызывается перед отображением маршрута и предоставляет данные для элемента через useLoaderData.

```jsx
<Route
  path="/teams/:teamId"
  loader={({ params }) => {
    return fetchTeam(params.teamId);
  }}
/>;

function Team() {
  let team = useLoaderData();
  // ...
}
```

Если вы не используете маршрутизатор данных, подобный createBrowserRouter, это ничего не даст

Пожалуйста, ознакомьтесь с документацией по загрузчику для получения более подробной информации.

action
Действие маршрута вызывается, когда на маршрут отправляется запрос из формы, запросчика или запроса.

```jsx
<Route
  path="/teams/:teamId"
  action={({ request }) => {
    const formData = await request.formData();
    return updateTeam(formData);
  }}
/>
```

Если вы не используете маршрутизатор данных, подобный createBrowserRouter, это ничего не даст

Пожалуйста, ознакомьтесь с документацией по действию для получения более подробной информации.

element/Component
Элемент / компонент React для рендеринга, когда маршрут совпадает с URL.

Если вы хотите создать элемент React, используйте element:

```jsx
<Route path="/for-sale" element={<Properties />} />
```

В противном случае используйте Component и React Router создаст элемент React для вас:

```jsx
<Route path="/for-sale" Component={Properties} />
```

Вам следует использовать API Component только для маршрутов передачи данных через RouterProvider. Использование этого API в <Route> внутри <Routes> снизит эффективность React при повторном использовании созданного элемента при рендеринге.

errorElement/ErrorBoundary
Если при рендеринге маршрута возникает исключение в loader или в action, этот элемент/компонент React будет отображаться вместо обычного element/Component.

Если вы хотите создать элемент React самостоятельно, используйте errorElement:

```jsx
<Route
  path="/for-sale"
  // if this throws an error while rendering
  element={<Properties />}
  // or this while loading properties
  loader={() => loadProperties()}
  // or this while creating a property
  action={async ({ request }) =>
    createProperty(await request.formData())
  }
  // then this element will render
  errorElement={<ErrorBoundary />}
/>
```

В противном случае используйте ErrorBoundary и React Router создаст элемент React для вас:

```jsx
<Route
  path="/for-sale"
  Component={Properties}
  loader={() => loadProperties()}
  action={async ({ request }) =>
    createProperty(await request.formData())
  }
  ErrorBoundary={ErrorBoundary}
/>
```

Если вы не используете маршрутизатор данных, подобный createBrowserRouter, это ничего не даст

Пожалуйста, ознакомьтесь с документацией по errorElement для получения более подробной информации.

hydrateFallbackElement/HydrateFallback
Если вы используете рендеринг на стороне сервера и частичную гидратацию, то вы можете указать элемент/компонент для рендеринга на маршрутах без гидратации во время первоначальной гидратации приложения.

Если вы не используете маршрутизатор данных, подобный createBrowserRouter, это ничего не даст

Это предназначено только для более сложных случаев использования, таких как функциональность clientLoader Remix. Большинству приложений SSR не потребуется использовать эти свойства маршрута.

Пожалуйста, ознакомьтесь с документацией по hydrateFallbackElement для получения более подробной информации.

handle
Любые данные, относящиеся к конкретному приложению. Пожалуйста, ознакомьтесь с документацией useMatches для получения подробной информации и примеров.

lazy
Чтобы уменьшить размер пакетов вашего приложения и поддерживать разделение кода между маршрутами, каждый маршрут может содержать асинхронную функцию, которая обрабатывает части определения маршрута, не соответствующие маршруту (loader, action, Component/element, ErrorBoundary/errorElement и т. д.).

Каждая lazy функция обычно возвращает результат динамического импорта.

```jsx
let routes = createRoutesFromElements(
  <Route path="/" element={<Layout />}>
    <Route path="a" lazy={() => import("./a")} />
    <Route path="b" lazy={() => import("./b")} />
  </Route>
);
```

Затем в модулях ленивых маршрутов экспортируйте свойства, которые вы хотите определить для маршрута:

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
```

Если вы не используете маршрутизатор данных, подобный createBrowserRouter, это ничего не даст

Пожалуйста, ознакомьтесь с lazy документацией для получения более подробной информации.




[Вернуться к содержанию](./index.md)