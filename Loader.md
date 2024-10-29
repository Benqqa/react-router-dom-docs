# Loader

/ Loader используется для предварительной загрузки данных, необходимых для отображения компонента или маршрута. Он позволяет загружать данные асинхронно перед рендерингом. */ 

Для каждого маршрута можно определить функцию «загрузчика», которая будет предоставлять данные элементу маршрута перед его отображением.

Эта функция работает только при использовании маршрутизатора, см. «Выбор маршрутизатора»

```jsx
createBrowserRouter([
  {
    element: <Teams />,
    path: "teams",
    loader: async () => {
      return fakeDb.from("teams").select("*");
    },
    children: [
      {
        element: <Team />,
        path: ":teamId",
        loader: async ({ params }) => {
          return fetch(`/api/teams/${params.teamId}.json`);
        },
      },
    ],
  },
]);
```

По мере того как пользователь перемещается по приложению, загрузчики для следующей подходящей ветки маршрутов будут вызываться параллельно, а их данные будут доступны компонентам через useLoaderData.

params
Параметры маршрута анализируются из динамических сегментов и передаются вашему загрузчику. Это полезно для определения того, какой ресурс нужно загрузить:

```jsx
createBrowserRouter([
  {
    path: "/teams/:teamId",
    loader: ({ params }) => {
      return fakeGetTeam(params.teamId);
    },
  },
]);
```

Обратите внимание, что :teamId в пути анализируется и предоставляется как params.teamId с тем же именем.

request
Это экземпляр запроса на выборку, отправляемый в ваше приложение.

function loader({ request }) {}
Запрос?!

Поначалу может показаться странным, что загрузчики получают «запрос». Представьте, что \<Link> выполняет что-то вроде следующего кода, и спросите себя: «Какое поведение по умолчанию здесь предотвращается?».

```jsx
<a
  href={props.to}
  onClick={(event) => {
    event.preventDefault();
    navigate(props.to);
  }}
/>
```

Без React Router браузер отправил бы запрос на ваш сервер, но React Router предотвратил это! Вместо того чтобы отправлять запрос на ваш сервер, React Router отправляет запрос вашим загрузчикам.

Наиболее распространённый вариант использования — создание URL-адреса и чтение URLSearchParams из него:

```jsx
function loader({ request }) {
  const url = new URL(request.url);
  const searchTerm = url.searchParams.get("q");
  return searchProducts(searchTerm);
}
```

Обратите внимание, что здесь используются не API-интерфейсы React Router, а стандартные веб-объекты: Request, URL, URLSearchParams.

loader.hydrate
Если вы используете рендеринг на стороне сервера и флаг future.v7_partialHydration для частичной гидратации, то вы можете захотеть включить запуск маршрута loader при начальной гидратации, даже если в нём есть данные гидратации (например, чтобы пользователь мог заполнить кэш данными гидратации). Чтобы принудительно запустить loader при гидратации в сценарии частичной гидратации, вы можете установить свойство hydrate для функции loader:

Возвращающий ответы
Хотя вы можете вернуть из загрузчика всё, что захотите, и получить к этому доступ из useLoaderData, вы также можете вернуть веб-ответ Response.

Это может показаться не сразу полезным, но подумайте о fetch. Поскольку возвращаемое значение fetch является ответом, а загрузчики понимают ответы, многие загрузчики могут возвращать простой ответ!

```jsx
// an HTTP/REST API
function loader({ request }) {
  return fetch("/api/teams.json", {
    signal: request.signal,
  });
}

// or even a graphql endpoint
function loader({ request, params }) {
  return fetch("/_gql", {
    signal: request.signal,
    method: "post",
    body: JSON.stringify({
      query: gql`...`,
      params: params,
    }),
  });
}
```

Вы также можете создать ответ самостоятельно:
```jsx

function loader({ request, params }) {
  const data = { some: "thing" };
  return new Response(JSON.stringify(data), {
    status: 200,
    headers: {
      "Content-Type": "application/json; utf-8",
    },
  });
}
```
React Router автоматически вызовет response.json() так, что вашим компонентам не нужно будет анализировать его при рендеринге:

```jsx
function SomeRoute() {
  const data = useLoaderData();
  // { some: "thing" }
}
```
Использование утилиты json упрощает эту задачу, так что вам не придётся создавать их самостоятельно. Следующий пример практически не отличается от предыдущего:

```jsx
import { json } from "react-router-dom";

function loader({ request, params }) {
  const data = { some: "thing" };
  return json(data, { status: 200 });
}
```
Если вы планируете перейти на Remix, возврат ответов от каждого загрузчика упростит миграцию.

Запуск загрузчиков
Вы можете использовать throw в своём загрузчике, чтобы выйти из текущего стека вызовов (прекратить выполнение текущего кода), и React Router начнёт работу с «ошибкой».

```jsx
function loader({ request, params }) {
  const res = await fetch(`/api/properties/${params.id}`);
  if (res.status === 404) {
    throw new Response("Not Found", { status: 404 });
  }
  return res.json();
}
```
Для получения более подробной информации прочтите errorElement документацию.



[Вернуться к содержанию](./index.md)