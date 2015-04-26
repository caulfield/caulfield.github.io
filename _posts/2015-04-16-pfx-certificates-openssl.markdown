---
layout: post
title: "How to install pfx certificate on linux server with nginx"
date: 2015-04-17 17:38:00
categories: openssl, nginx
---

There was some issue to setup ssl on servers using pfx file for mer. At first, pfx format(PKCS #12) historically were used on Microsoft Servers. Pfx files include both private key and the certificate.
First you will need is to parse pfx file using ssl and extract them

I used [pkcs12][pkcs12] utility.


This command exports private key from server.name.pfx to server.name.rsa:

`openssl pkcs12 -in server.name.pfx -nocerts -out server.name.rsa -nodes`

And this exports certificate:

`openssl pkcs12 -in server.name.pfx -nokeys -out serve.name.crt`

Note: Both commands may ask password.

When you have got these 3(with original pfx) files it's easy to setup nginx

{% highlight ruby %}

  server {
    listen  443;
    ssl on;

    ssl_certificate      /path/to/server.name.crt;
    ssl_certificate_key  /path/to/server.name.rsa;
  }

{% endhighlight %}

Reload config and it's done.

[pkcs12]: https://www.openssl.org/docs/apps/pkcs12.html
