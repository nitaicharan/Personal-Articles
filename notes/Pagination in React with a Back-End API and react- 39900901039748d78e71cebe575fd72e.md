# Pagination in React with a Back-End API and react-router’s Hooks API

Created: December 22, 2021 8:12 AM
Finished: No
Source: https://firxworx.com/blog/coding/react/pagination-in-react-with-a-back-end-api-and-react-routers-hooks-api/
Subjects: react
Tags: #react

This post discusses implementing pagination in React with a back-end API that supports it, as well as using react-router’s hooks to manage the page URL.

A codesandbox with an example implementation is available at the following URL:

[https://codesandbox.io/s/api-pagination-with-react-router-hooks-i0912](https://codesandbox.io/s/api-pagination-with-react-router-hooks-i0912)

There are *tons* of examples and tutorials for React on the web that demonstrate paging/sorting/filtering on the client side. However, this is not always a good approach because the *entire* data set must be loaded from the back-end API for this approach to work. This is especially an issue with large data sets!

By handling paging/sorting/filtering on the server, the client is sent only the subset of data necessary to present to the user.

The example also covers an often-forgotten point that impacts UI/UX: the page URL should be updated whenever a user changes paging/sorts/filters. A given URL should generally correspond to a given “view” of the data. This ensures that users can directly link to URL’s, bookmark them, and that the browser’s back button works as expected.

Breaking the back button is a major issue found in a huge number of JavaScript-stack Single Page Apps (SPA’s)!

URL management is accomplished via use of the newer built-in [URLSearchParams API](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) plus the `useLocation()` and `useHistory()` hooks implemented by `react-router-dom`.

Note that the URLSearchParams API is widely supported by modern browsers including Microsoft Edge, but not IE, although there is a polyfill available.

The example code essentially uses the URL as a store of state: it stores the current page (`page`) and the number of results per page (`limit`) as URL search parameters.

## Demo API

The API used for the example is the popular [JSONPlaceholder API](https://jsonplaceholder.typicode.com/). This free public API features endpoints that return dummy data that developers can use to mock-up front-end apps.

JSONPlaceholder’s API supports adding `_page` and `_limit` parameters to requests that instruct the API to return a subset of data.

## Code Walkthrough

Refer to the [codesandbox](https://codesandbox.io/s/api-pagination-with-react-router-hooks-i0912).

### App.tsx

In *App.tsx* we setup react-router v5.2 with a representative setup that uses the `Router`, `Switch`, and `Route` components.

### api.ts

In *api.ts* we implement a `fetchData()` function that wraps a call to the JavaScript `fetch` API and formats the response as a `ResponseData` object that’s defined as follows:

```
// data structure of items returned by the API
export interface DataItem {
  userId: number;
  id: number;
  title: string;
  completed: boolean;
}

// return type of the fetchData() function
export interface ResponseData {
  items: DataItem[];
  count: number;
}

```

The JSONPlaceholder API returns a total result count via an `X-Total-Count` response header. The `fetchData()` function includes this count as part of the `ResponseData` object that it returns.

Take note that the `generateQueryString()` helper function adds the preceding underscore character to the URL parameters as required by the JSONPlaceholder API.

### Paged.tsx

In *Paged.tsx* we use the `useLocation()` and `useHistory()` hooks provided by the `react-router-dom` package to get the current URL search parameters (location via `location.search`) and set the updated URL (history via `history.push()`).

To keep the codesandbox example straightforward, I took advantage of the `TablePagination` component provided by [Material UI](https://material-ui.com/components/pagination/) rather than implement my own. It is important to note that the `TablePagination` component assumes a 0-based page number while the JSONPlaceholder API assumes a 1-based page number. This difference is accounted for in the code.

An advantage of using a library such as Material UI is that details such as keyboard navigation that improve usability and accessibility are implemented out-of-the-box so that you can focus on building your app.

The use of React’s `useMemo()` hook is important to prevent an infinite re-render loop. Otherwise, a new `urlParams` would be created on every render, and not just when `location.search` changes.

When using the URLSearchParams API it is important to note that it only stores data as strings. You’ll note that explicit conversions are applied throughout for the numerical parameters `page` and `limit`. It is especially important to be aware of this if you are storing boolean values because the string “false” is truthy in JavaScript.

Data is fetched with the help of the the `useAsync()` hook provided by the [react-async-hook](https://github.com/slorber/react-async-hook) package. This library helps avoid certain issues inherent to fetching asynchronous data. An excellent blog post by library author Sébastien Lorber explains the motivation for creating this library: [Handling API Request Race Conditions in React](https://sebastienlorber.com/handling-api-request-race-conditions-in-react). Another great post that was inspired by Lorber’s is by Marcin Wanago: [Race Conditions in React and Beyond: A Race Condition Guard with TypeScript](https://wanago.io/2020/03/02/race-conditions-in-react-and-beyond-a-race-condition-guard-with-typescript/).

As a design decision, I return the user to the first page if they change `rowsPerPage`. However, you may wish to implement something different.

Finally it is worth noting that I updated the `tsconfig.json` file vs. the current defaults for the React + TypeScript template on codesandbox. The entries “dom.iterable” and “esnext” have been added to the `compilerOptions` > `lib` array to support syntax such calling as `Array.from()` on an instance of `URLSearchParams`.

Hopefully this post and my example helps you implement usable pagination in React!