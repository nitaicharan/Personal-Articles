# Five Clever Hacks for React-Query or SWR

Created: July 5, 2022 10:20 AM
Finished: No
Source: https://jherr2020.medium.com/five-clever-hacks-for-react-query-or-swr-27c2453f4e2d
Tags: #react

![1*Xh8aAO_qTOqDTMq4GmZAqQ.jpeg](Five%20Clever%20Hacks%20for%20React-Query%20or%20SWR%20b7b6921569e64f69abe0e3391000c69f/1Xh8aAO_qTOqDTMq4GmZAqQ.jpeg)

So dealing with the double-render issue in React 18 has finally gotten you to use a API handling library like [react-query](https://react-query.tanstack.com/) or [swr](https://swr.vercel.app/). Awesome! But did you know you can get more out of that 12Kb (or 4Kb in the case of swr) than just API fetching? Here are five pretty novel uses for these awesome libraries.

# Prefer a Video?

If you prefer to watch your technical story then have a watch over on YouTube.

Five Clever Hacks for React-Query and SWR: The Video!

# Simplified Multiple Fetches

We tend to think of a `useQuery` hook as one hook per fetch. But let’s say you have two fetches to make. For example you have a login system where you first fetch to do the login and then fetch again to get the user information once you have their user ID.

You might start with something like this:

```
const fetchLogin = () => fetch("/login.json").json();
const fetchUser = (id) => fetch(`/${id}.json`).json();const MyComponent = () => {
  const {data: login } = useQuery("login",fetchLogin);
const {data: user } = useQuery(
    "user", () => fetchUser(login.id),
    { enabled: login?.id }
  );  return <div>{JSON.stringify(user)}</div>
}
```

In this model we cascade these two `useQuery`hooks. First we get the login, and then once the login is returned with a non-zero `id` then we `enable` the second query. Now… this works. But such pain! And imagine if it were more complex with three or more requests. There has to be a better way!

There is of course, we can just make a `login` function, like so:

```
const login = async () => {
  const resp = await fetch("/login.json");
  const { id } = await resp.json();  const userResp = await fetch(`/${id}.json`);
  const user = await userResp.json();
  return user;
};
```

And use that instead in our component.

```
const MyComponent = () => {
  const {data: user} = useQuery("login",login);
  return <div>{JSON.stringify(user)}</div>
}
```

You see, `useQuery` monitors any function, it could be a single `fetch` or it could be a function like this that makes multiple fetches with logic and such. Or it might not be a fetch at all (as we will soon see.) The point here is to start thinking outside the `fetch` box.

But before we leave the topic of `fetch` lets look at just two more handy variants.

For example, if you have an array of fetches to make in series you could do something like this:

```
const getTextData = async () => {
  const out = [];
  for (const name of ["a", "b", "c"]) {
    const resp = await fetch(`/data_${name}.json`);
    out.push(await resp.json());
  }
  return out;
};
...
const {data: textData} = useQuery("textData",getTextData);
```

In this case we are using a `for` loop to iterate through an array of values and then requesting the JSON for each of them before returning it all. BTW, if you like this example but don’t like `for` and you replace it with `forEach` it won’t work and that’s because `forEach` isn’t compatible with `async/await` , but hey, try it for yourself and enjoy.

If you wanted to do this in parallel you might try something like this:

```
const getTextData = async () => Promise.all(
  ["a", "b", "c"].map(async (name) => {
    const resp = await fetch(`/data_${name}.json`);
    return await resp.json();
  })
);
```

This will also work, but I don’t think the order of results is guaranteed, it will depend on how fast the individual fetches resolve.

I hear you screaming: “Enough with fetching! Show me something new!” Fine, fine!

# Keeping Track Of Time

Let’s make a stopwatch using SWR. No, I’m not kidding!

We’ll start by create a functor (a function that makes functions) and this functor will make use a function that knows the time at which it was created. And then when we call it, it will return the delta between that start time and the current time, in seconds.

```
const createStopwatch = () => {
  const startTime = Date.now();
  return () => {
    return Math.round((Date.now() - startTime) / 1000);
  };
};
```

Now when we call `createStopwatch` we will get a function back that knows its start time and will give us the elapsed time since then. And we can use that in a component with that uses the `useSWR` hook, like so:

```
import useSWRfrom "swr";const Stopwatch = () => {
  const stopwatchRef = useRef(createStopwatch());
  const { data } = useSWR("stopwatch", stopwatchRef.current, {
    refreshInterval: 100,
    dedupingInterval: 100,
  });  return <div>Time: {data}</div>;
};
```

We start by creating a ref to hold the function, which, because we use `useRef` will only get called once on mount. Then we use that function (by getting it from `stopwatchRef.current`) in the `useSWR` hook, which calls that function every 100 milliseconds because of the `refreshInterval`.

That’s it! Boom! A stopwatch! We are using the refresh interval built into SWR to, instead of fetching data every hundred milliseconds, to instead call this synchronous function.

Now this is cool and all, but not really practical, let’s try something related but more practical.

# Monitor Those Logs!

Let’s say you want part of the UI to monitor a log. And the log updates a **lot**, like easily every 100 milliseconds. But you don’t want to update the UI that often because, let’s face it, the log isn’t that important. So can we use react-query (or SWR) to throttle the update speed? Sure we can!

First, let’s simulate a log:

```
const subscribeToLog = () => {
  let log = [];
  let logIndex = 0;  setInterval(() => {
    log.push(`${logIndex}: ${Date.now()}`);
    log = log.slice(-3);
    logIndex++;
  }, 100);  return () => log;
};
```

Now we have a `logListener` global that is a function that returns the log messages that are continuously being built by the interval function. Every 100 milliseconds that interval adds a new log message and then trims the log down to the most recent three events (just to keep the display size small.)

Now we use react-query to fetch the log, but only once every second:

```
const Logger = () => {
  const { data } = useQuery("log", logListener, {
    refetchInterval: 1000,
  });  return (
    <div>
      {data?.map((line, index) => (
        <div key={line}>{line}</div>
      ))}
    </div>
  );
};
```

In this case we are using the `useQuery` hook to poll the `logListener` (which returns the last three items in the log) every 1000 milliseconds (1 second). And that throttles the display so that we don’t update it too often.

Of course, the swr code is dramatically different. You have to change `refetchInterval` to `refreshInterval` and add that `dedupingInterval` . It’s crazy, I know, the differences are staggering.

Ok, so that really was a different use for something like react-query or swr, but what else have I got? How about getting GPS coordinates!

Five Clever Hacks for React-Query and SWR image

# Going Home With GPS

Anything you can wrap in a promise you can monitor with these awesome libraries. Take getting your GPS coordinates for example. Here we wrap the browsers built-in `getCurrentPosition` in a promise:

```
const getGPSCoordinates = async () =>
  new Promise((resolve, reject) => {
    navigator.geolocation.getCurrentPosition(
      (position) => {
        resolve({
          latitude: position.coords.latitude,
          longitude: position.coords.longitude,
        });
      },
      (error) => {
        reject(error);
      }
    );
  });
```

And then we can call it, with… lemme just pick one… swr this time:

```
const GPS = () => {
  const { data } = useSWR("gps", getGPSCoordinates);
  return <div>Location: {JSON.stringify(data)}</div>;
};
```

And there you go, GPS coordinates in your component.

The key point here is that; anything you can turn into a synchronous function, or a promise based async function, is going to work with these libraries. **Anything**. At all.

# Parallelize With Web Workers

Which brings me to [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers), which are really handy bits of code that you can run in a different thread on the page. Take a simple one like this:

```
export const multiplyNumbers = (a, b) => {
  postMessage({ type: "result", result: a * b });
};
```

This little guy can multiple two numbers and send back the result. Such a good little function! Anyway, we can integrate it into our code super simple using react-query (or swr). We first need to load it:

```
const workerInstance = worker();
```

Now we have an instance of the worker that we have loaded using the `workerize-loader` Webpack loader. We can then wrap that in a promise based function which calls it, waits for the result, and then resolves the promise with the output.

```
const multiplyNumbers = async (args) =>
  new Promise((resolve) => {
    workerInstance.addEventListener("message", (message) => {
      if (message.data.type === "result") {
        resolve(message.data.result);
      }
    });    workerInstance.multiplyNumbers(args.a, args.b);
  });
```

All we do is create a promise, register a listener on the instance, then make the call. Once the listener fires we have our result. And here is the component code that uses this function, this time using react-query.

```
const WebWorker = () => {
  const { data: result, mutate } = useMutation(
    "multiply", multiplyNumbers);  const [valueA, setValueA] = useState("10");
  const [valueB, setValueB] = useState("20");  return (
    <div>
      <input
        value={valueA}
        onChange={(evt) => setValueA(evt.target.value)}
      />
      <input
        value={valueB}
        onChange={(evt) => setValueB(evt.target.value)}
      />
      <button onClick={
        () => mutate({ a: valueA, b: valueB })
      }>Multiply</button>
      <div>{result}</div>
    </div>
  );
};
```

In this case I’m using the `useMutation` hook from react-query because it makes a little more sense in that it’s actively executing something. And that’s kind of important as you look to perhaps use some of these patterns; make sure your queries are modeled as `useQuery` and that actions that potentially change things use the `useMutation` hook.

Of course that doesn’t help you with swr, that doesn’t have a mutation hook, but there is still a way to do this with swr as well.

Now, let’s finish this off in grand style, by answering the age old question; if you have react-query or swr, do you need a state manager?

# Built-In State Manager?!?

Both swr and react-query manage caches, right? They can both make sure that if you access the same query key from two different spots you’ll get the same data.

Which means that you can use that cache to store bits of data you want, globally, and when you update them, they will update everywhere they are “subscribed”. Which is like … 80%? of what a state manager does?

So we can create a custom hook called `useSWRGlobalState` that does exactly this global shared stuff, check it out.

```
const useSWRGlobalState = (key, initialData) => {
  const { data, mutate } = useSWR(key, () => initialData);
  return [
    data ?? initialData,
    (value) =>
      mutate(value, {
        revalidate: false,
      }),
  ];
};
```

You give this hook a `key` , which is the query key we’ve been using all of over the place, and whatever you want for the initial data. And it in turn uses `useSWR` to get the current data as well as the `mutate` function.

The hook then returns an array that looks like the return from `useState` . It is an array where the first item is the current value, and the second is a setter function.

The setter function is where the magic happens. We call that `mutate` function we got back and give it the new value **but** we tell swr **not** to re-fetch the value. Which basically means; set the cache, but that’s all.

Now we can wrap this in some components!

```
const StateEditor = () => {
  const [value, setValue] = useSWRGlobalState("sharedText", "");  return (
    <input value={value}
       onChange={(evt) => setValue(evt.target.value)} />
  );
};const StateViewer = () => {
  const [value] = useSWRGlobalState("sharedText", "");  return <div>{value}</div>;
};const GlobalStateDemo = () => {
  return (
    <div>
      <StateEditor />
      <StateViewer />
    </div>
  );
};
```

Here we have two separate components, one that edits the state, that’s the `StateEditor` component, and one that views the shared state, that being the `StateViewer`. When you type into the `StateEditor` the change shows up immediately in `StateViewer` .

No kidding, really. No context. No Redux. No atoms. Just that one little hook, and the “fetch library” you already have.💥 Crazy, right?

Now, would I use this for realsies? In a big application that maybe already has a state manager, then for sure not. But if all I needed to share around my component hierarchy was a single piece of state, like maybe the user ID, and a JWT, then yeah, I might just do this.

BTW, this is possible with React-Query as well.

```
const useRQGlobalState = (key, initialData) => [
  useQuery(key, () => initialData, {
    enabled: false,
    initialData,
  }).data ?? initialData,
  (value) => client.setQueryData(key, value),
];
```

This hook returns an array, just like before, where the first item in the array is the current value, that we get with `useQuery` and then second value is a setter function that sets the cache data for the query directly on the react-query client.

# Wrapping It Up

I hope you’ve had a fun ride looking at a bunch of different ways you can wring more value out of the kilobytes you are adding to your app code by bringing in these awesome libraries. They really are an invaluable addition to the React ecosystem.