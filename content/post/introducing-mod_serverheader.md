---
title: 'Introducing mod_serverheader'
date: Sun, 19 Mar 2017 03:20:52 +0000
draft: false
tags: [apache, httpd, Project, Uncategorized]
---

[mod_serverheader project site](https://github.com/bostrt/mod_serverheader)In my work, I have seen people performing security scans, going through audits, and stumbling upon information they wish they could hide about their webservers. One of the simplest questions that comes up over-and-over is regarding the Server header in Apache HTTPD responses; the thing with the arrows are pointing to below:

    # curl -I http://localhost/
    HTTP/1.1 200 OK
    Date: Tue, 28 Feb 2017 20:56:24 EST
    Server: Apache/2.4.25 (Fedora) OpenSSL/1.0.2k-fips  ◀◀◀◀◀◀◀◀
    Content-Type: text/html; charset=UTF-8
    

There are a couple ways to reduce what is disclosed in the Server header.

1.  Configure your [`ServerTokens`](https://httpd.apache.org/docs/current/mod/core.html#servertokens) directive to be `ProductOnly`.
2.  Use ModSecurity’s [`SecServerSignature`](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual#SecServerSignature) directive to overwrite the Server header with custom contents.

Each of the options are backed by opposing opinions. The Apache HTTPD documentation claims there is no advantage to hiding/altering the contents of the Server header because a malicious user could just throw all their attacks at a webserver, no matter what the webserver product actual is.

> Setting ServerTokens to less than minimal is not recommended because it makes it more difficult to debug interoperational problems. Also note that disabling the Server: header does nothing at all to make your server more secure. The idea of “security through obscurity” is a myth and leads to a false sense of safety. [1](https://httpd.apache.org/docs/current/mod/core.html#servertokens)

And, as far as I know, the motivation behind ModSecurity’s `SecServerSignature`directive is the because of a not in the [HTTP/1.1 RFC 2068](https://www.ietf.org/rfc/rfc2068.txt):

> Revealing the specific software version of the server may allow the server machine to become more vulnerable to attacks against software that is known to contain security holes. Implementers SHOULD make the Server header field a configurable option. [2](https://www.ietf.org/rfc/rfc2068.txt)

I’m not going to take sides in the matter, becaues I think both points have merits. However, I think our battles must be chosen carefully. Should time be spent deciding whether or not to obfuscate your Server header or should time be spent on more significant security matters? Why not satisfy this simple concern to satisfy the request of security professionals or security auditors? ModSecurity has solved this issue with the `SecServerSignature` directive. However, I’ve seen people use ModSecurity _only_ for this header. I think the module is completely overkill to use just for this one directive. So, I worked up a simple module who’s only purpose is to overwrite the Server header: [https://github.com/bostrt/mod_serverheader](https://github.com/bostrt/mod_serverheader)

1.  [https://httpd.apache.org/docs/current/mod/core.html#servertokens](https://httpd.apache.org/docs/current/mod/core.html#servertokens)
2.  [https://www.ietf.org/rfc/rfc2068.txt](https://www.ietf.org/rfc/rfc2068.txt)