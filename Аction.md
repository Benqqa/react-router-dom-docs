
Маршрутизирующие действия — это «запись» в маршрутизирующий загрузчик «чтение». Они позволяют приложениям изменять данные с помощью простой семантики HTML и HTTP, в то время как React Router абстрагирует сложность асинхронного пользовательского интерфейса и повторной валидации. Это даёт вам простую ментальную модель HTML + HTTP (где браузер обрабатывает асинхронность и повторную валидацию) с поведением и возможностями UX современных одностраничных приложений.

Эта функция работает только при использовании маршрутизатора, см. «Выбор маршрутизатора»

```jsx
<Route
  path="/song/:songId/edit"
  element={<EditSong />}
  action={async ({ params, request }) => {
    let formData = await request.formData();
    return fakeUpdateSong(params.songId, formData);
  }}
  loader={({ params }) => {
    return fakeGetSong(params.songId);
  }}
/>
```

Действия вызываются всякий раз, когда приложение отправляет запрос, отличный от GET («post», «put», «patch», «delete») на ваш маршрут. Это может происходить несколькими способами:

// forms

```jsx
<Form method="post" action="/songs" />;
<fetcher.Form method="put" action="/songs/123/edit" />;

// imperative submissions
let submit = useSubmit();
submit(data, {
  method: "delete",
  action: "/songs/123",
});
fetcher.submit(data, {
  method: "patch",
  action: "/songs/123/edit",
});
```

Параметры маршрута анализируются из динамических сегментов и передаются в ваше действие. Это полезно для определения того, какой ресурс нужно изменить:

```jsx
<Route
  path="/projects/:projectId/delete"
  action={({ params }) => {
    return fakeDeleteProject(params.projectId);
  }}
/>
```

request


Это экземпляр запроса на получение, отправляемый на ваш маршрут. Наиболее распространённый вариант использования — анализ FormData из запроса

```jsx
<Route
  action={async ({ request }) => {
    let formData = await request.formData();
    // ...
  }}
/>
```

Запрос?!

Поначалу может показаться странным, что действия получают «запрос». Вы когда-нибудь писали эту строку кода?

```jsx
<form
  onSubmit={(event) => {
    event.preventDefault();
    // ...
  }}
/>
```

Что именно вы предотвращаете?

Без JavaScript, только с простым HTML и веб-сервером HTTP, предотвращённое по умолчанию событие на самом деле очень полезно. Браузеры сериализуют все данные в форме в FormData и отправляют их в теле нового запроса на ваш сервер. Как и в приведённом выше коде, React Router <Form> предотвращает отправку браузером этого запроса и вместо этого отправляет запрос на ваше действие по маршруту! Это позволяет создавать очень динамичные веб-приложения с помощью простой модели HTML и HTTP.

Помните, что значения в formData автоматически преобразуются при отправке формы, поэтому ваши входные данные должны быть заключены в name.

```jsx
<Form method="post">
  <input name="songTitle" />
  <textarea name="lyrics" />
  <button type="submit">Save</button>
</Form>;

// accessed by the same names
formData.get("songTitle");
formData.get("lyrics");
```

Для получения дополнительной информации о formData см. Работа с FormData.

Типы сериализации для выбора
Обратите внимание, что при использовании useSubmit вы также можете передать encType: "application/json" или encType: "text/plain" для сериализации полезной нагрузки в request.json() или request.text().

Возвращающий ответы
Вы можете вернуть из действия всё, что захотите, и получить к этому доступ с помощью useActionData, а также можете вернуть веб-ответ Response.

Для получения дополнительной информации обратитесь к документации по загрузчику.

Выполнение Действий
Вы можете использовать throw в своём действии, чтобы выйти из текущего стека вызовов (прекратить выполнение текущего кода), и React Router начнёт работу с «ошибкой».

```jsx
<Route
  action={async ({ params, request }) => {
    const res = await fetch(
      `/api/properties/${params.id}`,
      {
        method: "put",
        body: await request.formData(),
      }
    );
    if (!res.ok) throw res;
    return { ok: true };
  }}
/>
```

Для получения более подробной информации и расширенных вариантов использования ознакомьтесь с документацией по errorElement.

Обработка нескольких действий для каждого маршрута
Довольно распространённый вопрос, который возникает: «Что, если мне нужно обработать несколько разных действий в моём приложении?» Есть несколько способов сделать это, но обычно проще всего поставить name/value на \<button type="submit"> и использовать его в приложении, чтобы решить, какой код выполнять (да, кнопки отправки могут иметь атрибуты name/value!):

```jsx
async function action({ request }) {
  let formData = await request.formData();
  let intent = formData.get("intent");

  if (intent === "edit") {
    await editSong(formData);
    return { ok: true };
  }

  if (intent === "add") {
    await addSong(formData);
    return { ok: true };
  }

  throw json(
    { message: "Invalid intent" },
    { status: 400 }
  );
}

function Component() {
  let song = useLoaderData();

  // When the song exists, show an edit form
  if (song) {
    return (
      <Form method="post">
        <p>Edit song lyrics:</p>
        {/* Edit song inputs */}
        <button type="submit" name="intent" value="edit">
          Edit
        </button>
      </Form>
    );
  }

  // Otherwise show a form to add a new song
  return (
    <Form method="post">
      <p>Add new lyrics:</p>
      {/* Add song inputs */}
      <button type="submit" name="intent" value="add">
        Add
      </button>
    </Form>
  );
}
```

Если имя/значение кнопки не подходит для вашего случая, вы также можете использовать скрытый ввод для отправки и intent или отправлять данные с помощью различных методов HTTP через свойство <Form method> (POST для добавления, PUT/PATCH для редактирования, DELETE для удаления).

[Вернуться к содержанию](./index.md)
