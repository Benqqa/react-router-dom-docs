# Await

/ Await используется для ожидания завершения асинхронных операций в компонентах. Это позволяет управлять состоянием загрузки и отображать соответствующий интерфейс. */ 
Используется для отображения отложенных значений с автоматической обработкой ошибок. Обязательно ознакомьтесь с руководством по отложенным данным, так как с этим компонентом работают несколько API.

```jsx

import { Await, useLoaderData } from "react-router-dom";

function Book() {
  const { book, reviews } = useLoaderData();
  return (
    <div>
      <h1>{book.title}</h1>
      <p>{book.description}</p>
      <React.Suspense fallback={<ReviewsSkeleton />}>
        <Await
          resolve={reviews}
          errorElement={
            <div>Could not load reviews 😬</div>
          }
          children={(resolvedReviews) => (
            <Reviews items={resolvedReviews} />
          )}
        />
      </React.Suspense>
    </div>
  );
}
```

Примечание: <Await> ожидается, что он будет отображаться внутри <React.Suspense> или <React.SuspenseList> родительского элемента для включения альтернативного пользовательского интерфейса.

Объявление типа

```jsx
declare function Await(
  props: AwaitProps
): React.ReactElement;

interface AwaitProps {
  children: React.ReactNode | AwaitResolveRenderFunction;
  errorElement?: React.ReactNode;
  resolve: TrackedPromise | any;
}

interface AwaitResolveRenderFunction {
  (data: Awaited<any>): React.ReactElement;
}
```

children
Может быть либо элементами React, либо функцией.

При использовании функции значение указывается в качестве единственного параметра.

```jsx
<Await resolve={reviewsPromise}>
  {(resolvedReviews) => <Reviews items={resolvedReviews} />}
</Await>
При использовании React elements, useAsyncValue предоставит данные:

<Await resolve={reviewsPromise}>
  <Reviews />
</Await>;

function Reviews() {
  const resolvedReviews = useAsyncValue();
  return <div>{/* ... */}</div>;
}
```

errorElement
Элемент ошибки отображается вместо дочерних элементов, когда обещание отклоняется. Вы можете получить доступ к ошибке с помощью useAsyncError.

Если обещание отклоняется, вы можете предоставить необязательный errorElement для обработки этой ошибки в контекстном пользовательском интерфейсе с помощью хука useAsyncError.

```jsx
<Await
  resolve={reviewsPromise}
  errorElement={<ReviewsError />}
>
  <Reviews />
</Await>;

function ReviewsError() {
  const error = useAsyncError();
  return <div>{error.message}</div>;
}
```

Если вы не укажете errorElement, отклоненное значение будет передаваться на ближайший уровень маршрута errorElement и будет доступно через хук useRouteError

resolve
Принимает обещание, возвращаемое значением отложенного загрузчика, для выполнения и отображения.

```jsx
import {
  defer,
  Route,
  useLoaderData,
  Await,
} from "react-router-dom";

// given this route
<Route
  loader={async () => {
    let book = await getBook();
    let reviews = getReviews(); // not awaited
    return defer({
      book,
      reviews, // this is a promise
    });
  }}
  element={<Book />}
/>;

function Book() {
  const {
    book,
    reviews, // this is the same promise
  } = useLoaderData();
  return (
    <div>
      <h1>{book.title}</h1>
      <p>{book.description}</p>
      <React.Suspense fallback={<ReviewsSkeleton />}>
        <Await
          // and is the promise we pass to Await
          resolve={reviews}
        >
          <Reviews />
        </Await>
      </React.Suspense>
    </div>
  );
}
```

[Вернуться к содержанию](./index.md)