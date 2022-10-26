# styled-components

Created: May 7, 2022 11:08 AM
Finished: No
Source: https://styled-components.com/
Tags: #tool

## Use the best bits of ES6 and CSS to style your apps without stress 💅🏾

<div>const Button = styled.a` /* This renders the buttons above... Edit me! */ display: inline-block; border-radius: 3px; padding: 0.5rem 0; margin: 0.5rem 1rem; width: 11rem; background: transparent; color: white; border: 2px solid white; /* The GitHub button is a primary button * edit this to target it specifically! */ ${props => props.primary && css` background: white; color: black; `} ` render( <div> <Button href="https://github.com/styled-components/styled-components" target="_blank" rel="noopener" primary > GitHub </Button> <Button as={Link} href="/docs"> Documentation </Button> </div> )</div>

```
const Button = styled.a`${props => props.primary && css`    background: white;    color: black;<div>href="https://github.com/styled-components/styled-components"target="_blank"rel="noopener"<Button as={Link} href="/docs"></div>
```

Used by folks at

![styled-components%2087333adfb7df48438f6036fd3d7b802d/imdb.com.jpg](styled-components%2087333adfb7df48438f6036fd3d7b802d/imdb.com.jpg)

![styled-components%2087333adfb7df48438f6036fd3d7b802d/gizmodo.com.jpg](styled-components%2087333adfb7df48438f6036fd3d7b802d/gizmodo.com.jpg)

# Getting started

To download styled-components run:

That's all you need to do, you are now ready to use it in your app! (yep, no build step needed 👌)

It's recommended (but not required) to also use the [styled-components Babel plugin](https://github.com/styled-components/babel-plugin-styled-components) if you can. It offers many benefits like more legible class names, server-side rendering compatibility, smaller bundles, and more.

Let's say you want to create a simple and reusable <Button /> component that you can use throughout your application. There should be a normal version and a big and primary version for the important buttons. This is what it should look like when rendered: (this is a live example, click on them!)

First, let's import styled-components and create a styled.button:

This Button variable here is now a React component that you can use like any other React component! This unusual backtick syntax is a new JavaScript feature called a [tagged template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates).

You know how you can call functions with parenthesis? (myFunc()) Well, now you can also call functions with backticks! ([learn more about tagged template literals](https://styled-components.com/docs/advanced#tagged-template-literals))

If you render our lovely component now (just like any other component: <Button />) this is what you get:

It renders a button! That's not a very nice button though 😕 we can do better than this, let's give it a bit of styling and tickle out the hidden beauty within!

```
const Button = styled.button``
```

As you can see, styled-components lets you write actual CSS in your JavaScript. This means you can use all the features of CSS you use and love, including (but by far not limited to) media queries, all pseudo-selectors, nesting, etc.

The last step is that we need to define what a primary button looks like. To do that we also import { css } from styled-components and interpolate a function into our template literal, which gets passed the props of our component:

```
import styled, { css } from 'styled-components'const Button = styled.button`${props =>    props.primary &&      background: palevioletred;      color: white;    `};`
```

Here we're saying that when the primary property is set we want to add some more css to our component, in this case change the background and color.

That's all, we're done! Take a look at our finished component:

```
<div>const Button = styled.button`
  background: transparent;
  border-radius: 3px;
  border: 2px solid palevioletred;
  color: palevioletred;
  margin: 0.5em 1em;
  padding: 0.25em 1em;

  ${props => props.primary && css`
    background: palevioletred;
    color: white;
  `}
`;

const Container = styled.div`
  text-align: center;
`

render(
  <Container>
    <Button>Normal Button</Button>
    <Button primary>Primary Button</Button>
  </Container>
);</div>const Button = styled.button`  color: palevioletred;${props => props.primary && css`    background: palevioletred;    color: white;  `}`;const Container = styled.div`  text-align: center;`<Container><Button>Normal Button</Button><Button primary>Primary Button</Button></Container>);
```

Nice 😍 That's a live updating editor too, so play around with it a bit to get a feel for what it's like to work with styled-components! Once you're ready, dive into the documentation to learn about all the cool things styled-components can do for you: