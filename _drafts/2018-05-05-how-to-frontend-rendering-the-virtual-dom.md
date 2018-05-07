---
layout: post
title: "How to Frontend: Rendering the Virtual DOM"
date: 2018-05-05 12:00:00 -0700
categories: javascript
---

This series of posts focuses around how modern frontend view libraries work.

## Rendering the Virtual DOM
Now that we've found a good way to build up the virtual DOM, it's time to render it to the real DOM!

### Rendering Virtual Nodes
Let's walk through what we need to do to take our virtual node and render it onto a browser.

Take a look at the code snippet:
```javascript
/**
 * Creates a DOM node from a virtual node
 *
 * @param  {Object|string} vNode A virtual node or virtual text node
 * @return {Node}          node  An actual DOM node
 */
const render = vNode => {
    // Create a text node if we're given a string
    if (typeof vNode === 'string') {
        return document.createTextNode(vNode);
    }

    // Create an element if we're given a vNode object
    const node = document.createElement(vNode.tagName);

    /*
     * Apply the virtual props to the actual node
     * 
     * Check out the CodePen for the rest of the code
     */
    applyProps(node, vNode.props);

    vNode.children
        // Recursively render the virtual children
        .map(render)
        // And attach them to the DOM node
        .forEach(childNode => {
            node.appendChild(childNode)
        });

    // Return the DOM node
    return node;
};
```

### Applying Props

### Managing Events

## Functional Views
And now, let's take our Todo App example from the previous posts and make a small change. Let's make the view a pure function of data:
```javascript
const TodoApp = (data) => createVNode('div', { className: 'container' }, 
    createVNode('h1', {}, 'Todo App'),
    createVNode('div', { className: 'form-group' },
        createVNode('input', { id: 'input', className: 'form-control', type: 'text', placeholder: 'Do laundry', value: data.input, onKeyUp: onInputKeyUp }),
        createVNode('br', {}),
        createVNode('button', { id: 'button', className: 'btn btn-primary', onClick: onButtonClick }, 'Add Todo')
    ),
    createVNode('h2', {}, 'Things to do:'),
    createVNode('ul', { id: 'list', className: 'list-group' },
        // Read from the data object to build the view
        ...data.todos.map(todoString => createVNode('li', { className: 'list-group-item' }, todoString))
    )
);
```

What we've done here is very simple but quite powerful.

By making the view a pure function of data, we've made the application very easy to reason about. Our UI's only concern is with presenting data: it doesn't make any decisions about how to update the data; it just accepts data and returns a view.

> # UI = F(data)

Take a look at the running CodePen example:

<p data-height="450" data-theme-id="0" data-slug-hash="qYjazN" data-default-tab="js,result" data-user="waltertan12" data-embed-version="2" data-pen-title="Virtual DOM Example 3" data-editable="true" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/qYjazN/">Virtual DOM Example 3</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

# Shortfalls
That example is kind of awesome, right?

However, if you've tried using it at all, you probably noticed some pretty obvious issues.

1. The input field loses focus _every_ time you type into it
2. The application gets slower and slower with every added item

These problems are both associated with the fact that we are re-rendering the entire DOM tree on every single change.

**Type into the input field?**

The app re-renders and the input field with focus on is replaced with a new input field without focus.

**Have 300 items in your todo list?**

The browser has to recalculate all the positions of the elements and repaint them onto the screen.

![Layout](https://developer.mozilla.org/files/464/Gecko_Overview_9.png)

Find out how we overcome this problem in the next post.

- Walter
