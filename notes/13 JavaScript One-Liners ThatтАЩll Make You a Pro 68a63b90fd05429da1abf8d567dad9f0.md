# 13 JavaScript One-Liners That’ll Make You a Pro

Created: July 15, 2022 12:41 PM
Finished: No
Source: https://medium.com/dailyjs/13-javascript-one-liners-thatll-make-you-look-like-a-pro-29a27b6f51cb

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/10wsJJ6XGSgHjmUePUZqkOw.jpeg](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/10wsJJ6XGSgHjmUePUZqkOw.jpeg)

Photo by [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/yellow-one?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

JavaScript can do a lot of amazing things!

From complex frameworks to handling API’s, there is just SO much to learn.

But, it also enables you to do some awesome stuff using just one single line.

Check out these **13 JavaScript One-Liners That’ll Make You Look Like a Pro**!

# 1. Get a random boolean (true/false)

This function will return a boolean (true or false) using the `Math.random()` method. `Math.random` will create a random number between 0 and 1, after which we check if it is higher or lower than 0.5. That means it’s a 50%/50% chance to get either true or false.

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1ffWwEvDEUF322b8xatWE0Q.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1ffWwEvDEUF322b8xatWE0Q.png)

```
console.log(randomBoolean());
// Result: a 50/50 change on returning true of false
```

# 2. Check if the provided day is a weekday

Using this method, you’ll be able to check if the date that you provide in the function is either a weekday or weekend day.

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/17sBqa6rF8i_afEdPqLO1lQ.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/17sBqa6rF8i_afEdPqLO1lQ.png)

```
console.log(isWeekday(new Date(2021, 0, 11)));
// Result: true (Monday)console.log(isWeekday(new Date(2021, 0, 10)));
// Result: false (Sunday)
```

# 3. Reverse a string

There are a couple different ways to reverse a string. This is one of the most simple ones using the `split()`, `reverse()`, and `join()` methods.

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1WkFSwPRaezcrzN5B4xkgPA.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1WkFSwPRaezcrzN5B4xkgPA.png)

```
reverse('hello world');
// Result: 'dlrow olleh'
```

# 4. Check if the current tab is in view / focus

We can check if the current tab is in view / focus by using the `document.hidden` property.

```
isBrowserTabInView();
// Result: returns true or false depending on if tab is in view / focus
```

# 5. Check if a number is even or odd

A super simple task that can be solved by using the modulo operator (`%`). If you’re not too familiar with it, [here’s a nice visual explanation on Stack Overflow](https://stackoverflow.com/questions/17524673/understanding-the-modulus-operator/17525046#17525046).

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1Cz8wYXcBy5y33kxIO7ZBdQ.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1Cz8wYXcBy5y33kxIO7ZBdQ.png)

```
console.log(isEven(2));
// Result: trueconsole.log(isEven(3));
// Result: false
```

# 6. Get the time from a date

By using the `.toTimeString()` method and slicing the string at the correct place, we can get the time from a date that we provide, or get the current time.

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1M0cPlNN8PAOpC7yuPuVMUA.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1M0cPlNN8PAOpC7yuPuVMUA.png)

```
console.log(timeFromDate(new Date(2021, 0, 10, 17, 30, 0)));
// Result: "17:30:00"console.log(timeFromDate(new Date()));
// Result: will log the current time
```

# 7. Truncate a number to a fixed decimal point

Using the `Math.pow()` method, we can truncate a number to a certain decimal point that we provide in the function.

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/12hbZdavmY45rDPJ68aYgoQ.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/12hbZdavmY45rDPJ68aYgoQ.png)

```
// Examples
toFixed(25.198726354, 1);       // 25.1
toFixed(25.198726354, 2);       // 25.19
toFixed(25.198726354, 3);       // 25.198
toFixed(25.198726354, 4);       // 25.1987
toFixed(25.198726354, 5);       // 25.19872
toFixed(25.198726354, 6);       // 25.198726
```

# 8. Check if an element is currently in focus

We can check if an element is currently in focus using the `document.activeElement` property.

```
elementIsInFocus(anyElement)
// Result: will return true if in focus, false if not in focus
```

# 9. Check if the current user has touch events supported

```
const touchSupported = () => {
  ('ontouchstart' in window || window.DocumentTouch && document instanceof window.DocumentTouch);
}console.log(touchSupported());
// Result: will return true if touch events are supported, false if not
```

# 10. Check if the current user is on an Apple device

We can use `navigator.platform` to check if the current user is on an Apple device.

```
console.log(isAppleDevice);
// Result: will return true if user is on an Apple device
```

# 11. Scroll to top of the page

The `window.scrollTo()` method will take an x- and y-coordinate to scroll to. If we set these to zero and zero, we’ll scroll to the top of the page.

**Note: the `.scrollTo()` method isn’t supported on Internet Explorer.**

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1smPAl5sWqPO2ysTOk85xhQ.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1smPAl5sWqPO2ysTOk85xhQ.png)

```
goToTop();
// Result: will scroll the browser to the top of the page
```

# 12. Get average value of arguments

We can use the reduce method to get the average value of the arguments that we provide in this function.

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1tHQfAA6tyFe6xUIYFem9qg.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1tHQfAA6tyFe6xUIYFem9qg.png)

```
average(1, 2, 3, 4);
// Result: 2.5
```

# 13. Convert Fahrenheit / Celsius

***The final one is a 2-in-1!***

Dealing with temperatures can be confusing at times. These 2 functions will help you convert Fahrenheit to Celsius and the other way around.

![13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1dv2gzPM8sIW6tQt5AiM_Yw.png](13%20JavaScript%20One-Liners%20That%E2%80%99ll%20Make%20You%20a%20Pro%2068a63b90fd05429da1abf8d567dad9f0/1dv2gzPM8sIW6tQt5AiM_Yw.png)

```
// Examples
celsiusToFahrenheit(15);    // 59
celsiusToFahrenheit(0);     // 32
celsiusToFahrenheit(-20);   // -4fahrenheitToCelsius(59);    // 15
fahrenheitToCelsius(32);    // 0
```

Thanks for reading! I hope you learned something new today.

Want to get the most out of your website? Check out my e-book “**[Your Website Sucks](https://flurly.com/p/your-website-sucks)**” and download a chapter for free!

Want to improve your JS skills even more? Check out my other article “**[The 7 JS Array Methods you will need in 2021](https://medium.com/dailyjs/the-7-js-array-methods-you-will-need-in-2021-a9faa83b50e8)**” or “**[What’s the difference between Event Handlers & addEventListener in JS?](https://medium.com/dailyjs/whats-the-difference-between-event-handlers-addeventlistener-in-js-963431f05c34)**” to get even better at JS.

Have a nice day! 😄