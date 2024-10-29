# NavLink

/ NavLink используется для создания навигационных ссылок с активным состоянием. Он позволяет выделять текущий маршрут в навигационном меню. */ 


<NavLink> — это особый вид \<Link>, который определяет, является ли он «активным», «ожидает обработки» или «переходит в другое состояние». Это полезно в нескольких различных сценариях:

При создании меню навигации, например, в виде хлебных крошек или набора вкладок, где вы хотите показать, какая из них выбрана в данный момент
Оно предоставляет полезный контекст для вспомогательных технологий, таких как программы чтения с экрана
Он обеспечивает «переходный» режим, чтобы вы могли более точно управлять переходами между представлениями

```jsx
import { NavLink } from "react-router-dom";

<NavLink
  to="/messages"
  className={({ isActive, isPending }) =>
    isPending ? "pending" : isActive ? "active" : ""
  }
>
  Messages
</NavLink>;
```

Класс по умолчанию active
По умолчанию к компоненту active добавляется класс \<NavLink>, когда он активен, чтобы вы могли использовать CSS для его оформления.

```jsx
<nav id="sidebar">
  <NavLink to="/messages" />
</nav>
#sidebar a.active {
  color: red;
}
```

className
Свойство className работает как обычное имя класса, но вы также можете передать ему функцию для настройки имён классов в зависимости от активного и ожидающего состояния ссылки.

```jsx
<NavLink
  to="/messages"
  className={({ isActive, isPending, isTransitioning }) =>
    [
      isPending ? "pending" : "",
      isActive ? "active" : "",
      isTransitioning ? "transitioning" : "",
    ].join(" ")
  }
>
  Messages
</NavLink>
```

style
Свойство style работает как обычное свойство стиля, но вы также можете передать ему функцию для настройки стилей в зависимости от активного и ожидающего состояния ссылки.

```jsx
<NavLink
  to="/messages"
  style={({ isActive, isPending, isTransitioning }) => {
    return {
      fontWeight: isActive ? "bold" : "",
      color: isPending ? "red" : "black",
      viewTransitionName: isTransitioning ? "slide" : "",
    };
  }}
>
  Messages
</NavLink>
```

children
Вы можете передать опору для рендеринга в качестве дочернего элемента, чтобы настроить содержимое <NavLink> в зависимости от активного и ожидающего состояния. Это полезно для изменения стилей внутренних элементов.

```jsx
<NavLink to="/tasks">
  {({ isActive, isPending, isTransitioning }) => (
    <span className={isActive ? "active" : ""}>Tasks</span>
  )}
</NavLink>
```

end
Свойство end изменяет логику сопоставления для состояний active и pending таким образом, чтобы они сопоставлялись только с «концом» пути to NavLink. Если URL-адрес длиннее, чем to, он больше не будет считаться активным.

Ссылка	Текущий URL	Активен

```jsx
<NavLink to="/tasks" />	/tasks	верно
<NavLink to="/tasks" />	/tasks/123	верно
<NavLink to="/tasks" end />	/tasks	верно
<NavLink to="/tasks" end />	/tasks/123	ложь
<NavLink to="/tasks/" end />	/tasks	ложь
<NavLink to="/tasks/" end />	/tasks/	верно
```

Примечание о ссылках на корневой маршрут

<NavLink to="/"> Это исключительный случай, потому что каждый URL-адрес соответствует /. Чтобы избежать такого соответствия каждому маршруту по умолчанию, он фактически игнорирует свойство end и соответствует только в том случае, если вы находитесь на корневом маршруте.

caseSensitive
Добавление caseSensitive prop изменяет логику сопоставления, делая ее чувствительной к регистру.

Ссылка	URL	Активен

```jsx
<NavLink to="/SpOnGe-bOB" />	/sponge-bob	верно
<NavLink to="/SpOnGe-bOB" caseSensitive />	/sponge-bob	ложь
```
aria-current
Когда NavLink активен, он автоматически применяет \<a aria-current="page"> к базовому тегу привязки. См. aria-current на MDN.

reloadDocument
Свойство reloadDocument можно использовать, чтобы пропустить маршрутизацию на стороне клиента и позволить браузеру выполнить переход обычным образом (как если бы это было свойство \<a href>).

viewTransition
Свойство viewTransition позволяет включить переход вида для этой навигации, заключив окончательное обновление состояния в document.startViewTransition(). По умолчанию во время перехода к элементу transitioning добавляется класс \<a>, который можно использовать для настройки перехода вида.

```jsx
a.transitioning p {
  view-transition-name: "image-title";
}

a.transitioning img {
  view-transition-name: "image-expand";
}
<NavLink to={to} viewTransition>
  <p>Image Number {idx}</p>
  <img src={src} alt={`Img ${idx}`} />
</NavLink>
```

Вы также можете использовать свойства className/style или свойства рендеринга, переданные в children, для дальнейшей настройки в зависимости от значения isTransitioning

```jsx
<NavLink to={to} viewTransition>
  {({ isTransitioning }) => (
    <>
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
    </>
  )}
</NavLink>
```



[Вернуться к содержанию](./index.md)