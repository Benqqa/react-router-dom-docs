# Navigate

/ Navigate используется для программной навигации между маршрутами. Он позволяет изменять текущий маршрут в ответ на действия пользователя или события. */ 

Объявление типа

```ts
declare function Navigate(props: NavigateProps): null;


interface NavigateProps {
  to: To;
  replace?: boolean;
  state?: any;
  relative?: RelativeRoutingType;
}
```

Элемент \<Navigate> изменяет текущее местоположение при отображении. Это компонент-оболочка для useNavigate и принимает все те же аргументы, что и props.

Наличие версии хука useNavigate на основе компонентов упрощает использование этой функции в подклассе React.Component там, где хуки не могут быть использованы.

```jsx
import * as React from "react";
import { Navigate } from "react-router-dom";

class LoginForm extends React.Component {
  state = { user: null, error: null };

  async handleSubmit(event) {
    event.preventDefault();
    try {
      let user = await login(event.target);
      this.setState({ user });
    } catch (error) {
      this.setState({ error });
    }
  }

  render() {
    let { user, error } = this.state;
    return (
      <div>
        {error && <p>{error.message}</p>}
        {user && (
          <Navigate to="/dashboard" replace={true} />
        )}
        <form
          onSubmit={(event) => this.handleSubmit(event)}
        >
          <input type="text" name="username" />
          <input type="password" name="password" />
        </form>
      </div>
    );
  }
}
```



[Вернуться к содержанию](./index.md)