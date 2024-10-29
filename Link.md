# Link

/ Link используется для создания навигационных ссылок в приложении. Он позволяет пользователям перемещаться между различными маршрутами без перезагрузки страницы. */ 

Объявление типа

```ts
declare function Link(props: LinkProps): React.ReactElement;

interface LinkProps
  extends Omit<
    React.AnchorHTMLAttributes<HTMLAnchorElement>,
    "href"
  > {
  to: To;
  preventScrollReset?: boolean;
  relative?: "route" | "path";
  reloadDocument?: boolean;
  replace?: boolean;
  state?: any;
  viewTransition?: boolean;
}

type To = string | Partial<Path>;

interface Path {
  pathname: string;
  search: string;
  hash: string;
}
```

\<Link> — это элемент, который позволяет пользователю перейти на другую страницу, нажав на него. В react-router-dom элемент \<Link> отображает доступный элемент \<a> с реальным href-ом, который указывает на ресурс, на который он ссылается. Это означает, что такие действия, как щелчок правой кнопкой мыши по \<Link>-у, работают так, как вы ожидаете. Вы можете использовать \<Link reloadDocument> для пропуска маршрутизации на стороне клиента и позволить браузеру обрабатывать переход обычным образом (как если бы это был \<a href>).

```jsx
import * as React from "react";
import { Link } from "react-router-dom";

function UsersIndexPage({ users }) {
  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map((user) => (
          \<Li key={user.id}>
            \<Link to={user.id}>{user.name}</Link>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

Относительное \<Link to> значение (которое не начинается с /) разрешается относительно родительского маршрута, что означает, что оно основывается на пути URL, который был сопоставлен маршруту, который его отобразил \<Link>. Оно может содержать .. ссылки на маршруты, расположенные выше по иерархии. В этих случаях .. работает точно так же, как функция cd командной строки; каждая .. удаляет один сегмент родительского пути.

\<Link to> с .. ведет себя иначе, чем обычный, \<a href> когда текущий URL заканчивается на /. \<Link to> игнорирует косую черту в конце и удаляет по одному сегменту URL для каждого ... Но \<a href> значение обрабатывается .. по-разному, когда текущий URL заканчивается на / vs, когда это не так.

Пожалуйста, ознакомьтесь с разделом Splat Paths в документации useResolvedPath для получения информации о поведении future.v7_relativeSplatPath будущего флага для относительного \<Link to> поведения в маршрутах Splat

relative
По умолчанию ссылки относятся к иерархии маршрутов (relative="route"), поэтому .. будет переходить на один Route уровень выше текущего контекстного маршрута. Иногда вы можете обнаружить, что у вас есть совпадающие шаблоны URL, которые не имеют смысла для вложенности, и вы предпочитаете использовать относительную маршрутизацию по пути от текущего контекстного маршрута. Вы можете выбрать такое поведение с помощью relative="path":

// Contact and EditContact do not share additional UI layout

```jsx
<Route path="/" element={<Layout />}>
  <Route path="contacts/:id" element={<Contact />} />
  <Route
    path="contacts/:id/edit"
    element={<EditContact />}
  />
</Route>;

function EditContact() {
  // Since Contact is not a parent of EditContact we need to go up one level
  // in the current contextual route path, instead of one level in the Route
  // hierarchy
  return (
    \<Link to=".." relative="path">
      Cancel
    </Link>
  );
}
```

Пожалуйста, обратите внимание, что relative: "path" влияет только на разрешение относительного пути. Это не меняет «начальное» местоположение для разрешения относительного пути. Это разрешение всегда относительно текущего местоположения в иерархии маршрутов (т. е. маршрута, в котором Link отображается).

Если вы хотите использовать относительную маршрутизацию по пути к текущему URL-адресу вместо иерархии маршрутов, вы можете сделать это с помощью текущего location и конструктора URL (обратите внимание на поведение завершающей косой черты):

```jsx
// Assume the current URL is https://remix.run/docs/en/main/start/quickstart
let location = useLocation();

// Without trailing slashes
new URL(".", window.origin + location.pathname);
// 'https://remix.run/docs/en/main/start/'
new URL("..", window.origin + location.pathname);
// 'https://remix.run/docs/en/main/'

// With trailing slashes:
new URL(".", window.origin + location.pathname + "/");
// 'https://remix.run/docs/en/main/start/quickstart/'
new URL("..", window.origin + location.pathname + "/");
```

// 'https://remix.run/docs/en/main/start/'
Если вы используете <ScrollRestoration>, это позволит вам предотвратить сброс позиции прокрутки в верхнюю часть окна при нажатии на ссылку.

```jsx
<Link to="?tab=one" preventScrollReset={true} />
```

Это не мешает восстановлению позиции прокрутки, когда пользователь возвращается в нужное место с помощью кнопок «Назад» и «Вперёд», это просто предотвращает сброс настроек, когда пользователь переходит по ссылке.

Пример, когда вам может понадобиться такое поведение, — это список вкладок, которые управляют параметрами поиска в URL-адресе и находятся не в верхней части страницы. Вы же не хотите, чтобы позиция прокрутки перемещалась в верхнюю часть, потому что это может привести к тому, что содержимое, которое вы переключаете, выйдет за пределы области просмотра!

replace
Свойство replace можно использовать, если вы хотите заменить текущую запись в стеке истории с помощью history.replaceState вместо использования history.pushState по умолчанию.

state
Свойство state можно использовать для установки значения состояния для нового местоположения, которое хранится в состоянии истории. Впоследствии к этому значению можно получить доступ через useLocation().

```jsx
<Link to="new-path" state={{ some: "value" }} />
```

Вы можете получить доступ к этому значению состояния, находясь на маршруте «новый путь»:

```jsx
let { state } = useLocation();
```

reloadDocument
Свойство reloadDocument можно использовать, чтобы пропустить маршрутизацию на стороне клиента и позволить браузеру выполнить переход обычным образом (как если бы это было свойство \<a href>).

viewTransition
Свойство viewTransition позволяет использовать переход вида для этой навигации, заключив окончательное обновление состояния в document.startViewTransition():

```jsx
<Link to={to} viewTransition>
  Click me
</Link>
```

Если вам нужно применить специальные стили для этого перехода, вам также потребуется использовать хук useViewTransitionState() (или проверить класс transitioning и свойство isTransitioning рендеринга в NavLink):

```jsx
function ImageLink(to) {
  const isTransitioning = useViewTransitionState(to);
  return (
    \<Link to={to} viewTransition>
      <p
        style={{
          viewTransitionName: isTransitioning
            ? "image-title"
            : "",
        }}
      >
        Image Number {idx}
      </p>
      <img
        src={src}
        alt={`Img ${idx}`}
        style={{
          viewTransitionName: isTransitioning
            ? "image-expand"
            : "",
        }}
      />
    </Link>
  );
}
```

viewTransition работает только при использовании маршрутизатора данных, см. раздел Выбор маршрутизатора



[Вернуться к содержанию](./index.md)