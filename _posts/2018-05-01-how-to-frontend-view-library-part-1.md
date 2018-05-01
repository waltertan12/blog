---
layout: post
title: "How to Modern Frontend View Library: Part 1"
date: 2018-05-01 12:00:00 -0700
categories: javascript
markdown: redcarpet
---

# How to Modern Frontend View Library
## Part 1: The DOM
This series of posts focuses around how modern frontend view libraries (ahem... React) work.

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

The HTML tags (div, head, body, etc) get turned into an [Element](https://developer.mozilla.org/en-US/docs/Web/API/Element) and the text ("Hello", "Title", etc) turns into a [Text](https://developer.mozilla.org/en-US/docs/Web/API/Text).

The final result of this is a something that looks like this:

<p data-height="281" data-theme-id="dark" data-slug-hash="zjwGdw" data-default-tab="result" data-user="waltertan12" data-embed-version="2" data-pen-title="zjwGdw" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/zjwGdw/">zjwGdw</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script src="https://static.codepen.io/assets/embed/ei.js"></script>

If you want to play around with this concept more, you can open up the Inspector tool in your browser and explore existing web pages:
- [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Open_the_Inspector)
- [Chrome](https://developer.chrome.com/devtools#dom-and-styles)

## Building the DOM with JavaScript
So, we've discovered that the browser can render a static HTML file. Hooray!

However, what happens if we want to dynmically add another Element to the page? We use JavaScript!

Fortunately, the DOM provides a nice API to dyamically build up the DOM.

Here are the three key methods that help us build the DOM:
- [`Document#createElement`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElement)
  - This, unsurprisngly, creates Elements
- [`Document#createTextNode`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createTextNode)
  - This creates Text nodes
- [`Node#appendChild`](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild)
  - This let's us add elements / text nodes as children
    - tl;dr it let's us form associations with the nodes
  - This is super important when building a DOM tree

So, if we wanted to create the example from above purely with JavaScript, we could do it like this:
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

<p data-height="281" data-theme-id="dark" data-slug-hash="VxbLGE" data-default-tab="result" data-user="waltertan12" data-embed-version="2" data-pen-title="Dynamic DOM Example" data-editable="true" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/VxbLGE/">Dynamic DOM Example</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script src="https://static.codepen.io/assets/embed/ei.js"></script>

## Take Aways
We've (sort of) learned what the DOM is and how we are able to dynamically build the DOM using JavaScript. However, given the last example, building the DOM with JavaScript isn't exactly easy. The next post will introduce virtualization of the DOM and some better ways of dynamically building the DOM.

- Walter
