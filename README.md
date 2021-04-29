# `User-Pref-Media-Features` Client Hints Header

This is the repository for the `User-Pref-Media-Features` Client Hints Header.
You are welcome to [contribute](CONTRIBUTING.md)!

## Authors:

- [Thomas Steiner](https://github.com/tomayac)

## Participate

- [Issue tracker](https://github.com/tomayac/user-preference-media-features-header/issues)

## Introduction

CSS media queries, and specifically
[user preference media features](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences)
like `prefers-color-scheme` or `prefers-reduced-motion`, have a potentially
significant impact[¹](#footnotes) on the amount of CSS that needs to be
delivered by a page.

Focusing on `prefers-color-scheme`—but highlighting that the reasoning in this
explainer applies to other user preference media features as well—it is a
[best practice](https://web.dev/prefers-color-scheme/#loading-strategy) to _not_
load CSS for the particular non-matching color scheme in the critical rendering
path, and instead to initially only load the currently relevant CSS. One way of
doing so is via `<link media>`.

However, high-traffic sites like [Google Search](https://www.google.com/) that
wish to honor user preference media features like `prefers-color-scheme` and
that inline CSS for performance reasons, need to know about the preferred color
scheme (or other user preference media features respectively) ideally at request
time, so that the initial HTML payload already has the right CSS inlined.

Additionally, and specifically for `prefers-color-scheme`, sites by all means
want to avoid a Flash of inAccurate coloR Theme
([FART](https://css-tricks.com/flash-of-inaccurate-color-theme-fart/)).

## Proposed Solution

### Client Hint

[HTTP Client Hints](https://datatracker.ietf.org/doc/html/rfc8942) defines an
`Accept-CH` response header that servers can use to advertise their use of
request headers for proactive content negotiation, colloquially referred to as
"client hints". One such potential client hint that could help with the above
scenario might be a tentatively titled `User-Pref-Media-Features` hint, which
would notify the server about, for example, the currently preferred color
scheme.

- Meta note 1: This is somewhat of the inverse of what was proposed in
  https://github.com/w3c/csswg-drafts/issues/2370, where the `Save-Data` header
  was suggested to be exposed through a `prefers-reduced-data` media query.

- Meta note 2:
  [HTTP Client Hints](https://datatracker.ietf.org/doc/html/rfc8942) no longer
  includes the concretely supported client hints, but did previously, which is
  why I decided to initially file this as an
  [Issue](https://github.com/w3c/csswg-drafts/issues/4162) in the
  [csswg-drafts](https://github.com/w3c/csswg-drafts) repository instead.

- Meta note 3: The
  [`Accept-CH-Lifetime`](https://tools.ietf.org/html/draft-ietf-httpbis-client-hints-06#section-2.2.2)
  header field was likewise removed. This header would have enabled delivery of
  client hints on subsequent requests to the server's origin (and not just
  sub-resources).

- Meta note 4: The proposed client hint would be a
  [Critical Client Hint](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02).
  Critical Client Hints are Client Hints which meaningfully change the resulting
  resource. Such a resource should be fetched consistently across page loads to
  avoid jarring user-visible switches.

### Proposed Syntax

User preference media features consist of a name (like `prefers-reduced-motion`)
and allowed values (like `no-preference` or `reduce`. This maps perfectly to
[Structured Headers for HTTP](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15),
specifically
[Dictionaries](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15#section-3.2)
with
[Tokens](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15#section-3.3.4)
as values. For example, to convey that the user prefers a dark theme and reduced
motion, the header would look like in the example below.

```
User-Pref-Media-Features: prefers-color-scheme="dark", prefers-reduced-motion="reduce"
```

The CSS equivalent of the information conveyed in the above header would be
`@media (prefers-color-scheme: dark) {}` and
`@media (prefers-reduced-motion: reduce) {}` respectively.

## Example

1. The client makes an initial request as follows.
   ```bash
   GET / HTTP/1.1
   Host: example.com
   ```
1. The server responds, telling the client that it accepts the
   `User-Pref-Media-Features` and the `Sec-CH-Example` Client Hints, out of
   which it considers `User-Pref-Media-Features` a Critical Client Hint that it
   also varies the response on.
   ```bash
   HTTP/1.1 200 OK
   Content-Type: text/html
   Accept-CH: User-Pref-Media-Features, Sec-CH-Example
   Vary: User-Pref-Media-Features
   Critical-CH: User-Pref-Media-Features
   ```
1. The client then retries the request, telling the server that it has a user
   preference for dark-schemed content.
   ```bash
   GET / HTTP/1.1
   Host: example.com
   User-Pref-Media-Features: prefers-color-scheme="dark"
   Sec-CH-Example: 1
   ```
1. The server can then tailor the response accordingly.

## Privacy and Security Considerations

The
[Security Considerations](https://datatracker.ietf.org/doc/html/rfc8942#section-4)
of HTTP Client Hints and the
[Security Considerations](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02#section-5)
of Client Hint Reliability likewise apply to this proposal.

## References

- [Client Hints](https://datatracker.ietf.org/doc/html/rfc8942)
- [Client Hint Reliability](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02)
- [Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme)
- [Structured Headers for HTTP](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15)

## Acknowledgements

Many thanks for valuable feedback and advice from:

- [Yoav Weiss](https://github.com/yoavweiss)

## Footnotes

1. _"Implementing Dark Mode took over 1,000 lines of
   CSS"_—https://webkit.org/blog/8892/dark-mode-in-web-inspector/
