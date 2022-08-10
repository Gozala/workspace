- > The HTTP ** `Content-Security-Policy` ** response header allows
    web site administrators to control resources the user agent is allowed to load for a
    given page. With a few exceptions, policies mostly involve specifying server origins and
    script endpoints. This helps guard against cross-site scripting attacks
    ([Cross-site_scripting](https://developer.mozilla.org/en-US/docs/Glossary/Cross-site_scripting)).
- Phishing attacks are used to steal user data, including login credentials and credit card numbers. Attacker pretends to be a trusted entity requiring victim to enter some information which attacker can steal by sending it to a host under their control. However stealing/sending requires performing an HTTP request to an attackers host (E.g. using `fetch` API or by embedding a content that will cause browser to perform that HTTP request).
- This is not a a new problem and web platform had developed solution in form of [Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) headers
  > The HTTP `Content-Security-Policy` response header allows
    web site administrators to control resources the user agent is allowed to load for a
    given page. With a few exceptions, policies mostly involve specifying server origins and
    script endpoints. This helps guard against cross-site scripting attacks
    ([Cross-site_scripting](https://developer.mozilla.org/en-US/docs/Glossary/Cross-site_scripting)).
- IPFS Gateways could set `Content-Security-Policy` which would preventing accessing any external content (Any URLs outside of gateway domain), rendering phishing attacks ineffective, since it would be impossible to steal a victims data.