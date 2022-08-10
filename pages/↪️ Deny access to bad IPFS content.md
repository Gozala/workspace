reply-to:: https://filecoinproject.slack.com/archives/C02BZPRS9HP/p1659406411336629

- It looks like [IPFS is becoming new hotbed for phishing](https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/ipfs-the-new-hotbed-of-phishing/), perhaps that is unsurprising because permissionless publishing and censor resistance can be exploited to an attackers advantage.
- There are effort to address this by creating standard [[denylist format for IPFS Gateways]]
- Consensus seems to be around idea of **denylists** that various services could share among each other to recognize bad content and deny access to it. However interesting question is how do we communicate with a user that content they are trying to access was blocked because it is a [[phishing attack]] ? I would argue that we can have a [[better errors with HTTP redirects]] as we could render an explanation of
	- Why access to content has been denied
	- How do we came to that conclusion (E.g. google malware detection, GDPR take down request)
- Doing this turns IPFS gateway into [[user agent]] trying to protect user as opposed to be portrayed as accomplice of the attack. In fact gateways could lean further into [[user agent]] role and proactively flag **unverified content** and maybe even provide toggle to a user to set the comfort level
	- How aggressive it should be in terms of flagging content
	- Allow subscribing to user desired [[denylists]] (although doing that may prove a lot more challenging on server than on client end, but perhaps service workers can help with that)
- Every time idea of enhancing