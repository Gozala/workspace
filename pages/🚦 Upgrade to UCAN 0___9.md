title:: 🚦 Upgrade to UCAN 0.9

- Upcoming [[UCAN 0.9]] spec changes quite a few things that affect significantly [[dag.house]] code base.
	- The way own capabilities are issued changes from signaling through `my` URI protocol scheme to signaling through designated `my` field. This affects
		- [[self-issued UCANs]] which our clients use
		- UCAN validator logic
		- Bunch constructs in [[ucanto]] library due to structure changes.
	- `as:did:key:zAlice` protocol schema changed to `owned://did:key:zAlice` which affects how validator, although this is probably straightforward change.
	- Structure of [[dag-ucan]] is also affected and needs to be updated accordingly.
- 💭 I think it would be a good idea to delay full UCAN rollout until we switch to 0.9 so we don't have to deal with backwards compatibility or having to support two different UCAN structures.