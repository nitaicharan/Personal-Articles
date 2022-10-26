# Node.js Coding Style Guidelines

Created: April 24, 2022 9:25 PM
Finished: No
Source: https://medium.com/swlh/node-js-coding-style-guidelines-74a20d00c40b
Tags: #nodejs

S**oftware developers** are like lone wolves who prefer to work as individuals rather than in a group. I too come in that category. This can sometimes create problems when the need to work in groups arises in a project.

There are both *pros* and *cons* involved when working in groups. On the plus side, there are *more insights* available to a problem which can help get to a better solution, but at the same time there can be collaboration issues when one developer has to go through another developer’s code to debug or review it.

Each individual has his/her own way of writing code and more often than not, there is a high degree of variation among a group of developers. To avoid such problems when working in a team, most of the programming language communities follow standard coding guidelines. By following these guidelines every developer can write code in a specific way that is known to their teammates. This practice can help teams save time reading the code written by other members.

This article is a guide for writing consistent and aesthetically pleasing **Node.js** code. It is inspired by what is popular within the community, and also features some personal opinions.

## Formatting

- **4 spaces for indentation**

Use 4 spaces for indenting your code and swear an oath to never mix tabs and spaces — a special kind of hell awaits otherwise.

- **Newlines**

Use UNIX-style newlines (\n), and a newline character as the last character of a file. Windows-style newlines (\r\n) are forbidden inside any repository.

- **No trailing white space**

Just like you brush your teeth after every meal, you clean up any trailing white space in your `.js` files before committing. Otherwise the rotten smell of careless neglect will eventually drive away contributors and/or co-workers.

- **Use semicolons**

