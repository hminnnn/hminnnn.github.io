---
layout: post
author: HM
---
My first time seeing a random security bug report :')

## Security concerns with iframes/embedding

- [Clickjacking]("https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Other_embedding_technologies") - hackers can embed your document onto their malicious website, or the other way round, to capture user's interactions, allowing them to steal sensitive info.

## Content Security Policy (CSP)

CSP is a set of HTTP headers to improve security.

To prevent page from being embedded inside another page e.g. in an iframe, we can add a Content-Security-Policy header and set frame-ancestors to be none.

### frame-ancestors

If it's not included the header, it means "*" and the resource can be placed in a frame by others.

For nginx, we place this inside the location tags

`Content-Security-Policy "frame-ancestors none;"`

```
location / {
	try_files $uri $uri/ /index.html;
	add_header Content-Security-Policy "frame-ancestors none;";
}
```

Not sure how much to trust but after placing the header and restarting nginx, my site now says 'refused to connect' on [this test page]("https://www.lookout.net/test/clickjack.html").

### frame-ancestors vs frame-src

frame-src prevents your page from framing another. frame-ancestors prevent another page from framing yours.

If /test1 had this  
`frame-src 'none';frame-ancestors 'self'`

And /test2 had this  
`frame-src 'self';frame-ancestors 'none'`

/test1 can be framed by /test2, but /test2 cannot be framed by /test1 in an iframe tag due to `frame-ancestors 'none'` .

/test1 also cannot put /test2 in an iframe due to `frame-src 'none'` and `frame-ancestors 'none'`

## X-frame-options

The frame-ancestors directive obsoletes X-frame-options. X-Frame-Options header field will be replaced by CSP frame options directives.....?

This HTTP header field is to be declared in the HTTP header of the response that the server sends to the client(browser)

- indicates whether a browser should render this resource in a frame.

Options:

- X-Frame-Options: DENY
- X-Frame-Options: SAMEORIGIN
- X-Frame-Options: ALLOW-FROM <https://example.com/>