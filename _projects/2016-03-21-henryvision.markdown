---
layout: post
title:  "Henryvision"
date:   2015-10-20 12:00:00
thumbnail: "/post-assets/layout/henryvision-thumb.jpg"
banner: "/post-assets/layout/henryvision-banner.jpg"
alternate_banner: True
banner_attribution: "*Clear Vision* - Picture by C.P.Storm"
banner_source: "https://www.flickr.com/photos/cpstorm/167418602"

---

A demo with Red Hat Mobile Application Platform, Google Cloud Vision API.
You take a photo and the mobile application tells you what it is.
<!--more-->

### Status

Here is a short demo:

<iframe width="420" height="315" src="https://www.youtube.com/embed/npUtwnmuKEQ" frameborder="0" allowfullscreen></iframe>

Source code is available on Github : <https://github.com/aliok/henryvision>

### Overall architecture

I implemented this demo to learn more about Red Hat Mobile Application Platform.

This demo is on Red Hat Mobile Application Platform (RHMAP).

There are 4 components. Architecture can be considered as a microservice architecture.
There are not 100s of services though :)

#### MBaaS services:

These are the microservices. They provide integration with other systems.

- vision-service: Integrates with [Google Cloud Vision](https://cloud.google.com/vision/).
                  Returns the label suggestions for the received base64 encoded image.

- wordnet-service: Integration with [WordNet](https://wordnet.princeton.edu/wordnet/). Does scraping.
                   I hope they're fine with it since this is a small demo.
                   Returns the dictionary definition of a word.

Both of these applications are Node apps.

#### Cloud app:

This is the backend that mobile application uses. Uses the MBaaS services described above.
Gets the suggestions from *vision-service* for received base64 encoded image and generates
a natural language sentence based on the score of the suggestions. *wordnet-service* is used
to include a definition in that sentence.

This is also a Node application.

#### Mobile app:

This is an Ionic/Cordova app. Uses the cloud app described above.

- User takes a picture
- App sends it to backend and gets a sentence and suggestions
- App displays the sentence and the suggestions
- App speaks the sentence


### Conclusion

It was pretty easy to do all these things. RHMAP didn't make my day harder at all.
It is a very good integration what they provide.

If you would like to give it a try, here is a link for you:

<http://red.ht/25fr7sx>

OR

![qrCode](/post-assets/henryvision-qrcode.png)

(only Android!)