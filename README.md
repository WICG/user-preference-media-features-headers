# User Preference Media Features Client Hints Headers

This is the repository for a set of
[HTTP Client Hints](https://datatracker.ietf.org/doc/html/rfc8942) headers around
[user preference media features](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences) as
defined by
[Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme).
You are welcome to [contribute](CONTRIBUTING.md)!

## Authors:

- [Thomas Steiner](https://github.com/tomayac)

## Participate

- [GitHub repository](https://github.com/tomayac/user-preference-media-features-headers)
- [Issue tracker](https://github.com/tomayac/user-preference-media-features-headers/issues)

## Introduction

CSS media queries, and specifically
[user preference media features](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences) like
`prefers-color-scheme` or `prefers-reduced-motion`, have a potentially significant
impact[¹](#footnotes) on the amount of CSS that needs to be delivered by a page, and on the
experience the user is going to have when the page loads.

Focusing on `prefers-color-scheme`—but highlighting that the reasoning in this explainer applies to
other user preference media features as well—it is a
[best practice](https://web.dev/prefers-color-scheme/#loading-strategy) to _not_ load CSS for the
particular non-matching color scheme in the critical rendering path, and instead to initially only
load the currently relevant CSS. One way of doing so is via `<link media>`.

However, high-traffic sites like [Google Search](https://www.google.com/) that wish to honor user
preference media features like `prefers-color-scheme` and that inline CSS for performance reasons,
need to know about the preferred color scheme (or other user preference media features respectively)
ideally at request time, so that the initial HTML payload already has the right CSS inlined.

Additionally, and specifically for `prefers-color-scheme`, sites by all means want to avoid a Flash
of inAccurate coloR Theme ([FART](https://css-tricks.com/flash-of-inaccurate-color-theme-fart/)).

## Proposed Solution

### Client Hints

[HTTP Client Hints](https://datatracker.ietf.org/doc/html/rfc8942) defines an `Accept-CH` response
header that servers can use to advertise their use of request headers for proactive content
negotiation, colloquially referred to as client hints. This proposal is about a set of new client
hints aimed at conveying user preference media features. These client hints are named after the
corresponding user preference media feature that they report on. For example, the currently
preferred color scheme as per `prefers-color-scheme` is reported via the aptly named
`Sec-CH-Prefers-Color-Scheme` client hint.

> Meta note: This is somewhat of the inverse of what was proposed in
> [w3c/csswg-drafts#2370](https://github.com/w3c/csswg-drafts/issues/2370), where the `Save-Data`
>  client hint was suggested to be exposed through a `prefers-reduced-data` user preference media
> feature.

### Critical Client Hints

It is the expectation of the author that the proposed client hints will most commonly be used as
[Critical Client Hints](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02).
Critical Client Hints are Client Hints which meaningfully change the resulting resource. Such a
resource should be fetched consistently across page loads (including the initial page load) to avoid
jarring user-visible switches.

### Proposed Syntax

User preference media features consist of a name (like `prefers-reduced-motion`) and allowed values
(like `no-preference` or `reduce`. Each client hint header field is represented as
[Structured Headers for HTTP](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15)
object containing an
[item](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15#section-3.3) whose value
is a [string](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-15#section-3.3.3). For
example, to convey that the user prefers a dark theme and reduced motion, the client hints look like
in the example below.

```
Sec-CH-Prefers-Color-Scheme: "dark"
Sec-CH-Prefers-Reduced-Motion: "reduce"
```

The CSS equivalent of the information conveyed in the above client hints is
`@media (prefers-color-scheme: dark) {}` and `@media (prefers-reduced-motion: reduce) {}`
respectively.

## Complete List of the Client Hints

The list of the client hints is modeled after the
[user preference media features](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences) in
[Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme).

| Client Hint                           | Allowed Values                                                                                                                                                                                                                                                                        | Corresponding User Preference Media Feature                                                             |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `Sec-CH-Prefers-Reduced-Motion`       | [`no-preference`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-reduced-motion-no-preference), [`reduce`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-reduced-motion-reduce)                                                                        | [`prefers-reduced-motion`](https://drafts.csswg.org/mediaqueries-5/#prefers-reduced-motion)             |
| `Sec-CH-Prefers-Reduced-Transparency` | [`no-preference`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-reduced-transparency-no-preference), [`reduce`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-reduced-transparency-reduce)                                                            | [`prefers-reduced-transparency`](https://drafts.csswg.org/mediaqueries-5/#prefers-reduced-transparency) |
| `Sec-CH-Prefers-Contrast`             | [`no-preference`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-contrast-no-preference), [`less`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-contrast-less), [`more`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-contrast-more) | [`prefers-contrast`](https://drafts.csswg.org/mediaqueries-5/#prefers-contrast)                         |
| `Sec-CH-Forced-Colors`                | [`active`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-forced-colors-active), [`none`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-forced-colors-none)                                                                                                            | [`forced-colors`](https://drafts.csswg.org/mediaqueries-5/#forced-colors)                               |
| `Sec-CH-Prefers-Color-Scheme`         | [`light`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-color-scheme-light), [`dark`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-color-scheme-dark)                                                                                                | [`prefers-color-scheme`](https://drafts.csswg.org/mediaqueries-5/#prefers-color-scheme)                 |
| `Sec-CH-Prefers-Reduced-Data`         | [`no-preference`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-reduced-data-no-preference), [`reduce`](https://drafts.csswg.org/mediaqueries-5/#valdef-media-prefers-reduced-data-reduce)                                                                            | [`prefers-reduced-data`](https://drafts.csswg.org/mediaqueries-5/#prefers-reduced-data)                 |

> Meta note: Since [`Save-Data`](https://wicg.github.io/savedata/#save-data-request-header-field) is
> prior art, should `Sec-CH-Prefers-Reduced-Data` be defined for consistency?

## Example

1. The client makes an initial request to the server.
   ```bash
   GET / HTTP/1.1
   Host: example.com
   ```
1. The server responds, telling the client that it accepts the `Sec-CH-Prefers-Color-Scheme` and the
   `Sec-CH-Prefers-Contrast` Client Hints, out of which it considers `Sec-CH-Prefers-Color-Scheme` a
   Critical Client Hint that it also varies the response on.
   ```bash
   HTTP/1.1 200 OK
   Content-Type: text/html
   Accept-CH: Sec-CH-Prefers-Color-Scheme, Sec-CH-Prefers-Contrast
   Vary: Sec-CH-Prefers-Color-Scheme
   Critical-CH: Sec-CH-Prefers-Color-Scheme
   ```
1. The client then retries the request, telling the server that it has a user preference for
   dark-schemed content.
   ```bash
   GET / HTTP/1.1
   Host: example.com
   Sec-CH-Prefers-Color-Scheme: "dark"
   ```
1. The server can then tailor the response to the client's preferences accordingly.

## Privacy and Security Considerations

The [Security Considerations](https://datatracker.ietf.org/doc/html/rfc8942#section-4) of HTTP
Client Hints and the
[Security Considerations](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02#section-5)
of Client Hint Reliability likewise apply to this proposal.

## References

- [Client Hints](https://datatracker.ietf.org/doc/html/rfc8942)
- [Client Hint Reliability](https://tools.ietf.org/html/draft-davidben-http-client-hint-reliability-02)
- [Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme)
- [Structured Headers for HTTP](https://tools.ietf.org/html/draft-ietf-httpbis-header-structure-19)
- [`Save-Data` Client Hint](https://wicg.github.io/savedata/#save-data-request-header-field)

## Acknowledgements

Many thanks for valuable feedback and advice from:

- [Yoav Weiss](https://github.com/yoavweiss)

## Footnotes

1. _"Implementing Dark Mode took over 1,000 lines of
   CSS"_—https://webkit.org/blog/8892/dark-mode-in-web-inspector/
