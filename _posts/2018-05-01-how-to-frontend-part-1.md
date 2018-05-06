---
layout: post
title: "How to Frontend: Part 1"
date: 2018-05-01 12:00:00 -0700
categories: javascript
---

This series of posts focuses around how modern frontend view libraries work.

## DOM Basics
To get an understanding of what's going on with a library like React, you'll need to be familiar with the DOM.

### What is the DOM
The DOM is basically a data structure the browser creates from HTML

For example, given this HTML: 
```html
<body>
  <h1>Hello</h1>
  <div>
    <h2>Title</h2>
    <p>Some supporting text</p>
  </div>
</body>
```

The browser will create the following DOM tree
```
       body
      /   \
    h1     div
   /      /  \ 
 text    h2   p
        /      \
       text    text
```

The HTML tags (div, head, body, etc) get turned into an [Element](https://developer.mozilla.org/en-US/docs/Web/API/Element) and the text ("Hello", "Title", etc) turns into a [Text](https://developer.mozilla.org/en-US/docs/Web/API/Text) node.

The final result of this is a something that looks like this:

<p data-height="450" data-theme-id="dark" data-slug-hash="zjwGdw" data-default-tab="html,result" data-user="waltertan12" data-embed-version="2" data-pen-title="zjwGdw" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/zjwGdw/">zjwGdw</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script src="https://static.codepen.io/assets/embed/ei.js"></script>

If you want to play around with this concept more, you can open up the Inspector tool in your browser and explore existing web pages:
- [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Open_the_Inspector)
- [Chrome](https://developer.chrome.com/devtools#dom-and-styles)

## Changing the DOM
So, we've discovered that the browser can render a static HTML file.

Hooray!

But let's say we have a Todo App and we want to add another todo item to the page. What do we do?

Things we can try:
1. We could send the todo item to the server and have it send us a new static HTML page
2. We could use Javascript to help us dynamically add a new DOM element

Option 1 works but it doesn't provide an ideal user experience since we need to hard reload the page for every single change. A user could be adding dozens of items to the list -- waiting for a new page to load for each added item means the user spends a lot of time waiting around.

That said, let's explore option 2.

### Building the DOM with JavaScript
Fortunately, the DOM provides a nice API to dyamically build up the DOM.

Here are the three key methods we're going to use:
- [`Document#createElement`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElement)
  - This creates Elements
- [`Document#createTextNode`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createTextNode)
  - This creates Text nodes
- [`Node#appendChild`](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild)
  - This let's us add elements / text nodes as children
  - This is super important when building a DOM tree
  - **tl;dr** it let's us form associations between nodes

So, if we wanted to create the example from above purely with JavaScript, we could do it like this, though it's pretty verbose and not declarative:
```javascript
// Create the Elements
const body = document.createElement('body');
const div = document.createElement('div');
const head = document.createElement('head');
const h1 = document.createElement('h1');
const h2 = document.createElement('h2');
const p = document.createElement('p');

// Create the Text nodes
const hello = document.createTextNode('Hello');
const title = document.createTextNode('Title');
const supportingText = document.createTextNode('Some supporting text');

// Tie them all together (bottom up)
h1.appendChild(hello);
h2.appendChild(title);
p.appendChild(supportingText);

div.appendChild(h2);
div.appendChild(p);

body.appendChild(h1);
body.appendChild(div);
```

If you run that code in the console of your browser, you'll be able to see that we've successfully recreated the same DOM tree.

<p data-height="450" data-theme-id="dark" data-slug-hash="VxbLGE" data-default-tab="js,result" data-user="waltertan12" data-embed-version="2" data-pen-title="Dynamic DOM Example" data-editable="true" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/VxbLGE/">Dynamic DOM Example</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script src="https://static.codepen.io/assets/embed/ei.js"></script>

And now, back to the todo list, if we ever wanted to add an Element, we can simply call upon the Document and Node API to dynamically add elements.

Checkout the following example:

<p data-height="450" data-theme-id="dark" data-slug-hash="PemPWP" data-default-tab="js,result" data-user="waltertan12" data-embed-version="2" data-pen-title="Dynamic DOM Example 2" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/PemPWP/">Dynamic DOM Example 2</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script src="https://static.codepen.io/assets/embed/ei.js"></script>

## Take Aways
- We've (sort of) learned what the DOM is
- We know how to dynamically build up the DOM using JavaScript
  - This can provide a better user experience than a hard-reload another HTML document.
  - However, building the DOM with JavaScript is a little bit verbose.

The next post will introduce virtualization of the DOM and some better ways of dynamically building the DOM.

Walter
