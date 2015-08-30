---
layout: post
title:  "How to mess up Java"
date:   2015-08-30 13:00:00
banner: "/post-assets/layout/personal-notes-banner.jpg"
alternate_banner: True
banner_attribution: "*Bloc de nots* - Photo by AlbertFM0"
banner_source: "http://commons.wikimedia.org/wiki/File:Bloc_de_nots.jpg"

---

Yesterday I've seen another example of Oracle messing up Java and Java standards. This time, it was [Tyrus](https://tyrus.java.net/).
<!--more-->

In a recent project of mine I needed to use Websockets. To experiment with things about latency on Openshift, I created
a [small application](https://github.com/aliok/packet-latency-experiments) where server echoes what it receives and client computes the latency.

What can go wrong, right? I know Openshift [supports](https://blog.openshift.com/paas-websockets/) websockets. All I have to do is
bind the host and port that I receive.

Well, it didn't work. I was always receiving `java.net.SocketException: Permission denied`. I thought I misunderstood the
DIY cartridge's [environment variables](https://developers.openshift.com/en/diy-overview.html#_environment_variables).

So, I gave [running Wildfly server](https://github.com/shekhargulati/wildfly-openshift-quickstart) with DIY cartridge a try.
It worked!

I tried the same host, port, context path with my app. No way!

Then I thought maybe there is something wrong with the Tyrus RI. And, yes, there is!
 
See [this fucking awful piece of crap](https://github.com/tyrus-project/tyrus/blob/432078ae0068f57bb60c715564f8f65959fe2d6a/server/src/main/java/org/glassfish/tyrus/server/Server.java): **Hostname is not passed to server factory!**

Well, even if it would be passed, there is no hostname parameter in this piece of shit: [GrizzlyServerContainer](https://github.com/tyrus-project/tyrus/blob/432078ae0068f57bb60c715564f8f65959fe2d6a/containers/grizzly-server/src/main/java/org/glassfish/tyrus/container/grizzly/server/GrizzlyServerContainer.java#L172).

It is always using the host `0.0.0.0`.

Well, it explains why I was never successful with deploying to Openshift. I had to bind to IP given by Openshift, but hostname parameter I pass was never used in Tyrus!

~~~~~~


Well, good old days are behind where a reference implementation of a JSR actually works and good for POCs even though it is not the best choice for production usages.

Shame on you Oracle for fucking up Java! 


Have I reported the problem? No I haven't. I won't even bother reporting to this horrible project.
 
I am gonna migrate to [Undertow](http://undertow.io/) from Jboss. At least we still have Jboss.