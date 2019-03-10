---
layout: post
author: hm
---

To keep a web page out of Google, use noindex. 
2 ways to implement noindex:

1. < meta > tag on html
<br> Inside the <head> tag,
< meta name="robots" content="noindex" >
<br> Or to prevent only google web crawlers, use name="googlebot"

2. HTTP response header
<br> X-Robots-Tag: noindex or none

Robots.txt
- tell web crawlers not to crawl and index your page
- but if found from a link from other places on the web, it'll still be able to appear on google search.