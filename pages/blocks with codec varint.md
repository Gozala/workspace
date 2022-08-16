reply-to:: https://purrfect-tracker-45c.notion.site/ipfs-protocols-from-the-ground-f95cdf5d0fc14abda6df4ff16caaa4df

- [[@mikeal]] wrote wrote nice essay about use of sef-describing data in [[IPFS]] ecosystem. 
  > **self-description** is the principal that the *context necessary to interpret data should be included in the data itself*.
- I have wondered why does self-describing principal has not been applied to [block](https://ipld.io/docs/intro/primer/#blocks-vs-nodes)s. Instead of making blocks self-describing and embedding [[multiformat]] **codec** identifier inside a block, that information is kept out of bound in Content Identifier (CID).
	- Implication is that [[block]]s aren't self-describing and can not be
-