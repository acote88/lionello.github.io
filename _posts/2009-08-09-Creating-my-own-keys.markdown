---
layout: post
title: Creating my own keys
categories: []
permalink: /archives/80-Creating-my-own-keys.html
s9y_link: http://www.lunesu.com/index.php?/archives/80-Creating-my-own-keys.html
date: 2009-08-09 22:16:23.000000000 +08:00
---
Until now I've relied upon the private keys that were already installed when I got my VPS. This, of course, is insecure since they might have been compromised, meaning, nobody knows where that private key came from.

So I decided it was time to create some keys of my own. Here's the procedure. First, let's create some <em>private</em> space in our home folder for extra security:
{% highlight sh %}
cd ~
mkdir private
chmod 600 private
cd private
{% endhighlight %}
Next, create a secure private key, make sure you give it a good strong pass phrase and don't forget it:
{% highlight sh %}
openssl genrsa -des3 -out server.key.secure 1024
{% endhighlight %}
Now we derive a key from the secure key, but this time we don't use a pass phrase:
{% highlight sh %}
openssl rsa -in server.key.secure -out server.key
{% endhighlight %}
The next step we know: create a Certificate Signing Request. <a href="http://www.cacert.org/" title="CACert">CACert.org</a> only cares about the <em>CommonName </em>so the other fields can be ignored. When asked to enter [YOUR name] you enter the name of your host, ie. <em>example.com</em>:
{% highlight sh %}
openssl req -new -key server.key -out server.csr
cat server.csr
{% endhighlight %}
As before, copy paste this server.csr into cacert.org's form, wait, and copy-paste the result from cacert into a new file called <em>server.pem</em>. I've moved the (insecure) <em>server.key</em> and the final <em>server.pem</em> into <em>etc/ssl/private/</em> and <em>etc/ssl/certs/</em> respectively. <strong>Make sure only root has read access for server.key!</strong>

Last step: reconfigure Webmin, Apache and Dovecot to use the new key and certificate. And <a href="http://www.lunesu.com/archives/83-Mail-troubles.html" title="Mail troubles">Postfix</a>.
