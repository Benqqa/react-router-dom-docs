# Should Revalidate

/ Should Revalidate определяет, нужно ли повторно загружать данные при повторном посещении маршрута. Это позволяет контролировать кэширование данных и обновление пользовательского интерфейса. */ 

```jsx
Объявление типа
interface ShouldRevalidateFunction {
  (args: ShouldRevalidateFunctionArgs): boolean;
}

interface ShouldRevalidateFunctionArgs {
  currentUrl: URL;
  currentParams: AgnosticDataRouteMatch["params"];
  nextUrl: URL;
  nextParams: AgnosticDataRouteMatch["params"];
  formMethod?: Submission["formMethod"];
  formAction?: Submission["formAction"];
  formEncType?: Submission["formEncType"];
  text?: Submission["text"];
  formData?: Submission["formData"];
  json?: Submission["json"];
  actionResult?: any;
  actionStatus?: number;
  defaultShouldRevalidate: boolean;
}
```

Эта функция позволяет отказаться от повторной проверки загрузчика маршрута в целях оптимизации.

Эта функция работает только при использовании маршрутизатора, см. «Выбор маршрутизатора»

Существует несколько способов повторной проверки данных, которые автоматически синхронизируют ваш пользовательский интерфейс с данными:

После того, как action вызывается через:
\<Form>, \<fetcher.Form>, useSubmit, или fetcher.submit
Если флаг future.v7_skipActionErrorRevalidation включен, loaders по умолчанию не будет повторно проверять, если action возвращает или генерирует ошибку 4xx/5xx Response
Вы можете включить повторную проверку для этих сценариев с помощью shouldRevalidate и параметра actionStatus
Когда явная повторная проверка запускается через useRevalidator
Когда параметры URL изменяются для уже отрисованного маршрута
При изменении параметров поиска по URL
При переходе по тому же URL, что и текущий URL
Если вы определяете shouldRevalidate для маршрута, он сначала проверит функцию, прежде чем вызывать загрузчик маршрута для получения новых данных. Если функция возвращает false, то загрузчик не будет вызываться, а существующие данные для этого загрузчика сохранятся на странице.

Загрузки Fetcher также выполняют повторную проверку, но поскольку они загружают определенный URL, им не нужно беспокоиться о сценариях повторной проверки, основанных на URL, приведенных выше. Fetcher загружает только повторную проверку по умолчанию после отправки действий и явных запросов на повторную проверку.

```jsx
<Route
  path="meals-plans"
  element={<MealPlans />}
  loader={loadMealPlans}
  shouldRevalidate={({ currentUrl }) => {
    // only revalidate if the submission originates from
    // the `/meal-plans/new` route.
    return currentUrl.pathname === "/meal-plans/new";
  }}
>
  <Route
    path="new"
    element={<NewMealPlanForm />}
    // `loadMealPlans` will be revalidated after
    // this action...
    action={createMealPlan}
  />
  <Route
    path=":planId/meal"
    element={<Meal />}
    // ...but not this one because origin the URL
    // is not "/meal-plans/new"
    action={updateMeal}
  />
</Route>
```

Обратите внимание, что это относится только к данным, которые уже были загружены, отображаются в данный момент и будут отображаться по новому URL-адресу. Данные для новых маршрутов и обработчиков по новому URL-адресу всегда будут загружаться изначально.

Использование этого API может привести к тому, что ваш пользовательский интерфейс перестанет синхронизироваться с вашими данными. Используйте его с осторожностью!


[Вернуться к содержанию](./index.md)