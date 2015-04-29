---
layout: post
title:  "Referer Suppressor for external link"
date:   2015-04-25 00:00:00
categories: article
published: true
script: [/js/jquery-1.11.2.min.js, /js/meta-redirect.js]
---

"Referer Suppressor" which eliminate the browser's referer is one of my 
favorite feature of [IP Geo Block][IP-Geo-Block] <span class="emoji">
![emoji](https://assets-cdn.github.com/images/icons/emoji/unicode/1f604.png)
</span>.

It came to this plugin as a logical consequence of WP-ZEP. In this article, 
I'll tell you the story.

<!--more-->

### A possibility of nonce disclosure ###

A nonce is a secret information which can be known only by the user who 
accesses a certain page at a certain moment. It's one of basic and important 
factor to prevent <abbr title="Cross Site Request Forgeries">CSRF</abbr> or 
other vulnerability.

Instead of vulnerable plugins, WP-ZEP embed a nonce into hyperlinks and forms 
that have requests to somewhere in the admin area. To keep it secret, WP-ZEP 
must kill the possibility of disclosing nonce.

One possibility lies in referer strings that was left on the external page as 
a footprint you visited via hyperlinks.

That's why "Referer Suppressor" is needed.

### How to suppress a referer? ###

When a click event is triggered on a hyperlink which have an anchor to the 
external url, this plugin opens a new document to redirect to that url with 
some extra meta tags.

"[Meta refresh][meta-refresh]" is an old school which is not a part of HTTP 
standard, but every browser redirects to the specified url.

```html
<meta http-equiv="refresh" content="0; url=http://example.com/">
```

On a page including this tag, [IE or Firefox does not send the referer to the 
redirected url, but Chrome, Safari or Opera does][stackoverflow]. So we need 
a new school, i.e. "[Meta referrer][meta-referrer]":

```html
<meta name="referrer" content="no-referrer">
```

or

```html
<a href="http://example.com" referrer="no-referrer">
```

Then the final solution bocomes as follows.

```html
<meta name="referrer" content="never" />
<meta name="referrer" content="no-referrer" />
<meta http-equiv="refresh" content="0; url=http://example.com/" />
```

You can find this in [auth-nonce.js][auth-nonce-js].

#### Note ####

The keywords `never`, `default`, `always` are [obsolete][WHATWG-Wiki].

### Samples ###

- <a href="{{ "/etc/referer.html" | prepend: site.baseurl }}" target="_blank">Simple link</a>
- <a href="{{ "/etc/referer.html" | prepend: site.baseurl }}" data-meta-referrer="false">Meta refresh</a>
- <a href="{{ "/etc/referer.html" | prepend: site.baseurl }}" data-meta-referrer="true">Meta refresh + Meta referrer</a>

[IP-Geo-Block]:  https://wordpress.org/plugins/ip-geo-block/ "WordPress › IP Geo Block « WordPress Plugins"
[meta-refresh]:  http://en.wikipedia.org/wiki/Meta_refresh "Meta refresh - Wikipedia, the free encyclopedia"
[meta-referrer]: http://w3c.github.io/webappsec/specs/referrer-policy/#referrer-policy-delivery-meta "Referrer Policy - W3C Editor's Draft"
[WHATWG-Wiki]:   https://wiki.whatwg.org/wiki/Meta_referrer "Meta referrer - WHATWG Wiki"
[auth-nonce-js]: https://github.com/tokkonopapa/WordPress-IP-Geo-Block/blob/master/ip-geo-block/admin/js/auth-nonce.js "WordPress-IP-Geo-Block/auth-nonce.js at master - tokkonopapa/WordPress-IP-Geo-Block - GitHub"
[stackoverflow]: http://stackoverflow.com/questions/2985579/does-http-equiv-refresh-keep-referrer-info-and-metadata "html - Does http-equiv=&quot;refresh&quot; keep referrer info and metadata? - Stack Overflow"
[coderwall]:     https://coderwall.com/p/7a09ja/no-referer-after-redirect-solved "No referer after redirect (Solved)"
[sample-link]:   http://tokkono.cute.coocan.jp/demo/libs/referer.php