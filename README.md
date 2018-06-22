# The DOM Is a Tree

## Problem Statement

If we want to be able to select any element on our web page via the DOM, we need
to understand the way the DOM is structured. By tracing its hierarchy, we can
quickly and efficiently select the elements we need.

## Objectives

1. Define the computer science version of "tree"
2. Describe how the DOM works as a tree
3. Practice exploring the DOM

## Define the Computer Science Version of "Tree"

What do we mean when we say that the DOM is a tree? Trees make a good metaphor
for the DOM because we intuitively understand how they relate to the original
seed, the _root node_.

Starting at the original fertilized seed pod, branches extend outward and
upward. The oldest, thickest branches grow most directly from the original
seed. Thicker branches are eventually able to split into (multiple) thinner
_child_ branches.

The DOM works basically the same way, except we usually talk about the original
seed pod as being a _root node_ or a _root element_. Additionally, we turn the
biological tree, well, on its head. From the _root node_ branches extend down
and out so that the "leaves" are the most deeply nested HTML elements.

![Graphic of a DOM node by user Eib, via Wikipedia][dom-graphic]

## Describe How the DOM Works as a Tree

An interesting property of trees is that every tree can contain _subtrees_,
which we can treat independently of their parent trees.

Practically speaking, the DOM begins at `<html>`, but most of the time, we'll
prefer to do our JavaScript manipulations in the `<body>` _subtree_, because,
we can see it! Naturally, the `<body>` _subtree_ will have _subtrees_ (and
those trees will have _subtrees_...)

So, for example, if we have:

``` html
<body>
  <div>
    <p>Hi!</p>
  </div>

  <div>
    <p>Bye!</p>
  </div>
</body>
```

We would have a tree that looks like

``` shell
        body
        /  \
      div   div
      /      \
     p        p
    /          \
 "Hi!"        "Bye!"
```

Pretty simple, right? Similarly, if we had a DOM subtree that looked like

``` html
<div>
  <div>
    <h1>Hello!</h1>
  </div>

  <div>
    <h5>Sup?</h5>
  </div>
</div>
```

We could simply treat it as the tree

``` shell
         div
        /  \
      div   div
      /      \
    h1        h5
    /          \
 "Hello!"     "Sup?"
```

## Practice Exploring the DOM

When you learned HTML, you probably learned about adding attributes like
`class` and `id` to HTML elements. Later you also probably learned to use
semantic markup to accurately mark-up the purpose of your content. By having
meaningful attributes and semantic elements in our HTML, we find or "target"
the DOM elements.

### Finding a Node

JavaScript exposes a few ways of finding DOM nodes more or less directly,
courtesy of the `document` object.

#### `document.getElementById()`

This method provides the quickest access to a node, but it requires that we
know something very specific about it — the element's `id`. Since `id`s must be
unique, this method only returns one element. If you have two elements with the
same `id`, this method returns the first one — keep your `id`s unique! Consider
the following DOM tree:

``` html
<div>
  <h5 id="greeting">Hello!</h5>
</div>
```

We could find the `h5` element with `document.getElementById('greeting')`.
Notice how the `id` that we pass to `getElementById` is identical to the `id`
in `<h5 id="greeting">`? We can assign attributes to HTML nodes (or elements)
simply by including them between the `<>` tags at the start of the element (so
not in the `</h5>` tag, in this case).

**Try it out!**

Open up your web inspector (command+option+j on OS X) and find an element on
the page — make note of its `id`. Then open up your console, type
`document.getElementById('theIdYouTookNoteOf')`, and check out your handy dandy
DOM node. Try changing a few of its attributes!

#### `document.getElementsByClassName()`

This method, as its name implies, finds elements by their `className`. Unlike
`id`, `className` does not need to be unique; as such, this method returns an
`HTMLCollection`. `HTMLCollection`s have a `length` property which you can
access by adding `.length`

> **IMPORTANT**: If you're familiar with some programming concepts, you might
> be thinking "Oh, it gets an _array_ of HTML nodes with a class." **IT DOES
> NOT RETURN AN ARRAY**. This is a common source of frustration among new front-end
> programmers. As you learn about JavaScript `Array`s, later, resist thinking
> that `document.getElementsByClassName()` as returning them.

Given the following DOM tree

``` html
<!-- the `className` attribute is called `class` in HTML -- it's a bummer -->
<div>
  <div class="banner">
    <h1>Hello!</h1>
  </div>

  <div class="banner">
    <h1>Sup?</h1>
  </div>

  <div class="banner">
    <h5>Tinier heading</h5>
  </div>
</div>
```

we could find all of the elements with `className === 'banner'` by calling
`document.getElementsByClassName('banner')`.

**Try it out!**

Inspect the web page again, this time making note of a className. Get all
elements with that className and give 'em a look. Remember, you can use a `for`
loop to loop through them. (You can also assign the return value of
`document.getElementsByClassName()` to a variable: `var elements =
document.getElementsByClassName('banner')`.)

#### `document.getElementsByTagName()`

Suppose you don't know an element's ID but you do know its tag name (the tag
name is the main thing between the `<>`, e.g., `'div'`, `'span'`, `'h1'`, etc.).
Since tag names aren't unique, this method returns an `HTMLCollection` of 0 to
many nodes with the given tag.

**Try it out!**

Explore the DOM in console by typing `document.getElementsByTagName('div')`.
Remember, you can iterate through these elements using a simple `for` loop.

### Finding a Node Without Knowing Anything About It

What if we know next to nothing about an element? Or what if we're just
interested in finding out more about the child nodes of a given element? This is
where our knowledge of trees and nested data structures comes in handy!

Given the following DOM tree (which you can find in `index.html` — feel free to
open the file up in your browser to play along!)

``` html
<main>
  <div>
    <div>
      <p>Hello!</p>
    </div>
  </div>
  <div>
    <div>
      <p>Hello!</p>
    </div>
  </div>
  <div>
    <div>
      <p>Hello!</p>
    </div>
  </div>
</main>
```

how would we go about changing only the second "Hello!" to "Goodbye!"? We
couldn't just iterate over `document.getElementsByTagName('div')`, checking for
`textContent === "Hello!"`, because we'd inadvertently change all three
"Hello!"s. More importantly, the DOM might change (more on that later), and we
want to make sure that we're updating the right element.

Let's start by getting the `<main>` element

``` javascript
const main = document.getElementsByTagName('main')[0]
```

Then we can get the children of `main` using `main.children`, so we can get the
second child with `main.children[1]`.

``` javascript
const div = main.children[1]
```

Finally, we can get and update our `<p>` element with

``` javascript
// we can call getElementsByTagName() on an _element_
// to constrain the search to its children!
const p = div.getElementsByTagName('p')[0]

p.textContent = "Goodbye!"
```

Obviously, this way of accessing that text isn't fully generic, but it does a
good job of demonstrating the basic tools available to us for finding and
manipulating HTML elements.

## Resources

- [MDN - Document Object Model](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)

## Conclusion

Understanding the tree structure of the DOM helps us navigate its hierarchy. In subtrees and branches we can find the nodes we need by IDs, class names or tag names. Once we've selected our elements, we can use JavaScript to manipulate them.

<p class='util--hide'>View <a href='https://learn.co/lessons/the-dom-is-a-tree'>The DOM is a Tree</a> on Learn.co and start learning to code for free.</p>

[dom-graphic]:  https://curriculum-content.s3.amazonaws.com/skills-front-end-web-development/js-dom-and-events-the-dom-is-a-tree-readme/DOM-model.svg
