# Outlet

/ Outlet используется для отображения вложенных маршрутов в приложении. Он позволяет создавать сложные структуры маршрутов с вложенными компонентами. */ 

Объявление типа

```ts
interface OutletProps {
  context?: unknown;
}
declare function Outlet(
  props: OutletProps
): React.ReactElement | null;
```

\<Outlet> следует использовать в элементах родительского маршрута для отображения элементов дочернего маршрута. Это позволяет отображать вложенные элементы пользовательского интерфейса при отображении дочерних маршрутов. Если родительский маршрут совпадает полностью, он отобразит дочерний индексный маршрут или ничего не отобразит, если индексного маршрута нет.

```jsx
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>

      {/* Этот элемент отобразит либо <DashboardMessages>, если URL-адрес равен 
 "/messages", <DashboardTasks> в "/tasks", либо null, если это "/"
      */}
      <Outlet />
    </div>
  );
}

function App() {
  return (
    <Routes>
      <Route path="/" element={<Dashboard />}>
        <Route
          path="messages"
          element={<DashboardMessages />}
        />
        <Route path="tasks" element={<DashboardTasks />} />
      </Route>
    </Routes>
  );
}
```



[Вернуться к содержанию](./index.md)