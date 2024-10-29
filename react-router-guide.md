 Route
Routes are perhaps the most important part of a React Router app. They couple URL segments to components, data loading and data mutations. Through route nesting, complex application layouts and data dependencies become simple and declarative.

Routes are objects passed to the router creation functions:

const router = createBrowserRouter([
  {
    // it renders this element
    element: <Team />,

    // when the URL matches this segment
    path: "teams/:teamId",

    // with this data loaded before rendering
    loader: async ({ request, params }) => {
      return fetch(
        `/fake/api/teams/${params.teamId}.json`,
        { signal: request.signal }
      );
    },

    // performing this mutation when data is submitted to it
    action: async ({ request }) => {
      return updateFakeTeam(await request.formData());
    },

    // and renders this element in case something went wrong
    errorElement: <ErrorBoundary />,
  },
]);
You can also declare your routes with JSX and createRoutesFromElements, the props to the element are identical to the properties of the route objects:

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      element={<Team />}
      path="teams/:teamId"
      loader={async ({ params }) => {
        return fetch(
          `/fake/api/teams/${params.teamId}.json`
        );
      }}
      action={async ({ request }) => {
        return updateFakeTeam(await request.formData());
      }}
      errorElement={<ErrorBoundary />}
    />
  )
);
Neither style is discouraged and behavior is identical. For the majority of this doc we will use the JSX style because that's what most people are accustomed to in the context of React Router.

When using RouterProvider, if you do not wish to specify a React element (i.e., element={<MyComponent />}) you may specify a Component instead (i.e., Component={MyComponent}) and React Router will call createElement for you internally. You should only do this for RouterProvider applications though since using Component inside of <Routes> will de-optimize React's ability to reuse the created element across renders.

Type declaration
interface RouteObject {
  path?: string;
  index?: boolean;
  children?: RouteObject[];
  caseSensitive?: boolean;
  id?: string;
  loader?: LoaderFunction;
  action?: ActionFunction;
  element?: React.ReactNode | null;
  hydrateFallbackElement?: React.ReactNode | null;
  errorElement?: React.ReactNode | null;
  Component?: React.ComponentType | null;
  HydrateFallback?: React.ComponentType | null;
  ErrorBoundary?: React.ComponentType | null;
  handle?: RouteObject["handle"];
  shouldRevalidate?: ShouldRevalidateFunction;
  lazy?: LazyRouteFunction<RouteObject>;
}
path
The path pattern to match against the URL to determine if this route matches a URL, link href, or form action.

Dynamic Segments
If a path segment starts with : then it becomes a "dynamic segment". When the route matches the URL, the dynamic segment will be parsed from the URL and provided as params to other router APIs.

<Route
  // this path will match URLs like
  // - /teams/hotspur
  // - /teams/real
  path="/teams/:teamId"
  // the matching param will be available to the loader
  loader={({ params }) => {
    console.log(params.teamId); // "hotspur"
  }}
  // and the action
  action={({ params }) => {}}
  element={<Team />}
/>;

// and the element through `useParams`
function Team() {
  let params = useParams();
  console.log(params.teamId); // "hotspur"
}
You can have multiple dynamic segments in one route path:

<Route path="/c/:categoryId/p/:productId" />;
// both will be available
params.categoryId;
params.productId;
Dynamic segments cannot be "partial":

🚫 "/teams-:teamId"
✅ "/teams/:teamId"
🚫 "/:category--:productId"
✅ "/:productSlug"
You can still support URL patterns like that, you just have to do a bit of your own parsing:

function Product() {
  const { productSlug } = useParams();
  const [category, product] = productSlug.split("--");
  // ...
}
Optional Segments
You can make a route segment optional by adding a ? to the end of the segment.

<Route
  // this path will match URLs like
  // - /categories
  // - /en/categories
  // - /fr/categories
  path="/:lang?/categories"
  // the matching param might be available to the loader
  loader={({ params }) => {
    console.log(params["lang"]); // "en"
  }}
  // and the action
  action={({ params }) => {}}
  element={<Categories />}
/>;

// and the element through `useParams`
function Categories() {
  let params = useParams();
  console.log(params.lang);
}
You can have optional static segments, too:

