reply-to:: https://spritely.institute/static/papers/spritely-core.pdf

- Reading through [[The Heart of Spritely]] I have capture some of my observations
- Actors a.k.a object constructors take `bcom` argument that can be used to update it's behavior.
	- This allows defining object behaviors in a pure fashion.
	- This does reminds me of [Elm Architecture](https://guide.elm-lang.org/effects/) that we can simplify as a function that takes incoming message and returns new state & outgoing message
	  
	  ```ts
	  interface Actor<Model, In, Out> {
	    update(message:In, state:Model): [Model, Out]
	  }
	  ```
	- I actually like Elm variant better because `state` is always explicit, in other words instead of switching behavior you just have a field in `state` that tells you it's current behavior.
- [[Promise pipelining]] is really cool feature that allows you to batch up several operations, but it seems really tricky to do in typed language settings. More specifically
	- How do you know if return type is going to be an "object" or some data ?
	- What if return type is an "object" but has a different interface than you'd expect ?
	- What if object interface has changed (as in new version got deployed in the meantime) ?
	- I suspect in all of those cases you get an exception, but living with languages that