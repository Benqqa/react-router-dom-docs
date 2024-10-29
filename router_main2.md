# React Router: Навигация в React-приложениях

## 1. Введение в React Router

### Что такое клиентская маршрутизация
Клиентская маршрутизация позволяет управлять отображением компонентов в зависимости от URL без перезагрузки страницы. Это создает более плавный пользовательский опыт, характерный для одностраничных приложений (SPA).

### Зачем нужен React Router
React Router - это стандартная библиотека маршрутизации для React, которая позволяет:
- Создавать многостраничную навигацию в SPA
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/home" component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример создания многостраничной навигации с использованием React Router */

## 3. Базовая настройка
- Установка пакета
  ```bash
  npm install react-router-dom
  ```
  / Установка пакета react-router-dom */

- Настройка роутера
  ```jsx
  import { BrowserRouter as Router } from 'react-router-dom';

  function App() {
    return (
      <Router>
        {/* Ваши маршруты */}
      </Router>
    );
  }
  ```
  / Пример настройки роутера */

- Создание базовой структуры маршрутов
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/home" component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример создания базовой структуры маршрутов */

- Обработка несуществующих маршрутов (404)
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function NotFound() {
    return <h2>404 Not Found</h2>;
  }

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/home" component={Home} />
          <Route path="/about" component={About} />
          <Route path="*" component={NotFound} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример обработки несуществующих маршрутов */

  - Вложенные маршруты (Nested Routes)
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function Dashboard() {
    return (
      <div>
        <h2>Dashboard</h2>
        <Switch>
          <Route path="/dashboard/profile" component={Profile} />
          <Route path="/dashboard/settings" component={Settings} />
        </Switch>
      </div>
    );
  }

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/dashboard" component={Dashboard} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример создания вложенных маршрутов */

- Защищенные маршруты (Protected Routes)
  ```jsx
  import { BrowserRouter as Router, Route, Redirect } from 'react-router-dom';

  function PrivateRoute({ component: Component, ...rest }) {
    return (
      <Route
        {...rest}
        render={(props) =>
          fakeAuth.isAuthenticated ? (
            <Component {...props} />
          ) : (
            <Redirect to="/login" />
          )
        }
      />
    );
  }

  function App() {
    return (
      <Router>
        <PrivateRoute path="/protected" component={ProtectedComponent} />
      </Router>
    );
  }
  ```
  / Пример создания защищенных маршрутов */


## 5. Оптимизация и лучшие практики
- Ленивая загрузка компонентов (React.lazy)
  ```jsx
  import React, { Suspense, lazy } from 'react';
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  const Home = lazy(() => import('./Home'));
  const About = lazy(() => import('./About'));

  function App() {
    return (
      <Router>
        <Suspense fallback={<div>Loading...</div>}>
          <Switch>
            <Route path="/home" component={Home} />
            <Route path="/about" component={About} />
          </Switch>
        </Suspense>
      </Router>
    );
  }
  ```
  / Пример ленивой загрузки компонентов */

- Предзагрузка маршрутов
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function preloadComponent(Component) {
    return (props) => {
      Component.preload();
      return <Component {...props} />;
    };
  }

  const Home = React.lazy(() => import('./Home'));
  const About = React.lazy(() => import('./About'));

  Home.preload = () => import('./Home');
  About.preload = () => import('./About');

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/home" component={preloadComponent(Home)} />
          <Route path="/about" component={preloadComponent(About)} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример предзагрузки маршрутов */

- Мультиязычные сайты
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/en/home" component={Home} />
          <Route path="/fr/home" component={HomeFR} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример мультиязычного сайта */

## 7. Интеграция с другими инструментами
- Redux и React Router
  ```jsx
  import { Provider } from 'react-redux';
  import { BrowserRouter as Router } from 'react-router-dom';
  import { store } from './store';

  function App() {
    return (
      <Provider store={store}>
        <Router>
          {/* Ваши маршруты */}
        </Router>
      </Provider>
    );
  }
  ```
  / Пример интеграции Redux и React Router */

- TypeScript поддержка
  ```tsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  const Home: React.FC = () => <div>Home</div>;
  const About: React.FC = () => <div>About</div>;

  const App: React.FC = () => {
    return (
      <Router>
        <Switch>
          <Route path="/home" component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </Router>
    );
  };
  ```
  / Пример использования React Router с TypeScript */

- Тестирование маршрутизации
  ```jsx
  import { render } from '@testing-library/react';
  import { BrowserRouter as Router } from 'react-router-dom';

  test('renders home component', () => {
    const { getByText } = render(
      <Router>
        <Home />
      </Router>
    );

    expect(getByText(/home/i)).toBeInTheDocument();
  });
  ```
  / Пример тестирования маршрутизации */

- Серверный рендеринг (SSR)
  ```jsx
  import { StaticRouter } from 'react-router-dom/server';

  function App({ location }) {
    return (
      <StaticRouter location={location}>
        <Switch>
          <Route path="/home" component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </StaticRouter>
    );
  }
  ```
  / Пример использования React Router для серверного рендеринга */

[Вернуться к содержанию](./index.md)