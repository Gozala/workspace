- [[indieweb]] reply (or **comment**) is a kind of a [post](https://indieweb.org/post) that is in response to some other post.
- It is very simple and elegant solution and can be captured in the simple piece of HTML
  ```html
  <div class="h-entry">
   <a href="http://example.com/note123" class="u-in-reply-to">Some note with a point</a>
   <div class="p-name p-content">Good point! Now what is the next thing we should do?</div>
  </div>
  ```
- While I do not think HTML is the way to capture