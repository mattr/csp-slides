+++ title = "Content Security Policy"
+++
<!--: .wrap .size-70 ..aligncenter -->

## **What is CSP?**

<!--: .text-intro -->

Content Security Policy (CSP) determines where resources (JavaScript, stylesheets, fonts etc)
can be loaded from.

---

<!--: .wrap -->

## How does it work?

CSP provides a number of *directives* which are included in the `Content-Security-Policy` HTTP
Header, or an equivalent `meta` tag. (If using a `meta` tag, it should be early in the `head`, as
only resources listed *after* the declaration will be included in the policy.)

Some common (and useful ones) are:

* `default-src`
* `script-src`
* `font-src`
* `style-src`
* `img-src`
* `connect-src`

The whole list is available
at https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy

---

<!--: .wrap ..aligncenter -->

## **Directives**

---

<!--: .wrap -->

## Directives

### `default-src`

This provides the default settings for any directives which are not otherwise included in the
policy.

Note that it is a fallback for when a directive is not declared; if you specify a directive,
`default-src` is ignored for that type.

---

<!--: .wrap -->

## Directives

### `default-src` Example

```http request
Content-Security-Policy: default-src 'self' https:; script-src 'self';
```

Anything which is *not* a script will use the default source, e.g. stylesheets, fonts, etc. These
can all be loaded either from `'self'` (i.e. the request origin) **or** any connection as long as it
is over `https`.

Scripts, however, can only be served from the local server.

---

<!--: .wrap -->

## Directives

### `script-src`

Defines where `script`s may be loaded from. This is almost exclusively for JavaScript.

### `style-src`

Defines where `style` elements (including stylesheets) may be loaded from.

### `img-src`

Exactly what you'd expect...

### `font-src`

This one too...

---

<!--: .wrap -->


## Directives

### `connect-src`

This restricts the URLs which can be loaded using script interfaces, for example, remote scripts
which are loaded by Google Tag Manager and other resources fetched via `XMLHttpRequest` to be loaded
into the page.

---

<!--: .wrap ..aligncenter -->

## **Values**

---

<!--: .wrap -->

## Values

### `'self'`

The request origin (including URL and port). (Quotes are required.)

---

<!--: .wrap -->

## Values

### Scheme sources (ends with ':')

This allows for the protocol or data scheme, such as `https:` or `data:` or `blob:`. Generally data
schemes are not recommended as they ar insecure; however, there is some application for using
`data:` for fonts and images, and `blob:` is useful for Rails in development mode when using
webpacker. (Quotes are not required.)

---

<!--: .wrap -->

## Values

### URL

Allows content to be loaded from a particular remote URL. This can optionally include the protocol,
and accepts wildcards.

Example:

```http request
Content-Security-Policy: default-src 'self' https://my.example.com; script-src 'self' *.cdnjs.com;
```

---

<!--: .wrap -->

## Values

### `'none'`

Empty set. Allows no resources. Usually used for content elements like `object` where it is unlikely
to be used. (Quotes are required.)

---

<!--: .wrap -->

## Values

### `'unsafe-inline'`

Allows the execution of inline content on the page, e.g. a `script` or `style` tag.

### `'unsafe-eval'`

Allows execution of JavaScript `eval` or `Function` declarations to load content.

Both of these directives are considered unsafe and should be avoided if at all possible, or used in
conjunction with...

---

<!--: .wrap -->

## Values

### `nonce-<base64 value>`

Specifies a nonce that can be included in element declarations, indicating that this particular
entry has been allowed. This can be used in conjunction with `'unsafe-inline'` to explicitly declare
which inline entries are allowed.

```http request
Content-Security-Policy: default-src 'self' nonce-rAnd0m;
```

```html

<script nonce="rAnd0m">
  console.log("This script will execute...");
</script>

<script>
  console.log("...but this one will not.");
</script>
```

**NOTE:** This can only be used with elements that have a `nonce` attribute. For example, `img`
does not have a `nonce` attribute, so `nonce` cannot be used as a value for `img-src` directives.

---

<!--: .wrap -->

## Values

### `<hash algorithm>-<base64 value>`

Specifies a hash matching the hash value of a given element (using the specified algorithm, and
excluding the element -- e.g. `script` or `style` -- tags). Whitespace is important. Generally not
advisable as this one can be quite brittle and requires recalculation if the element's value changes
at all.

In CSP 2.0, this is applied only to inline scripts. CSP 3.0 allows it in the case of `script-src`
for external scripts.

---

<!--: .wrap -->

## Values

### `'strict-dynamic'` (CSP 3.0 only)

This specifies that the trust explicitly given to a script present in the markup, by accompanying it
with a nonce or a hash, shall be propagated to all the scripts loaded by that root script. At the
same time, any allow-list or source expressions such as `'self'` or `'unsafe-inline'`
are ignored.

Example: this would allow any scripts loaded via GTM (or Ensighten) to be trusted automatically, as
long as the original script was allowed (using a nonce).

---

<!--: .wrap -->

## How do I test it, though?

CSP has the ability to very quickly break a site if something is missed. To accommodate this, CSP
includes an alternate header, `Content-Security-Policy-Report-Only`. If this is included in place of
the standard CSP header (not alongside!), instead of blocking the resource, it will POST a JSON
object to a defined `report-uri` specified in the directive.

The JSON payload is quite comprehensive and provides details on which directive has been violated as
well as the source URI, which makes it easy to re-work your CSP to add the required values to the
necessary directive(s).

---

<!-- : .wrap ..aligncenter -->

## **Any Questions?**