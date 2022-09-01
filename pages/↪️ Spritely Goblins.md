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
		-
	-