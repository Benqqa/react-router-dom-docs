# Form

/ Form используется для создания и управления формами в приложении. Он позволяет обрабатывать ввод данных пользователем и отправлять их на сервер. */ 


Объявление типа

```ts
declare function Form(props: FormProps): React.ReactElement;

interface FormProps
  extends React.FormHTMLAttributes<HTMLFormElement> {
  method?: "get" | "post" | "put" | "patch" | "delete";
  encType?:
    | "application/x-www-form-urlencoded"
    | "multipart/form-data"
    | "text/plain";
  action?: string;
  onSubmit?: React.FormEventHandler<HTMLFormElement>;
  fetcherKey?: string;
  navigate?: boolean;
  preventScrollReset?: boolean;
  relative?: "route" | "path";
  reloadDocument?: boolean;
  replace?: boolean;
  state?: any;
  viewTransition?: boolean;
}
```

Компонент «Форма» — это оболочка для обычной HTML-формы, которая имитирует браузер для маршрутизации на стороне клиента и изменения данных. Это не библиотека для проверки форм и управления состоянием, к которой вы могли бы привыкнуть в экосистеме React (для этого мы рекомендуем встроенную в браузер проверку HTML-форм и проверку данных на вашем серверном приложении).

Эта функция работает только при использовании маршрутизатора, см. «Выбор маршрутизатора»

```jsx
import { Form } from "react-router-dom";

function NewEvent() {
  return (
    <Form method="post" action="/events">
      <input type="text" name="title" />
      <input type="text" name="description" />
      <button type="submit">Create</button>
    </Form>
  );
}
```

Убедитесь, что у ваших входных данных есть имена, иначе FormData не будет включать значение этого поля.

Всё это приведёт к обновлению состояния любых отображаемых хуков useNavigation, чтобы вы могли создавать индикаторы ожидания и оптимистичный пользовательский интерфейс во время выполнения асинхронных операций.

Если форма не выглядит как навигация, вам, вероятно, нужно useFetcher.

action
URL-адрес, на который будет отправлена форма, как и в HTML-форме. Единственное отличие — действие по умолчанию. В HTML-формах по умолчанию используется полный URL-адрес. В <Form> по умолчанию используется относительный URL-адрес ближайшего маршрута в контексте.

Рассмотрим следующие маршруты и компоненты:

```jsx
function ProjectsLayout() {
  return (
    <>
      <Form method="post" />
      <Outlet />
    </>
  );
}

function ProjectsPage() {
  return <Form method="post" />;
}

<DataBrowserRouter>
  <Route
    path="/projects"
    element={<ProjectsLayout />}
    action={ProjectsLayout.action}
  >
    <Route
      path=":projectId"
      element={<ProjectsPage />}
      action={ProjectsPage.action}
    />
  </Route>
</DataBrowserRouter>;
```

Если текущий URL-адрес — "/projects/123", то форма внутри дочернего маршрута ProjectsPage будет иметь действие по умолчанию, как и следовало ожидать: "/projects/123". В этом случае, когда маршрут является самым глубоким совпадающим маршрутом, и \<Form> и обычные HTML-формы дают одинаковый результат.

Но форма внутри ProjectsLayout будет указывать на "/projects", а не на полный URL. Другими словами, она указывает на соответствующий сегмент URL для маршрута, на котором отображается форма.

Это помогает обеспечить переносимость, а также совместное размещение форм и обработчиков их действий, если вы добавляете какие-либо соглашения для своих модулей маршрутов.

Если вам нужно отправить сообщение по другому маршруту, добавьте параметр действия:

\<Form action="/projects/new" method="post" />
Смотрите также:

Параметр поиска по индексу (значение индекса в сравнении с родительским маршрутом)
Пожалуйста, ознакомьтесь с разделом Splat Paths в документации useResolvedPath для получения информации о поведении future.v7_relativeSplatPath будущего флага для относительного useNavigate() поведения в маршрутах Splat

method
Это определяет используемый HTTP-метод. То же самое, что и обычный HTML метод формы, за исключением того, что он также поддерживает «put», «patch» и «delete» в дополнение к «get» и «post». По умолчанию используется «get».

