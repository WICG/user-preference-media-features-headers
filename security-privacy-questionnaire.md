# Questions to Consider

## What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?

This feature exposes information about user preferences like the preferred color scheme, if they prefer more contrast or less
transparency, if they would rather consume less data, etc. All this is information that is exposed through CSS (via`@media (…)`
rules) and that can also be queried programmatically via JavaScript (via `window.matchMedia('(…)').matches`. The new aspect is
that these user preferences could be communicated at the time of the initial request if the client hints are used as critical
client hints.
  
## Do features in your specification expose the minimum amount of information necessary to enable their intended uses?

Ultimately it is up to sites to choose the client hints they are interested in and that they deem critical client hints.

## How do the features in your specification deal with personal information, personally-identifiable information (PII), or information derived from them?

In combination, multiple client hints can increase the fingerprintability of a user. The same information is exposed via CSS,
though, as outlined above.

## How do the features in your specification deal with sensitive information?

Some user preferences may reveal details about a user, for example, if someone prefers contrast there is a certain likelihood
that the person in question has eye sight issues. The same information is exposed via CSS, though, as outlined above.

## Do the features in your specification introduce new state for an origin that persists across browsing sessions?

The opposite is the case. Currently, sites tend to store user preferences like the preferred color scheme in cookies, which
may be outdated if users set their preferred color scheme dynamically, for example, based on the time of day. With
this proposal, the objective is to get the most up-to-date state about the user preference to the server each time.

## Do the features in your specification expose information about the underlying platform to origins?

Not all platforms may support all user preferences. For example, a given Linux window manager may not have a specific
"less transparency" setting. This is indistinguishable from users who have never touched such a setting. In CSS, this
is most commonly exposed through the `no-preference` value.

## Does this specification allow an origin to send data to the underlying platform?

No.

## Do features in this specification allow an origin access to sensors on a user’s device

No.

## What data do the features in this specification expose to an origin? Please also document what data is identical to data exposed by other features, in the same or different contexts.

See [User Preference Media Features](https://drafts.csswg.org/mediaqueries-5/#mf-user-preferences) for the complete list.

## Do features in this specification enable new script execution/loading mechanisms?

No.

## Do features in this specification allow an origin to access other devices?

No.

## Do features in this specification allow an origin some measure of control over a user agent’s native UI?

If a user prefers a certain color scheme or less transparency or more contrast, etc., they usually do so at the
operating system level. This may change the way the UA gets presented theme-wise.

## What temporary identifiers do the feautures in this specification create or expose to the web?

The related client hint headers that get sent in the context of the loading of a site.

## How does this specification distinguish between behavior in first-party and third-party contexts?

N/A.

## How do the features in this specification work in the context of a browser’s Private Browsing or Incognito mode?

To be determined. It might be reasonable to not send the client hints in this context.

## Does this specification have both "Security Considerations" and "Privacy Considerations" sections?

Yes.

## Do features in your specification enable origins to downgrade default security protections?

No.

## What should this questionnaire have asked?

These very questions seem adequate.
