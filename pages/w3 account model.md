- Account is identified via [[did:key]] identifier
- Unknown [[did:key]] identifier represents [[inactive account]]. System does not track such accounts and treats them similar to accounts with `0` balance.
- User can [[activate account]] via `account/register` capability, that can be derived from  (that MUST be delegated by [[account DID]]) which must provide `email/verify`
- Account [[storage capacity]] is derived from [[account balance]] by a TBD formula.
- Account can be activated by anyone, by transferring enough funds to result in non zero capacity.
	- TODO Work out inconsistency with 1TB minimum capacity requirement for regular accounts. E.g. if we remove 1TB minimum it would
- Anyone with `account/activate` capability (delegated from [[account DID]]) can activate account by providing a valid email verification proof (that has not been associated with other account).
	- On activation `did:key:zTreasury` will transfer enough funds into account to maintain 1TB capacity.
	  > We would not be moving actual money or charging credit cards in this process, but will create same transaction log as when regular account funds another.
- Account funds needed to pay for minimum [[storage capacity]], that is used storage or 1TB whichever's greater) get locked at every billing cycle to pay for service.
- When new data is added to an account it is charged the difference from available funds. If account does not has enough funds it's [[over capacity]] and future service may get denied to it.
- Excess account funds (amount past what is needed for rent for minimum 1TB or currently used storage (whatever's greatest) could be transferred to other accounts.
  > This implies that you can't just create new accounts to overcome free 1TB limit. And this new accounts may have capacity smaller than 1TB.
- Account funds can also be allocated to pay for other features e.g. for pinning service.
- Anyone could setup an autopay for an account via `account/autopay` to purchase specified capacity on each billing cycle.
	- Accounts with autopay are charged the specified amount on setup.
	- On next billing cycle autopay will charge amount necessary to fund specified capacity. If account has not used purchased capacity in last cycle funds from last cycle carry over, meaning autopay will only charge for used capacity.
	- Second actor could also purchase more capacity by setting another autopay. In such scenario second card only gets charged if account goes beyond prior capacity.
	- If charging a credit card fails on billing cycle it's capacity is reduced accordingly. If account hold data over it's capacity storing more data into it will be denied.
- ```ts
  interface Account {
    /**
     * Accounts that are subsidized by this account. This account is billed
     * for all it's donees.
     */
    donees: Set<Account>
    
    /**
     * Set of CARs stored in this account
     */
    store: Map<CARLink, CAR>
  }
  
  interface AccountView extends Account {
    /**
     * Virtual field 
     */
    did(): DID
    /**
     * Returns set of donor accounts that fund this account.
     */
    donors(): Set<Account>
    
    balance(): USD
    
    /**
     * Account capacity is derived from it's balance.
     */
    capacity(): number
  }
  
  interface AccountCapabilities {
    fund(account:Account): Link
    transfer(balance: USD, to:Account): Link
  }
  
  type USD = `${number}USD`
  type DID = `did:key:${string}`
  ```