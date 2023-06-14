React Router is a collection of navigational components that work with a React App to allow navigation between pages. It's one of the most commonly used libraries in React for handling routing.

The most recent version of React Router is version 6. You're just as likely to encounter version 5 and there are some similarities and differences we will explore with each of their usage.

## Part 1: Overview of React Router

### Version 5:

In version 5, the fundamental components that you will interact with are:

-   `<BrowserRouter />`: A `<Router>` that uses the HTML5 history API (pushState, replaceState and the popstate event) to keep your UI in sync with the URL. It's basically sits on top of the native browser history API and gives us the tools to interact with it in our React app.

-   `<Route />`: Its most basic responsibility is to render some UI when a location matches the route’s path.

-   `<Link />`: Provides declarative, accessible navigation around your application. This should be used in place of an `<a>` tag in order to prevent a full reload of the page (unless that's what you want). A `<Link />` component navigates between routes without reloading the entire app so you can maintain local state.

-   `<Switch />`: Renders the first `<Route>` or `<Redirect>` that matches the location. It acts as a filter for the url path requested. In the example below, if a user entered `http://localhost:3000/about` they would see the `<About>` component. Going to `http://localhost:3000/unknown_route` would result in a blank page or error since there is no route path that matches `unknown_route`.

An example of how to use these components:

```jsx
import { BrowserRouter as Router, Route, Link, Switch } from 'react-router-dom';

function App() {
	return (
		<Router>
			<div>
				<nav>
					<ul>
						<li>
							<Link to='/'>Home</Link>
						</li>
						<li>
							<Link to='/about'>About</Link>
						</li>
					</ul>
				</nav>

				<Switch>
					<Route path='/about'>
						<About />
					</Route>
					<Route path='/'>
						<Home />
					</Route>
				</Switch>
			</div>
		</Router>
	);
}

function Home() {
	return <h2>Home</h2>;
}

function About() {
	return <h2>About</h2>;
}
```

<div style="position: relative; padding-bottom: 64.98194945848375%; height: 0;"><iframe src="https://www.loom.com/embed/3682020e20db48eabad29aff4a3cb68c?sid=75aff5f1-3b45-408b-89c2-1515c13cca11" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe></div>

### Version 6:

In version 6, the API has changed a bit, but the concept is still the same. Here are some equivalent components in version 6:

-   `<BrowserRouter />` is now `<Routes />`: This component is used to wrap the entire application and provide routing functionality.

-   `<Route />` remains `<Route />`: However, it now takes a `path` and an `element` prop. The `element` prop is expected to be a React element (i.e., `<Component />`), not a component type (i.e., `Component`).

-   `<Link />` remains `<Link />`: However, the `to` prop is now `href`.

-   `<Switch />` is replaced with `<Routes />`: It's used to group `<Route />` components.

Here's the same example as above, but using version 6 syntax:

```jsx
import { BrowserRouter as Router, Route, Link, Routes } from 'react-router-dom';

function App() {
	return (
		<Router>
			<div>
				<nav>
					<ul>
						<li>
							<Link to='/'>Home</Link>
						</li>
						<li>
							<Link to='/about'>About</Link>
						</li>
					</ul>
				</nav>

				<Routes>
					<Route path='/about' element={<About />} />
					<Route path='/' element={<Home />} />
				</Routes>
			</div>
		</Router>
	);
}

function Home() {
	return <h2>Home</h2>;
}

function About() {
	return <h2>About</h2>;
}
```

## Part 2: Route Parameters

Another important aspect of React Router is route parameters, which allow you to capture values from the URL.

In version 5, you can define a route parameter by using a colon (`:`) followed by the name of the parameter inside the `path` prop of the `Route` component. You can then access this parameter via the `match.params` property passed to your component.

You may be wondering how props like `match` are passed as props. This is done automatically by React Router 😉.

```jsx
<Route path='/user/:id' component={User} />;

// User Component
function User({ match }) {
	return <h2>User Id: {match.params.id}</h2>;
}
```

In version 6, you define a route parameter in the same way, but access it a bit differently. You use the `useParams` hook from `react-router-dom` to access the route parameters and there is no need to access it via `props`.

```jsx
<Route path='/user/:id' element={<User />} />;

// User Component
import { useParams } from 'react-router-dom';

function User() {
	let { id } = useParams();
	return <h2>User Id: {id}</h2>;
}
```

## Part 3: Navigation

For programmatically navigating around your application, React Router provides a `history` object. Sometimes you may want to navigate a user to a page after completing an action like signing into your app. Using a `<Link>` doesn't make sense here since it's a UI element. Navigating manually (or programatically) means you should use the `history` object.

In version 5, you can use the `history.push` method to navigate to a different path. This `history` object is available in the props of any component rendered by a `Route`.

```jsx
function Home({ history }) {
	return (
		<div>
			<h2>Home</h2>
			<button onClick={() => history.push('/about')}>Go to About</button>
		</div>
	);
}
```

In version 6, the `history` object is replaced by a few hooks:

-   `useNavigate` for navigation
-   `useLocation` to access the current location
-   `useHistory` to access the navigation history

Here's how you would navigate in version 6:

```jsx
import { useNavigate } from 'react-router-dom';

function Home() {
	const navigate = useNavigate();

	return (
		<div>
			<h2>Home</h2>
			<button onClick={() => navigate('/about')}>Go to About</button>
		</div>
	);
}
```

<div style="position: relative; padding-bottom: 64.98194945848375%; height: 0;"><iframe src="https://www.loom.com/embed/5a54a1bfa12f4700aa398fae4a88fe6a?sid=a8d6bf30-6dc3-4286-8c07-0101b767fe52" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe></div>

## Part 4: Why You Might Need to Access the URL in an App

It's common for web applications to use pieces of a URL to understand what content should be rendered.

Let's break down a couple of key reasons why developers might need to access these:

1. **Dynamic Content Loading**: By using route parameters or query strings, you can load content dynamically based on the URL. This is often used in e-commerce sites, blogs, or any site that has a lot of categorized content. For instance, you might have a URL like `www.example.com/product/:id` where `:id` is the ID of a product. When a user navigates to this URL, your React application could fetch the specific product's data and display it.

2. **Filtering and Sorting**: Query parameters are a great way to handle filtering and sorting of data. For example, in an e-commerce site, you might filter products by category or sort them by price. This could result in a URL like `www.example.com/products?category=electronics&sort=price-asc`. By reading these query parameters, your React application can then display the appropriate products.

3. **Preserving Application State**: Sometimes you want to preserve certain application states in the URL so that when the user refreshes the page or shares the URL, the application will render the same state. An example could be a map application where the current coordinates and zoom level are stored in the URL.

Now let's look at some examples of how you might accomplish these in React with React Router.

## Accessing Params

In a blog application, you might want to display a specific post based on the URL. Here's how you might do it in both React Router v5 and v6:

### Version 5:

```jsx
<Route path="/post/:id" component={Post} />

// Post Component
function Post({ match }) {
  const postId = match.params.id;

  // Fetching data based on postId
  useEffect(() => {
    fetch(`/api/posts/${postId}`)
      .then(response => response.json())
      .then(data => /* do something with the data */);
  }, [postId]);

  // ...
}
```

### Version 6:

```jsx
<Route path="/post/:id" element={<Post />} />

// Post Component
import { useParams } from "react-router-dom";

function Post() {
  const { id: postId } = useParams();

  // Fetching data based on postId
  useEffect(() => {
    fetch(`/api/posts/${postId}`)
      .then(response => response.json())
      .then(data => /* do something with the data */);
  }, [postId]);

  // ...
}
```

## With a Query String

For example, to handle sorting of posts in a blog application, you might use a query parameter. Here's how you might do it with a url like `www.yourblog.com/posts?sort=recent`:

### Version 5:

```jsx
// Assuming you are in a component rendered by a Route
function Posts({ location }) {
  const query = new URLSearchParams(location.search);
  const sort = query.get('sort');

  // Fetching data based on sort
  useEffect(() => {
    fetch(`/api/posts?sort=${sort}`)
      .then(response => response.json())
      .then(data => /* do something with the data */);
  }, [sort]);

  // ...
}
```

### Version 6:

```jsx
// Assuming you are in a component rendered by a Route
import { useLocation } from "react-router-dom";

function Posts() {
  const { search } = useLocation();
  const query = new URLSearchParams(search);
  const sort = query.get('sort');

  // Fetching data based on sort
  useEffect(() => {
    fetch(`/api/posts?sort=${sort}`)
      .then

(response => response.json())
      .then(data => /* do something with the data */);
  }, [sort]);

  // ...
}
```

In the examples above, we're accessing URL parameters or the query string and then fetching data based on these values. This allows us to display content specific to each user's navigation, providing a more personalized and dynamic user experience.

Remember that both route parameters and query strings are part of the URL and thus are visible to users. Make sure not to put sensitive information in them. Also, because users can manually change these values, ensure your application can handle unexpected values gracefully.
