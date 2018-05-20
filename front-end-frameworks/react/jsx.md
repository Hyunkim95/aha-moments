# JSX

## Transpilation

**before:**

```
/** @jsx h */
let foo = <div id="foo"> Hello </div>;
```

**after:**

```
var foo = h('div', {id: "foo"}, 'Hello');
```

```
function h (nodeName, attributes, ...args) {
	let children = args.length ? [].concat(...args) : null;
	return { nodeName, attributes, children }
}
```

This results in a tree structure

```
{
	nodeName: "div",
	attributes: {
		"id": "foo"
	},
	children: ["Hello"]
}
```

Render function

```
function render (vnode) {
	// Strings just convert to #text nodes:
	if (vnode.split) return document.createTextNode(vnode);
	
	// Create a DOM element with the nodeName of our VDOM element:
	let n = document.createElement(vnode.nodeName);
	
	// Copy attributes onto the new node:
	let a = vnode.attributes || {};
	Object.keys(a).forEach( k => n.setAttribute(k, a[k]));
	
	// render (build) and then append child nodes:
	(vnode.children || []).forEach(c => n.appendChild(render(c)) );
	
	return n
}
```

```
let items = ['foo', 'bar', 'baz'];

// creates one list item given some text
function item (text) {
	return <li> { text } </li>
}

// a "view" with "iteration" and a "partial"
let list = render( 
	<ul> 
	{
		item.map(item)
	} 
	</ul>
);
```
* render() returns a DOM node, so we just need to place that into the DOM `document.body.appendChild(list);`


### Example Code
```
const ITEMS = 'hello there people'.split(' ');

let list = items => items.map( p => <li> {p} </li> );

// view with a call out "partial" to generate a list from an Array:
let vdom = (
	<div id="foo">
		<p> Look, a simple JSX DOM renderer! </p>
		<ul> { list(ITEMS) } </ul>
	</div>
)

// render() converts our "virtual DOM" to a real DOM tree:
let dom = render(vdom);

// append the new nodes somewhere
document.body.appendChild(dom);

let json = JSON.stringify(vdom, null, ' ');

document.body.appendChild( 
	render( <pre id="vdom"> { json } </pre> )
)
```

