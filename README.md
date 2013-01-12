
# entity

__Entity/Component Systems__ is a relatively new kind of game architecture
and growing in popularity. Example engines using it today include [Unity](http://unity3d.com/), [Ash](http://www.ashframework.org/), [Artemis](http://gamadu.com/artemis/).

[This project](https://github.com/entity) aims to bring entity/component systems to Javascript development, with the aid of a package manager, [component](https://github.com/component).

There are plenty of articles describing entity systems so I won't go into much detail, however a brief explanation is still required if you have never heard of them before.

In traditional OOP we have classes and then we create objects using those classes to build up the required behavior.

The problem why this pattern isn't working well with game development has to do with the fact that different objects in a game world often have more in common than not.

Now, we could use multiple inheritance and/or deep inheritance hierarchies but the problem is solved much better through composition or mixins.

This is the problem entity/component systems are attempting to solve.

Instead of one big tree of inheritance, we instead compose objects (or entities) with components (data) and then we have systems (objects with methods) that apply certain behavior on entities that have certain components.

The idea is that component/systems should be focused and decoupled from the actual game logic and thus can be _reused_ across different games.

To achieve this, we need to share and agree on a certain degree of conventions.

These are what I am using for my manager implementation, but they're not set in stone in any way, so these can change at any time if a better pattern emerges. Feel free to share your thoughts on the following:

The absolute basic events are: `init` `start` `pause` `stop` `tear`

The observer pattern is used extensively in games, for this reason _all_ methods of a system are automatically also listeners for those events.

An example system implementation would look like this:

```js
var dom = {}

dom.start = function (e) {
  document.body.appendChild(e.el)
}

dom.stop = function (e) {
  document.body.removeChild(e.el)
}
```

Easy, huh? Suppose our dom system also wants to listen to `render` events, just add the following method:

```js
dom.render = function (e) {
  css(e.el, {
    left: Math.round(e.pos.left)
  , top: Math.round(e.pos.top)
  })
}
```

The manager will then call this system's method for every `render` event, passing it the entities it cares about.

To link systems and entities, so that the manager knows which entities should be passed to each system to operate on, one would just need to `use` them by passing the system reference:

```js
var entity = world.createEntity()
entity.use(dom)

// alternative style
var entity = world.createEntity(dom)
```

We also need some properties (or components) to operate on, `el` and `pos` are shown here.

Components are also easy to create and attach:

```js
var v = require('vector')

var position = {
  pos: [v, 0,0]
}

entity.use(position)
```

Component values are created by passing a constructor-like function and a set of arguments to pass to it. In this case it creates a 2D [vector](https://github.com/entity/vector).

Usually, there is an 1-to-1 relationship between components and systems, thus you can mix them in the same object:

```js
function domEl () {
  return document.createElement('div')
}

var dom = {}

dom.el = [domEl]
dom.class = [String, 'dom-entity']

dom.init = function (e) {
  e.el.classList.add(e.class)
}

dom.start = function (e) {
  document.body.appendChild(e.el)
}

dom.stop = function (e) {
  document.body.removeChild(e.el)
}
```

Looks simple? Well... it is!

These tiny semantics make for a very powerful game development architecture.

Forget long pages of documentation, conventions over more conventions and opinionated monolithic game engines.

[Start](https://github.com/entity) building component/systems and never look back, or maybe play some [arkanoid](http://entity.github.com/arkanoid/) ([source](https://github.com/entity/arkanoid)) if you don't feel like working today.