According to [scientific research](http://news.ycombinator.com/item?id=1547647), the usage of semicolons is a core value of our community. Consider the points of [the opposition](http://blog.izs.me/post/2353458699/an-open-letter-to-javascript-leaders-regarding), but be a traditionalist when it comes to abusing error correction mechanisms for cheap syntactic pleasures.

- **80 characters per line**

Limit your lines to 80 characters. Yes, screens have gotten much bigger over the last few years, but your brain has not. Use the additional room for split screen, your editor supports that, right?

- **Use single quotes**

Use single quotes, unless you are writing JSON. This helps you separate your objects’ strings from normal strings.

*Right:*

```
var foo = ‘bar’;
```

*Wrong:*

```
var foo = “bar”;
```

- **Opening braces go on the same line**

Your opening braces go on the same line as the statement.

*Right:*

```
if (true) {
    console.log(‘winning’);
}
```

*Wrong:*

```
if (true)
{
    console.log(‘losing’);
}
```

Also, notice the use of white space before and after the condition statement. What if you want to write ‘else’ or ‘else if’ along with your ‘if’…

*Right:*

```
if (true) {
    console.log(‘winning’);
} else if (false) {
    console.log(‘this is good’);
} else {
    console.log(‘finally’);
}
```

*Wrong:*

```
if (true)
{
    console.log(‘losing’);
}
else if (false)
{
    console.log(‘this is bad’);
}
else
{
    console.log(‘not good’);
}
```

- **Declare one variable per var statement**

Declare one variable per var statement, it makes it easier to re-order the lines.

*Right:*

*Wrong:*

## Naming Conventions

Variables, properties and function names should use lowerCamelCase. They should also be descriptive. Single character variables and uncommon abbreviations should generally be avoided.

*Right:*

```
var adminUser = db.query(‘SELECT * FROM users …’);
```

*Wrong:*

```
var admin_user = db.query(‘SELECT * FROM users …’);
```

Class names should be capitalised using UpperCamelCase.

*Right:*

*Wrong:*

- **Use UPPERCASE for Constants**

Constants should be declared as regular variables or static class properties, using all uppercase letters.

*Right:*

*Wrong:*

## Variables

- **Object / Array creation**

Use trailing commas and put *short* declarations on a single line. Only quote keys when your interpreter complains:

*Right:*

```
var b = {
  good: 'code',
  'is generally': 'pretty',
};
```

*Wrong:*

```
var b = {"good": 'code'
        , is generally: 'pretty'
        };
```

## Conditionals

- **Use the === operator**

Programming is not about remembering [stupid rules](https://developer.mozilla.org/en/JavaScript/Reference/Operators/Comparison_Operators). Use the triple equality operator and it will work just as expected.

*Right:*

```
if (a !== '') {
  console.log('winning');
}
```

*Wrong:*

```
if (a == '') {
  console.log('losing');
}
```

- **Use descriptive conditions**

Any non-trivial conditions should be assigned to a descriptively named variable or function:

*Right:*

```
if (isValidPassword) {
  console.log('winning');
}
```

*Wrong:*

```
if (password.length >= 4 && /^(?=.*\d).{4,}$/.test(password)) {
  console.log('losing');
}
```

## Functions

- **Write small functions**

Keep your functions short. A good function fits on a slide that the people in the last row of a big room can comfortably read.

- **Return early from functions**

To avoid deep nesting of if-statements, always return a function’s value as early as possible.

*Right:*

```
function isPercentage(val) {
  if (val < 0) {
    return false;
  }  if (val > 100) {
    return false;
  }
```

*Wrong:*

```
function isPercentage(val) {
  if (val >= 0) {
    if (val < 100) {
      return true;
    } else {
      return false;
    }
  } else {
    return false;
  }
}
```

Or for this particular example it may also be okay to shorten things even further:

```
function isPercentage(val) {
  var isInRange = (val >= 0 && val <= 100);
  return isInRange;
}
```

- **Method chaining**

One method per line should be used if you want to chain methods.

You should also indent these methods so it’s easier to tell they are part of the same chain.

*Right:*

*Wrong:*

```
User
.findOne({ name: 'foo' })
.populate('bar')
.exec(function(err, user) {
  return true;
});User.findOne({ name: 'foo' })
  .populate('bar')
  .exec(function(err, user) {
    return true;
  });User.findOne({ name: 'foo' }).populate('bar')
.exec(function(err, user) {
  return true;
});User.findOne({ name: 'foo' }).populate('bar')
  .exec(function(err, user) {
    return true;
  });
```

## Comments

- **Use slashes for comments**

Use slashes for both single line and multi line comments. Try to write comments that explain higher level mechanisms or clarify difficult segments of your code. Don’t use comments to restate trivial things.

*Right:*

```
// 'ID_SOMETHING=VALUE' -> ['ID_SOMETHING=VALUE',
// 'SOMETHING', 'VALUE']
var matches = item.match(/ID_([^\n]+)=([^\n]+)/));// This function has a nasty side effect where a failure to
// increment a redis counter used for statistics will
// cause an exception. This needs to be fixed in a later iteration.
function loadUser(id, cb) {
  // ...
}var isSessionValid = (session.expires < Date.now());
if (isSessionValid) {
  // ...
}
```

*Wrong:*

```
// Execute a regex
var matches = item.match(/ID_([^\n]+)=([^\n]+)/);// Usage: loadUser(5, function() { ... })
function loadUser(id, cb) {
  // ...
}// Check if the session is valid
var isSessionValid = (session.expires < Date.now());
// If the session is valid
if (isSessionValid) {
  // ...
}
```

## Miscellaneous

- **‘Requires’ at top**

Always put requires at top of file to clearly illustrate a file’s dependencies. Besides giving an overview for others at a quick glance of dependencies and possible memory impact, it allows one to determine if they need a package.json file should they choose to use the file elsewhere.

There is one more thing that can be done here which is to write your require statements in alphabetical order. This makes it easier to find whether some package has been imported or not by looking in a definite place instead of searching through all the require statements.

Having discussed these coding style guidelines I recommend all the developers out there follow these or some modified form of these guidelines to write their code, especially in circumstances where they are working in a team/group.

Following certain guidelines for writing code certainly saves time and enables a team to add new members and allows them to read and understand the code written by others by just going through the coding style guidelines of the team. This will speed up the new person’s on boarding into the team.

If you enjoy reading stories like these, then you should **[get my posts in your inbox](https://tarun-gupta.medium.com/subscribe)** and if want to support me as a writer, consider [signing up to become a Medium member](https://tarun-gupta.medium.com/membership). It’s $5 a month, giving you unlimited access to stories on Medium. If you sign up using my link, I’ll earn a small commission at no extra cost to you.

[Node%20js%20Coding%20Style%20Guidelines%200e62f60c68864efeb5864440c6f36329/0mINcqcGiD_7CIf6O](Node%20js%20Coding%20Style%20Guidelines%200e62f60c68864efeb5864440c6f36329/0mINcqcGiD_7CIf6O)

Thanks for reading! To read more such articles visit: