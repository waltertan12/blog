---
layout: post
title: "How to Frontend: Part 2"
date: 2018-05-02 12:00:00 -0700
categories: javascript
---

This series of posts focuses around how modern frontend view libraries (ahem... React) work.

## DOM Virtualization
From the last [post](/blog/javascript/2018/05/01/how-to-frontend-part-1.html), we've seen that's a fairly cumbersome to build and manipulate the DOM with JavaScript. With this virtualization approach, we're able

### What's in a Virtual DOM Node?
Let's start by defining what makes a DOM node a DOM node.

First off, let's separate out Element nodes from Text nodes. 
#### Text Nodes
Text nodes are _basically_ text and can be represented as strings.

#### Element Nodes
1. Elements have a tag name 
2. Elements have associated properties, like className, src, or href
3. Elements are hierarchical and have child nodes

A good representation of a node might look like this:
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

### Writing the Virutal DOM Declaratively
Now that we have a good data structure defined, we can create a factory method to help us build up the DOM.

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

Let's create the Todo app from the last post using the factory method:
```javascript
/*
 * <div class="container">
 *   <h1>Todo App</h1>
 *   <div class="form-group">
 *     <input id="input" class="form-control" type="text" placeholder="Do laundry" />
 *     <br />
 *     <button id="button" class="btn btn-primary">Add todo</button>
 *   </div>
 *
 *   <h2>Things to do: </h2>
 *   <ul id="list" class="list">
 *   </ul>
 * </div>
 */

createVNode('div', { className: 'container' }, 
    createVNode('h1', {}, 'Todo App'),
    createVNode('div', { className: 'form-group' },
        createVNode('input', { id: 'input', className: 'form-control' }),
        createVNode('br', {}),
        createVNode('button', { id: 'button', className: 'btn btn-primary' },
            'Add todo'
        )
    ),
    createVNode('h2', {}),
    createVNode('ul', { id: 'list', className: 'list' })
);
```

Check out this CodePen example to see the JSON object it creates:

<p data-height="450" data-theme-id="0" data-slug-hash="Zoypem" data-default-tab="js,result" data-user="waltertan12" data-embed-version="2" data-pen-title="Virtual DOM Example 1" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/Zoypem/">Virtual DOM Example 1</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>


### Rendering the Virtual DOM
```javascript
/**
 * For a full list of Element props, look at the docs
 *   - https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement
 *   - https://developer.mozilla.org/en-US/docs/Web/API/Element
 *   - https://developer.mozilla.org/en-US/docs/Web/API/Node
 *
 * @param {Element} node
 * @param {Object}
 */
const applyProps = (node, props) => {
    Object.keys(props)
        .forEach(propName => {
            const prop = props[propName];
            // This is for something like the style attribute
            if (typeof prop === 'object') {
                Object.keys(prop)
                    .forEach(attribute => {
                        node[propName][attribute] = prop[attribute];
                    });

            // The prop is normal
            } else if (typeof prop === 'string' || typeof prop === 'number') {
                node[propName] = prop;
            }
        });
};

const render = (vNode) => {
    if (typeof vNode === 'string') {
        return document.createTextNode(vNode);
    }

    const node = document.createElement(vNode.tagName);
    applyProps(node, vNode.props);
    vNode
        .children
        .map(render)
        .forEach(node.appendChild.bind(node));

    return node;
}
```

<p data-height="450" data-theme-id="0" data-slug-hash="qYjazN" data-default-tab="js,result" data-user="waltertan12" data-embed-version="2" data-pen-title="Virtual DOM Example 2" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/qYjazN/">Virtual DOM Example 2</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
