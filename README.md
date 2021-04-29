# User Preference Media Features Client Hints Header explainer

This is the repository for User Preference Media Features Client Hints Header. You're welcome to
[contribute](CONTRIBUTING.md)!

## Authors:

- [Thomas Steiner](https://github.com/tomayac)

## Participate
- [Issue tracker](https://github.com/tomayac/user-preference-media-features-header/issues)

## Introduction

CSS media queries, and specifically [user preference media features](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences) like `prefers-color-scheme`, have a potentially significant impact¹ on the amount of CSS that needs to be delivered by a page.

Focusing on `prefers-color-scheme`—but highlighting that the reasoning in this issue applies to other user preference media features as well—it is a [best practice](https://web.dev/prefers-color-scheme/#loading-strategy) to *not* load CSS for the particular non-matching color scheme in the critical rendering path, and instead to initially only load the currently relevant CSS. One way of doing so is via `<link media>`.

However, high-traffic sites like [Google Search](https://www.google.com/) that wish to honor user preference media features like `prefers-color-scheme` and that inline CSS for performance reasons need to know about the preferred color scheme (or other user preference media features respectively) ideally at request time, so that the initial HTML payload already has the right CSS inlined.

## Potential Solutions

### Client Hint

[HTTP Client Hints](https://httpwg.org/http-extensions/client-hints.html) defines an `Accept-CH` response header that servers can use to advertise their use of request headers for proactive content negotiation, colloquially referred to as "client hints" ([demo](https://client-hints-demo.appspot.com/), try it in a current version of Chrome). One such potential client hint that could help with the above scenario might be a tentatively titled `Media-Feature` hint, which would notify the server about, for example, the currently preferred color scheme.

* Meta note 1:
This is somewhat of the inverse of what is being proposed in https://github.com/w3c/csswg-drafts/issues/2370, where the `Save-Data` header is suggested to be exposed through a `prefers-reduced-data` media query.

* Meta note 2:
[HTTP Client Hints](https://httpwg.org/http-extensions/client-hints.html) as of draft [07](https://httpwg.org/http-extensions/client-hints.html#since-07) no longer includes the concretely supported client hints as it was still the case with [06](https://tools.ietf.org/html/draft-ietf-httpbis-client-hints-06#section-3), which is why I decided to file this here in the [csswg-drafts](https://github.com/w3c/csswg-drafts) repo.

* Meta note 3:
The [`Accept-CH-Lifetime`](https://tools.ietf.org/html/draft-ietf-httpbis-client-hints-06#section-2.2.2) header field was likewise [removed in draft 07](https://httpwg.org/http-extensions/client-hints.html#since-07). This header would have enabled delivery of client hints on subsequent requests to the server's origin (and not just sub-resources).

### New HTTP Header

Given the changes in *Meta note 3*, an alternative approach could be to introduce a whole new `Media-Feature` header (obviously likewise only a tentative name) rather (than a client hint), similar as what has happened with [`Save-Data`](https://wicg.github.io/netinfo/#save-data-request-header-field), which [up until draft 06](https://httpwg.org/http-extensions/client-hints.html#since-06) was a client hint before being promoted to the [Network Information API](https://wicg.github.io/netinfo/).

### Proposed Syntax

The header in both cases (*Client Hint* or *New HTTP Header*) could look something like in the example below, with the concrete user preference media features as a comma-separated list:

```
Media-Feature: prefers-color-scheme=dark, prefers-reduced-motion=reduce
```

## Privacy Considerations

The [fingerprinting discussions](https://github.com/httpwg/http-extensions/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+label%3Aclient-hints+fingerprinting) in [Client Hints](https://github.com/httpwg/http-extensions#client-hints) likewise apply.

## References

- [Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme)

## Acknowledgements

Many thanks for valuable feedback and advice from:

- [Yoav Weiss](https://github.com/yoavweiss)

## Footnotes

——
¹ *"Implementing Dark Mode took over 1,000 lines of CSS"*—https://webkit.org/blog/8892/dark-mode-in-web-inspector/
