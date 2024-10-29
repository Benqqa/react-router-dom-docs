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

- Синхронизировать UI с URL
  ```jsx
  import { Link } from 'react-router-dom';

  function Navigation() {
    return (
      <nav>
        <Link to="/home">Home</Link>
        <Link to="/about">About</Link>
      </nav>
    );
  }
  ```
  / Пример синхронизации UI с URL с помощью компонента Link */

- Управлять историей браузера
  ```jsx
  import { useHistory } from 'react-router-dom';

  function NavigateButton() {
    let history = useHistory();

    function handleClick() {
      history.push('/home');
    }

    return (
      <button type="button" onClick={handleClick}>
        Go Home
      </button>
    );
  }
  ```
  / Пример управления историей браузера с помощью useHistory */
  / Методы history:
    - push: добавляет новый маршрут в историю
    - replace: заменяет текущий маршрут на новый
    - go: перемещается на определенное количество записей в истории
    - goBack: перемещается назад на одну запись в истории
    - goForward: перемещается вперед на одну запись в истории
    - listen: позволяет подписаться на изменения в истории
    - block: блокирует изменения в истории, пока не будет вызван коллбек
  / Пример управления историей браузера с помощью useHistory */

- Обрабатывать параметры URL
  ```jsx
  import { useParams } from 'react-router-dom';

  function UserProfile() {
    let { userId } = useParams();

    return <div>User ID: {userId}</div>;
  }
  ```
  / Пример обработки параметров URL с помощью useParams */

## 2. Основные концепции

### BrowserRouter vs HashRouter
BrowserRouter использует HTML5 History API, а HashRouter использует хэш в URL.
  ```jsx
  import { BrowserRouter, HashRouter } from 'react-router-dom';

  function App() {
    return (
      <BrowserRouter>
        <Switch>
          <Route path="/home" component={Home} />
        </Switch>
      </BrowserRouter>
    );
  }

  function AppWithHash() {
    return (
      <HashRouter>
        <Switch>
          <Route path="/home" component={Home} />
        </Switch>
      </HashRouter>
    );
  }
  ```
  / Пример использования BrowserRouter и HashRouter */

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

## 4. Продвинутые возможности
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

- Программная навигация
  ```jsx
  import { useHistory } from 'react-router-dom';

  function NavigateButton() {
    let history = useHistory();

    function handleClick() {
      history.push('/home');
    }

    return (
      <button type="button" onClick={handleClick}>
        Go Home
      </button>
    );
  }
  ```
  / Пример программной навигации */

- Передача состояния между маршрутами
  ```jsx
  import { useHistory } from 'react-router-dom';

  function Home() {
    let history = useHistory();

    function handleClick() {
      history.push('/about', { from: 'Home' });
    }

    return (
      <button type="button" onClick={handleClick}>
        Go to About
      </button>
    );
  }

  function About() {
    let location = useLocation();

    return <div>{location.state?.from}</div>;
  }
  ```
  / Пример передачи состояния между маршрутами */

- Обработка query-параметров
  ```jsx
  import { useLocation } from 'react-router-dom';

  function useQuery() {
    return new URLSearchParams(useLocation().search);
  }

  function MyComponent() {
    let query = useQuery();

    return <div>Query param: {query.get('param')}</div>;
  }
  ```
  / Пример обработки query-параметров */

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

- Обработка загрузки данных
  ```jsx
  import { useEffect, useState } from 'react';
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function DataFetchingComponent() {
    const [data, setData] = useState(null);

    useEffect(() => {
      fetch('/api/data')
        .then((response) => response.json())
        .then((data) => setData(data));
    }, []);

    if (!data) {
      return <div>Loading...</div>;
    }

    return <div>Data: {data}</div>;
  }

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/data" component={DataFetchingComponent} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример обработки загрузки данных */

- Сохранение состояния при навигации
  ```jsx
  import { useState } from 'react';
  import { BrowserRouter as Router, Route, Switch, useLocation } from 'react-router-dom';

  function usePreserveScroll() {
    const location = useLocation();
    const [scrollPositions, setScrollPositions] = useState({});

    useEffect(() => {
      if (scrollPositions[location.key]) {
        window.scrollTo(0, scrollPositions[location.key]);
      }

      return () => {
        setScrollPositions((prev) => ({
          ...prev,
          [location.key]: window.scrollY,
        }));
      };
    }, [location]);

    return null;
  }

  function App() {
    return (
      <Router>
        <usePreserveScroll />
        <Switch>
          <Route path="/home" component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример сохранения состояния при навигации */

## 6. Типичные сценарии использования
- Многостраничные приложения
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function App() {
    return (
      <Router>
        <Switch>
          <Route path="/home" component={Home} />
          <Route path="/about" component={About} />
          <Route path="/contact" component={Contact} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример многостраничного приложения */

- Панели администратора
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function AdminPanel() {
    return (
      <Router>
        <Switch>
          <Route path="/admin/dashboard" component={Dashboard} />
          <Route path="/admin/users" component={Users} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример панели администратора */

- Пользовательские кабинеты
  ```jsx
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

  function UserAccount() {
    return (
      <Router>
        <Switch>
          <Route path="/account/profile" component={Profile} />
          <Route path="/account/settings" component={Settings} />
        </Switch>
      </Router>
    );
  }
  ```
  / Пример пользовательского кабинета */

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

## 8. Распространенные проблемы и их решения
- Обновление страницы на продакшене
  / Убедитесь, что ваш сервер настроен на обработку всех маршрутов, возвращая index.html для всех запросов */

- Работа с историей браузера
  / Используйте BrowserRouter для работы с историей браузера */

- SEO оптимизация
  / Используйте серверный рендеринг (SSR) для улучшения SEO */

- Кэширование данных между маршрутами
  / Используйте React Context или Redux для кэширования данных между маршрутами */



[Вернуться к содержанию](./index.md)