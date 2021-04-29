# User Preference Media Features Client Hints Header

This is the repository for User Preference Media Features Client Hints Header. You are welcome to
[contribute](CONTRIBUTING.md)!

## Authors:

- [Thomas Steiner](https://github.com/tomayac)

## Participate
- [Issue tracker](https://github.com/tomayac/user-preference-media-features-header/issues)

## Introduction

CSS media queries, and specifically [user preference media features](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences) like `prefers-color-scheme` or `prefers-reduced-motion`, have a potentially significant impact¹ on the amount of CSS that needs to be delivered by a page.

Focusing on `prefers-color-scheme`—but highlighting that the reasoning in this explainer applies to other user preference media features as well—it is a [best practice](https://web.dev/prefers-color-scheme/#loading-strategy) to *not* load CSS for the particular non-matching color scheme in the critical rendering path, and instead to initially only load the currently relevant CSS. One way of doing so is via `<link media>`.

However, high-traffic sites like [Google Search](https://www.google.com/) that wish to honor user preference media features like `prefers-color-scheme` and that inline CSS for performance reasons need to know about the preferred color scheme (or other user preference media features respectively) ideally at request time, so that the initial HTML payload already has the right CSS inlined.

## Proposed Solution: Client Hint

[HTTP Client Hints](https://datatracker.ietf.org/doc/html/rfc8942) defines an `Accept-CH` response header that servers can use to advertise their use of request headers for proactive content negotiation, colloquially referred to as "client hints". One such potential client hint that could help with the above scenario might be a tentatively titled `User-Pref-Media-Features` hint, which would notify the server about, for example, the currently preferred color scheme.

* Meta note 1:
This is somewhat of the inverse of what was proposed in https://github.com/w3c/csswg-drafts/issues/2370, where the `Save-Data` header was suggested to be exposed through a `prefers-reduced-data` media query.

* Meta note 2:
[HTTP Client Hints](https://datatracker.ietf.org/doc/html/rfc8942) no longer includes the concretely supported client hints, but did previously, which is why I decided to initially file this as an [Issue](https://github.com/w3c/csswg-drafts/issues/4162) in the [csswg-drafts](https://github.com/w3c/csswg-drafts) repository instead.

* Meta note 3:
The [`Accept-CH-Lifetime`](https://tools.ietf.org/html/draft-ietf-httpbis-client-hints-06#section-2.2.2) header field was likewise removed. This header would have enabled delivery of client hints on subsequent requests to the server's origin (and not just sub-resources).

### Proposed Syntax

User preference media features consist of a name (like `prefers-reduced-motion`) and allowed values (like `no-preference` or `reduce`. This maps perfectly to [Structured Headers for HTTP](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15),
specifically [Dictionaries](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15#section-3.2).
For example, to convey that the user prefers a dark theme and reduced motion, the header would look like in the example below.

```
User-Pref-Media-Features: prefers-color-scheme="dark", prefers-reduced-motion="reduce"
```

The CSS equivalent of the information conveyed in the above header would be `@media (prefers-color-scheme: dark) {}` and `@media (prefers-reduced-motion: reduce)` respectively.

## Privacy and Security Considerations

The [Security Considerations](https://datatracker.ietf.org/doc/html/rfc8942#section-4) of HTTP Client Hints
and the [Security Considerations](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02#section-5) of Client Hint Reliability likewise apply to this proposal.

## References

- [Client Hints](https://datatracker.ietf.org/doc/html/rfc8942)
- [Client Hint Reliability](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02)
- [Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme)
- [Structured Headers for HTTP](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15)

## Acknowledgements

Many thanks for valuable feedback and advice from:

- [Yoav Weiss](https://github.com/yoavweiss)

## Footnotes

——
¹ *"Implementing Dark Mode took over 1,000 lines of CSS"*—https://webkit.org/blog/8892/dark-mode-in-web-inspector/
