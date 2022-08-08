reply-to:: https://github.com/mikeal/car-transaction/blob/main/README.md

- Given the following code snippet I'm not sure I fully understand it
- ```js
  import Transaction from 'car-transaction' 
  
  const run = async () => {
    // start a basic transaction
    const t = Transaction.create()
  
    const subCID = await t.write({ some: 'data' })
    await t.write({ sub: subCID })
    const buffer = await t.commit()
  
    // read a transaction
    // the last write is always the root
    const { root, get } = await Transaction.load(buffer)
    // root is a cid
    const { sub } = await get(root)
    const { some } = await get(sub)
    // get retrieves the block and decodes it
    if (some !== 'data') throw new Error('data error')
  }
  ```
	- I am not sure I understand what `t` is. Is it a mutex with revision history that you update through transactions ?
		- Looking at the source I see it just a way to push some blocks into the CAR and make the last one it's root. It is very similar to [CAR codec implementation](https://github.com/web3-storage/ucanto/blob/0606168313d17d66bcc1ad6091440765e1700a4f/packages/transport/src/car/codec.js) in [[ucanto]] library, but there are some differences:
			- ucanto [[CAR]] codec implements [[BlockCodec]] interface so it can be used just like any other codec and all the layers above could just plug it right in.
			- [[BlockCodec]] interface adds constraint that you have to provide a data and get bytes back. In other words interface is not incremental and all data must be in memory. While it may seem bad in practice CAR writer needs to hold all blocks in memory anyhow and there is no real benefit to incremental write interface over batched write interface if things are kept in memory.
			- Use of JS array for aggregating blocks is more convenient than a custom writer API which is why ucanto implementation does not bother to have incremental API as well.
		- It is worth pointing out however that if CARs where streamed over the wire
-