ПОЛУЧАТЬ заявки
Метод по умолчанию — «получить». При отправке данных действие не будет вызвано. Отправка данных — это то же самое, что обычная навигация (пользователь нажимает на ссылку), за исключением того, что пользователь может указать параметры поиска, которые передаются в URL из формы.

```jsx
<Form method="get" action="/products">
  <input
    aria-label="search products"
    type="text"
    name="q"
  />
  <button type="submit">Search</button>
</Form>
```

Допустим, пользователь вводит «кроссовки» и отправляет форму. React Router имитирует работу браузера и сериализует форму в URLSearchParams, а затем перенаправляет пользователя на "/products?q=running+shoes". Это похоже на то, как если бы вы, как разработчик, отображали <Link to="/products?q=running+shoes"> вместо того, чтобы позволить пользователю динамически задавать строку запроса.

Ваш загрузчик маршрутов может получить доступ к этим значениям наиболее удобным способом, создав новый URL из request.url и затем загрузив данные.

```jsx
<Route
  path="/products"
  loader={async ({ request }) => {
    let url = new URL(request.url);
    let searchTerm = url.searchParams.get("q");
    return fakeSearchProducts(searchTerm);
  }}
/>
```

Материалы о мутациях
Все остальные методы являются «мутационными запросами», то есть вы собираетесь что-то изменить в своих данных с помощью POST, PUT, PATCH или DELETE. Обратите внимание, что обычные HTML-формы поддерживают только «post» и «get», мы также придерживаемся этих двух методов.

Когда пользователь отправляет форму, React Router сопоставляет action с маршрутами приложения и вызывает <Route action> с сериализованными FormData. Когда действие завершится, все данные загрузчика на странице автоматически обновятся, чтобы ваш пользовательский интерфейс синхронизировался с вашими данными.

Метод будет доступен в request.method внутри вызываемого действия маршрута. Вы можете использовать его, чтобы сообщить абстракциям данных о цели отправки.

```jsx
<Route
  path="/projects/:id"
  element={<Project />}
  loader={async ({ params }) => {
    return fakeLoadProject(params.id);
  }}
  action={async ({ request, params }) => {
    switch (request.method) {
      case "PUT": {
        let formData = await request.formData();
        let name = formData.get("projectName");
        return fakeUpdateProject(name);
      }
      case "DELETE": {
        return fakeDeleteProject(params.id);
      }
      default: {
        throw new Response("", { status: 405 });
      }
    }
  }}
/>;
```

```jsx
function Project() {
  let project = useLoaderData();

  return (
    <>
      <Form method="put">
        <input
          type="text"
          name="projectName"
          defaultValue={project.name}
        />
        <button type="submit">Update Project</button>
      </Form>

      <Form method="delete">
        <button type="submit">Delete Project</button>
      </Form>
    </>
  );
}
```

Как видите, обе формы отправляют данные по одному и тому же маршруту, но вы можете использовать request.method для выбора того, что вы хотите сделать. После выполнения действий loader будет повторно проверен, и пользовательский интерфейс автоматически синхронизируется с новыми данными.

navigate
Вы можете указать форме пропустить навигацию и использовать запрос внутри, указав <Form navigate={false}>. По сути, это сокращение для useFetcher() + <fetcher.Form> в случае, если вам не нужны полученные данные, а нужно только отправить форму и получить доступ к ожидающему состоянию с помощью useFetchers().

fetcherKey
При использовании не навигационного Form вы также можете дополнительно указать свой собственный ключ для получения данных с помощью <Form navigate={false} fetcherKey="my-key">.

replace
Указывает форме на необходимость заменить текущую запись в стеке истории вместо добавления новой записи.

<Form replace />
Поведение по умолчанию зависит от поведения формы:

method=get формы по умолчанию равны false
методы отправки зависят от formAction и action поведения:
если ваш action выдает ошибку, то по умолчанию она будет равна false
если ваш action перенаправляется в текущее местоположение, по умолчанию используется значение true
если ваш action перенаправляет в другое место, по умолчанию используется значение false
если ваше formAction текущее местоположение, то по умолчанию оно равно true
в противном случае по умолчанию используется значение false
Мы обнаружили, что с помощью get вы часто хотите, чтобы пользователь мог нажать «Назад», чтобы просмотреть предыдущие результаты поиска/фильтры и т. д. Но при использовании других методов по умолчанию true отображается запрос «Вы уверены, что хотите повторно отправить форму?». Обратите внимание, что даже если replace={false} React Router не будет повторно отправлять форму при нажатии кнопки «Назад» и используется метод post, put, patch или delete.

