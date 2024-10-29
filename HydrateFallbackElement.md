# Hydrate Fallback Element

/ Hydrate Fallback Element используется для отображения резервного пользовательского интерфейса во время гидратации приложения, когда основной контент еще не загружен. */ 

Если вы используете рендеринг на стороне сервера и частичную гидратацию, то вы можете указать элемент/компонент для рендеринга на маршрутах без гидратации во время первоначальной гидратации приложения.

Если вы не хотите указывать элемент React (например, hydrateFallbackElement={<MyFallback />}), вместо него можно указать компонент HydrateFallback (например, HydrateFallback={MyFallback}), и React Router будет вызывать createElement внутри себя.

Эта функция работает только при использовании маршрутизатора, см. «Выбор маршрутизатора»

```jsx
let router = createBrowserRouter(
  [
    {
      id: "root",
      path: "/",
      loader: rootLoader,
      Component: Root,
      children: [
        {
          id: "invoice",
          path: "invoices/:id",
          loader: loadInvoice,
          Component: Invoice,
          HydrateFallback: InvoiceSkeleton,
        },
      ],
    },
  ],
  {
    future: {
      v7_partialHydration: true,
    },
    hydrationData: {
      root: {
        /*...*/
      },
      // No hydration data provided for the `invoice` route
    },
  }
);
```

По умолчанию нет запасного варианта, и он просто отобразит null на этом уровне маршрута, поэтому рекомендуется всегда предоставлять собственный запасной элемент.


[Вернуться к содержанию](./index.md)