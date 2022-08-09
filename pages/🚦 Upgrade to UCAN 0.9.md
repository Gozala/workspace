title:: 🚦 Upgrade to UCAN 0.9

- Upcoming [[UCAN 0.9]] spec changes quite a few things that affect significantly [[dag.house]] code base.
	- The way own capabilities are issued changes from signaling through `my` URI protocol scheme to signaling through designated `my` field. This affects [[self-issued UCANs]] that our clients will use, UCAN validator and bunch of other pieces of [[ucanto]] library.
	- `as:did:key:zAlice` protocol schema changes to `owned://did:key:zAlice` which affects how validator.
	- Above changes also affect [[dag-ucan]] library which will need to be changed accordingly.