Другими словами, это действительно полезно только для отправки запросов GET, когда вы хотите, чтобы кнопка «Назад» не отображала предыдущие результаты.

relative
По умолчанию пути относятся к иерархии маршрутов, поэтому .. будет подниматься на один Route уровень выше. Иногда вы можете столкнуться с тем, что у вас есть совпадающие шаблоны URL, которые не имеют смысла для вложенности, и вы предпочитаете использовать относительную маршрутизацию по пути. Вы можете выбрать такое поведение с помощью <Form to="../some/where" relative="path">

reloadDocument
Указывает форме пропустить React Router и отправить форму с помощью встроенного в браузер поведения.

<Form reloadDocument />
Это рекомендуется использовать вместо <form>, чтобы получить преимущества по умолчанию и относительные action, но в остальном это то же самое, что и обычная HTML-форма.

Без такого фреймворка, как Remix, или без собственной серверной обработки сообщений для маршрутов это не очень полезно.

Смотрите также:

useNavigation
useActionData
useSubmit
state
Свойство state можно использовать для установки значения состояния для нового местоположения, которое хранится в состоянии истории. Впоследствии к этому значению можно получить доступ через useLocation().

```jsx
<Form
  method="post"
  action="new-path"
  state={{ some: "value" }}
/>
```

Вы можете получить доступ к этому значению состояния, находясь на маршруте «новый путь»:

let { state } = useLocation();
preventScrollReset
Если вы используете <ScrollRestoration>, это позволит предотвратить сброс позиции прокрутки в верхнюю часть окна при перенаправлении формы на новый адрес.

<Form method="post" preventScrollReset={true} />
Смотрите также: <Link preventScrollReset>

viewTransition
Свойство viewTransition позволяет использовать переход вида для этой навигации, заключая окончательное обновление состояния в document.startViewTransition(). Если вам нужно применить специальные стили для этого перехода вида, вам также потребуется использовать useViewTransitionState().

Примеры
ЗАДАЧА: Больше примеров

Фильтрация большого списка
Обычно запросы GET используются для фильтрации больших списков, например, на сайтах электронной коммерции и бронирования путешествий.

```jsx
function FilterForm() {
  return (
    <Form method="get" action="/slc/hotels">
      <select name="sort">
        <option value="price">Price</option>
        <option value="stars">Stars</option>
        <option value="distance">Distance</option>
      </select>

      <fieldset>
        <legend>Star Rating</legend>
        <label>
          <input type="radio" name="stars" value="5" />{" "}
          ★★★★★
        </label>
        <label>
          <input type="radio" name="stars" value="4" /> ★★★★
        </label>
        <label>
          <input type="radio" name="stars" value="3" /> ★★★
        </label>
        <label>
          <input type="radio" name="stars" value="2" /> ★★
        </label>
        <label>
          <input type="radio" name="stars" value="1" /> ★
        </label>
      </fieldset>

      <fieldset>
        <legend>Amenities</legend>
        <label>
          <input
            type="checkbox"
            name="amenities"
            value="pool"
          />{" "}
          Pool
        </label>
        <label>
          <input
            type="checkbox"
            name="amenities"
            value="exercise"
          />{" "}
          Exercise Room
        </label>
      </fieldset>
      <button type="submit">Search</button>
    </Form>
  );
}
```

Когда пользователь отправит эту форму, она будет сериализована в URL-адрес примерно так, в зависимости от выбранных пользователем параметров:

/slc/hotels?sort=price&stars=4&amenities=pool&amenities=exercise
Вы можете получить доступ к этим значениям из request.url

```jsx
<Route
  path="/:city/hotels"
  loader={async ({ request }) => {
    let url = new URL(request.url);
    let sort = url.searchParams.get("sort");
    let stars = url.searchParams.get("stars");
    let amenities = url.searchParams.getAll("amenities");
    return fakeGetHotels({ sort, stars, amenities });
  }}
/>
```

Смотрите также:

Использует SUBMIT

[Вернуться к содержанию](./index.md)