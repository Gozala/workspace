- Account is identified via [[did:key]] identifier
- Unknown [[did:key]] identifier represents **inactive account**. System does not track such accounts and treats them similar to accounts with `0` balance.
- Account [[storage capacity]] is derived from [[account balance]] by a TBD formula.
- Account can be activated by anyone, by transferring enough funds and increasing capacity to `>0`.
- Anyone with `account/activate` capability (delegated from [[account DID]]) can activate account by providing a valid email verification proof (that has not been associated with other account).
	- On activation `did:key:zTreasury` will transfer enough funds into account to maintain 1TB capacity.
	  > We would not actually charge actual credit cards, but it would look and feel just the same
- Excess account funds, that is amount not needed to pay for minimum 1TB or current used storage (whatev)
- Accounts funds can be used to
	- Pay for storage at the TBD rate.
	- To transfer excess funds to other accounts
	  > ⚠️ This implies user holding one account could open as many other accounts as desired, so we need to make sure the price we charge for 1TB is no greater than amount needed to activate new account otherwise users will be incentivized to open new accounts instead.
	- Pay for other features e.g. pinning service
- Anyone could setup an autopay for an account via `account/autopay` with upper limit.
	- Accounts with autopay are charged the upper limit during setup transferring increasing account balance & capacity.
	- On next billing card linked to autopay is charged to maintain it's capacity. If account did not used it's full capacity funds for remainder carries over meaning that card will be only charged for capacity actually used.
	- Second actor could further increase upper limit by setting up another card into autopay. In such scenario second card only gets charged on next billing cycle if account went beyond previous upper limit or if first card failed to pay.
	- If charging a credit card fails on billing cycle it's balance become negative and writing to that account will be denied until funds are going to become available again.