<Route path="/project/task?/:taskId" />
Splats
Also known as "catchall" and "star" segments. If a route path pattern ends with /* then it will match any characters following the /, including other / characters.

<Route
  // this path will match URLs like
  // - /files
  // - /files/one
  // - /files/one/two
  // - /files/one/two/three
  path="/files/*"
  // the matching param will be available to the loader
  loader={({ params }) => {
    console.log(params["*"]); // "one/two"
  }}
  // and the action
  action={({ params }) => {}}
  element={<Team />}
/>;

// and the element through `useParams`
function Team() {
  let params = useParams();
  console.log(params["*"]); // "one/two"
}
You can destructure the *, you just have to assign it a new name. A common name is splat:

let { org, "*": splat } = params;
Layout Routes
Omitting the path makes this route a "layout route". It participates in UI nesting, but it does not add any segments to the URL.

<Route
  element={
    <div>
      <h1>Layout</h1>
      <Outlet />
    </div>
  }
>
  <Route path="/" element={<h2>Home</h2>} />
  <Route path="/about" element={<h2>About</h2>} />
</Route>
In this example, <h1>Layout</h1> will be rendered along with each child route's element prop, via the layout route's Outlet.

index
Determines if the route is an index route. Index routes render into their parent's Outlet at their parent's URL (like a default child route).

<Route path="/teams" element={<Teams />}>
  <Route index element={<TeamsIndex />} />
  <Route path=":teamId" element={<Team />} />
</Route>
These special routes can be confusing to understand at first, so we have a guide dedicated to them here: Index Route.

children
(TODO: need to talk about nesting, maybe even a separate doc)

caseSensitive
Instructs the route to match case or not:

<Route caseSensitive path="/wEll-aCtuA11y" />
Will match "wEll-aCtuA11y"
Will not match "well-actua11y"
loader
The route loader is called before the route renders and provides data for the element through useLoaderData.

<Route
  path="/teams/:teamId"
  loader={({ params }) => {
    return fetchTeam(params.teamId);
  }}
/>;

function Team() {
  let team = useLoaderData();
  // ...
}
If you are not using a data router like createBrowserRouter, this will do nothing

Please see the loader documentation for more details.

action
The route action is called when a submission is sent to the route from a Form, fetcher, or submission.

<Route
  path="/teams/:teamId"
  action={({ request }) => {
    const formData = await request.formData();
    return updateTeam(formData);
  }}
/>
If you are not using a data router like createBrowserRouter, this will do nothing

Please see the action documentation for more details.

element/Component
The React Element/Component to render when the route matches the URL.

If you want to create the React Element, use element:

<Route path="/for-sale" element={<Properties />} />
Otherwise use Component and React Router will create the React Element for you:

<Route path="/for-sale" Component={Properties} />
You should only opt into the Component API for data routes via RouterProvider. Using this API on a <Route> inside <Routes> will de-optimize React's ability to reuse the created element across renders.

errorElement/ErrorBoundary
When a route throws an exception while rendering, in a loader or in an action, this React Element/Component will render instead of the normal element/Component.

If you want to create the React Element on your own, use errorElement:

<Route
  path="/for-sale"
  // if this throws an error while rendering
  element={<Properties />}
  // or this while loading properties
  loader={() => loadProperties()}
  // or this while creating a property
  action={async ({ request }) =>
    createProperty(await request.formData())
  }
  // then this element will render
  errorElement={<ErrorBoundary />}
/>
Otherwise use ErrorBoundary and React Router will create the React Element for you:

<Route
  path="/for-sale"
  Component={Properties}
  loader={() => loadProperties()}
  action={async ({ request }) =>
    createProperty(await request.formData())
  }
  ErrorBoundary={ErrorBoundary}
/>
If you are not using a data router like createBrowserRouter, this will do nothing

Please see the errorElement documentation for more details.

hydrateFallbackElement/HydrateFallback
If you are using Server-Side Rendering and you are leveraging partial hydration, then you can specify an Element/Component to render for non-hydrated routes during the initial hydration of the application.

If you are not using a data router like createBrowserRouter, this will do nothing

This is only intended for more advanced uses cases such as Remix's clientLoader functionality. Most SSR apps will not need to leverage these route properties.

Please see the hydrateFallbackElement documentation for more details.

handle
Any application-specific data. Please see the useMatches documentation for details and examples.

lazy
In order to keep your application bundles small and support code-splitting of your routes, each route can provide an async function that resolves the non-route-matching portions of your route definition (loader, action, Component/element, ErrorBoundary/errorElement, etc.).

Each lazy function will typically return the result of a dynamic import.

let routes = createRoutesFromElements(
  <Route path="/" element={<Layout />}>
    <Route path="a" lazy={() => import("./a")} />
    <Route path="b" lazy={() => import("./b")} />
  </Route>
);
Then in your lazy route modules, export the properties you want defined for the route:

export async function loader({ request }) {
  let data = await fetchData(request);
  return json(data);
}

export function Component() {
  let data = useLoaderData();

  return (
    <>
      <h1>You made it!</h1>
      <p>{data}</p>
    </>
  );
}
If you are not using a data router like createBrowserRouter, this will do nothing

Please see the lazy documentation for more details.

[Вернуться к содержанию](./index.md)