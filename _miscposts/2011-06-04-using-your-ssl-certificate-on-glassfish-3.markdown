---
layout: post
title:  "Using your SSL certificate on Glassfish 3"
date:   2011-06-04 01:07:43
banner: "/post-assets/layout//using-your-ssl-certificate-on-glassfish-banner.jpg"
banner_attribution: "Picture by Alexander Vasenin"
banner_source: "http://commons.wikimedia.org/wiki/File:Red_lionfish_near_Gilli_Banta_Island.JPG"

---

This was kind of problematic for me and my colleagues, so after I manage to set our SSL certificate, I decided to share the story.
<!--more-->

Before doing anything, back up the files under `$GFHOME/domains/yourdomain/config`.

Steps are:

1. Generating a private key
2. Creating a Certification Signing Request (CSR)
3. Sending the CSR to a certificate authority
4. Importing your signed certificate
5. Setting necessary Glassfish configuration

First, we let’s create our private key. You can directly work on the existing keystore shipped with Glassfish, 
or you can create a new keystore and then import it to Glassfish’s keystore. To be more safe, I created a new 
keystore and generated the private key on that:

{% highlight bash %}
cd ~
mkdir ssl-stuff
cd ssl-stuff
keytool -keysize 2048 -genkey -alias yourdomain.com -keyalg RSA -dname "CN=www.yourdomain.com,O=Your Organization,L=Your City,S=Your State,C=Your Country Code like US, CH or TR" -keystore keystore.jks -keypass changeit -storepass changeit
{% endhighlight %}

Now our private key is generated in keystore.jks file. I used keypass ‘changeit’ as key password and keystore password for now.

{% highlight bash %}
keytool -certreq -alias yourdomain.com -keystore keystore.jks -storepass changeit -keypass changeit -file server.csr
{% endhighlight %}

This results in a certificate signing request, server.csr. Now we have two files :
 
* keystore.jks
* server.csr

Next step is uploading server.csr file to one of the certificate authorities (I used Godaddy and I was pretty happy with their service) and receiving signed 
certificate. Certificate authorities looks for the “whois” record of your domain and if they find a technical representative email address, they send the 
signed certificate to that email. If there is no email address (like one of the domains I set the SSL up), they give you a code (like abcd123) and require 
you to either add a subdomain with that code or add a html page named with the code (and also with the content filled with the code).

From certificate authority, you should generally receive two files

* Your signed certificate : generally named as “yourdomain.com.crt”
* Root certificate of your certificate (you might receive more than one. In some cases certification authority sends the intermediate certificates) : Godaddy names it “gd_bundle.crt”

Next step is importing the keystore I used into the Glassfish’s keystore:
{% highlight bash %}
keytool -importkeystore -srckeystore ~/ssl-stuff/keystore.jks -destkeystore $GLASSFISHHOME/domains/yourdomain/config/keystore.jks
{% endhighlight %}

Now, the final step done on keystore is importing the signed certificate. First, we gotta switch to Glassfish’s keystore and import the root certificate:
{% highlight bash %}
cd $GLASSFISH_HOME/domains/yourdomain/config
keytool -import -v -trustcacerts -alias root -file gd_bundle.crt -keystore keystore.jks -keypass changeit -storepass changeit
{% endhighlight %}

Then, import the signed certificate of yours:
{% highlight bash %}
keytool -import -v -trustcacerts -alias yourdomain.com -file yourdomain.com.crt -keystore keystore.jks -keypass changeit -storepass changeit
{% endhighlight %}

Now we’re done with the keystore. You should have something like this:
{% highlight bash %}
keytool -list -keystore keystore.jks
 
Keystore-Typ: JKS
Keystore-Provider: SUN
 
....
 
root, 03.06.2011, trustedCertEntry,
Zertifikatsfingerabdruck (MD5): XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX
yourdomain.com, 03.06.2011, PrivateKeyEntry,
Zertifikatsfingerabdruck (MD5): XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX
{% endhighlight %}

Final step of the whole process is using our domain alias as the “certificate nickname” on GlassFish:
![Screenshot](/post-assets/using-your-ssl-certificate-on-glassfish-screenshot0.png)
