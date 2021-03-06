---
layout: post
title: "How to Frontend: The Virtual DOM"
date: 2018-05-02 12:00:00 -0700
tags: ["javascript", "virtual-dom"]
---

This series of posts focuses around how modern frontend view libraries work.

## DOM Virtualization
From the last [post](/blog/javascript/2018/05/01/how-to-frontend-part-1.html), we've seen that's a fairly cumbersome to build and manipulate the DOM with JavaScript. With this virtualization approach, we're get the ability to do the following:
  - Declaratively build the virtual DOM
  - Build up the UI with all the niceties of JavaScript
    - You have all of JavaScript at your disposal, not just templating markup
  - Render applications functionally (see the next post)

Let's start by defining what makes a DOM node a DOM node.

## Defining a Virtual DOM Node

In the last post, we learned that there are two different types of nodes: Element nodes and text.

Let's do the same for our virtual DOM. 

**Element Nodes**
- Elements have a tag name 
- Elements have associated properties, like className, src, or href
  - Here's documentation on some props nodes can have:
    - [HTMLElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement)
    - [Element](https://developer.mozilla.org/en-US/docs/Web/API/Element)
    - [Node](https://developer.mozilla.org/en-US/docs/Web/API/Node)
- Elements are hierarchical and have child nodes

**Text Nodes**
- Text nodes are basically text and can be represented as strings.

Putting it all together, we can represent a virtual node like this:
```javascript
{
    tagName: 'div',
    props: {
        className: 'some-class',
    },
    children: [
        'Hello, world!', // <-- check out that virtual text node
    ]
}
```

## VNode Factory
Now that we have a good data structure defined, we can create a factory method to help us build up the virtual DOM.

The function accepts a tag name, a properties object, and, using [rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters), a list of zero or more child nodes / text. 

```javascript
/**
 * @param  {string}           tagName
 * @param  {object}           props
 * @param  {...object|string} children
 * @return {object}
 */
const createVNode = (tagName, props, ...children) => {
    return { tagName, props, children };
};
```
You'll see why this last rest argument is especially helpful by creating a virtual Todo app.

Here's the HTML we want to emulate.
```html
<div class="container">
  <h1>Todo App</h1>
  <div class="form-group">
    <input
      id="input"
      class="form-control"
      type="text"
      placeholder="Do laundry" 
    />
    <br />
    <button id="button" class="btn btn-primary">Add todo</button>
  </div>

  <h2>Things to do: </h2>
  <ul id="list" class="list-group">
  </ul>
</div>
```

### An Imperative Approach
First, let's build it like we would if we were using `Document#createElement`
```javascript
// Create the nodes
const containerDiv = createVNode('div', { className: 'container' });

const h1 = createVNode('h1', {}, 'Todo App');

const formDiv = createVNode('div', { className: 'form-group' });
const input = createVNode('input', { id: 'input', className: 'form-control', type: 'text', placeholder: 'Do laundry' });
const br = createVNode('br', {});
const button = createVNode('button', { id: 'button', className: 'btn btn-primary' });

const h2 = createVNode('h2', {}, 'Things to do:');
const ul = createVNode('ul', { id: 'list', className: 'list-group' });

// Build the assocations
formDiv.children = formDiv.children.concat([ input, br, button ]);
containerDiv.children = containerDiv.children.concat([ h1, formDiv, h2, ul ]);
```

Not too bad, but it's a bit cumbersome and error prone to always be calling `Array#concat` or `Array#push` and assigning relationships.

### A Declarative Approach
Now, let's take advantage of the rest parameters and build it more declaratively.
```javascript
createVNode('div', { className: 'container' }, 
    // We can add as many children as we want using the rest parameter
    createVNode('h1', {}, 'Todo App'),
    createVNode('div', { className: 'form-group' },
        createVNode('input', { id: 'input', className: 'form-control', type: 'text', placeholder: 'Do laundry' }),
        createVNode('br', {}),
        createVNode('button', { id: 'button', className: 'btn btn-primary' },
            'Add todo'
        )
    ),
    createVNode('h2', {}, 'Things to do:'),
    createVNode('ul', { id: 'list', className: 'list-group' })
);
```
Instead of needing to assign associations between the nodes, the factory approach let's us declare the associations by nesting the items.

A bit nicer to write, right?

<p data-height="450" data-theme-id="dark" data-slug-hash="Zoypem" data-default-tab="js,result" data-user="waltertan12" data-embed-version="2" data-pen-title="Virtual DOM Example 1" data-editable="true" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/Zoypem/">Virtual DOM Example 1</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Factory Method or JSX?
This factory approach is how React and other libraries ([HyperScript](https://github.com/hyperhype/hyperscript), [Mithril](https://github.com/MithrilJS/mithril.js), etc) build up their virtual DOM.

In React, the factory method is called `React#createElement`. However, if you're building a React application, chances are you won't be directly be making calls to it. 

That's because Facebook introduced an XML like syntax called [JSX](https://github.com/facebook/jsx). Instead of needing to write `React#createElement`, you can write something that looks more similar to HTML.

```jsx
const TodoApp = (
  <div className="container">
    <h1>Todo App</h1>
    <div className="form-group">
      <input
        id="input"
        className="form-control"
        type="text"
        placeholder="Do laundry" 
      />
      <br />
      <button id="button" className="btn btn-primary">Add todo</button>
    </div>
  
    <h2>Things to do: </h2>
    <ul id="list" className="list-group">
    </ul>
  </div>
);
```

That said, JSX isn't valid JavaScript, so apps need to transform the JSX into `React#createElement` calls using JSX parsers, like [acorn-jsx](https://github.com/RReverser/acorn-jsx) or [Babylon](https://github.com/babel/babel/tree/master/packages/babylon).

```javascript
// The JSX in the previous code block gets turned into this
const TodoApp = React.createElement('div', { className: 'container' }, 
    // We can add as many children as we want using the rest parameter
    React.createElement('h1', null, 'Todo App'),
    React.createElement('div', { className: 'form-group' },
        React.createElement('input', { id: 'input', className: 'form-control', type: 'text', placeholder: 'Do laundry' }),
        React.createElement('br', null),
        React.createElement('button', { id: 'button', className: 'btn btn-primary' },
            'Add todo'
        )
    ),
    React.createElement('h2', null, 'Things to do:'),
    React.createElement('ul', { id: 'list', className: 'list-group' })
);
```

## Take Aways
- Factory methods help us write the virutal DOM declaratively
- JSX is syntatic sugar over virtual node factories

The next step is to take this virtual tree and actually render it to the browser.

Walter
