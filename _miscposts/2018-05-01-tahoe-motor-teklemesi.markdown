---
layout: post
title:  "Tahoe Motor Teklemesi"
date:   2018-05-01 00:40:00
alternate_banner: True
banner: "/post-assets/layout/vortec-engine-banner.jpg"
banner_attribution: "Vortec L31 5.7 Engine by hvcompton"
banner_source: "http://sbcv8.com/vortec-engine-rebuild/"

---

Bu sayfada da Chevrolet Tahoe motor teklemesi probleminden bahsedeceğim.

<!--more-->

**Bu araçlarla ilgili edindiğim bilgilerden topladığım yazıların tamamı şurada:**

[Tahoe 96-98 Alacaklar veya Sahipleri İçin Bilgi ve İlk Yapılacaklar](/miscposts/2018-04-19-tahoe-sahipleri-icin-bilgi.html) 

### Motor teklemesi

Eğer aracınızda P0300, P0301, ... P0308 hatalarından birisi varsa, bu silindirlerde bir problem olduğunu gösterir.
P0300 rasgele misfire kodu. Yani hangi silindir olduğunu söylemiyor. P0301 1. silindir misfire kodu. Böyle gidiyor.

Motor teklemesi denilen şey İngilizce literatürde "cylinder misfire" denilen problem.
Cylinder misfire, silindirin o seferde ateşlememesi demek. Yani verilen hava ve yakıt yanmamış ve direk egzozdan atılmış.
Bu yakıt tüketimini ve aracın performansını çok etkileyecek bir durum.

100 tane sebebi olabilir:
* Buji kabloları
* Oksijen sensörleri
* CMP sensörü
* LPG ayarı
* Emme manifoltundan hava sızıntısı
* Motor zamanlama ayarı
* Silindir kapağı (sibop yakma veya subap yakma denen olay)
* ...

Bir tamirhaneye gittiğimde direk misfire sayısına bakarak silindir kapağının değişmesi gerektiğini söylediler. Dedikleri belki de 
doğru, ama 5000 - 7000 TL masrafı direk yapacak halim yok. O yüzden kendim bazı şeyleri denemeye karar verdim.

Ben aracı aldığımdan beri hep LPG ile kullanıyordum ve ilk olarak LPG'yi elemek istedim.
Arıza kodlarını silip benzinle sürünce bu P030X hata kodlarını bir daha hiç görmedim.

Bu iyi bir haber. Ama hata kodunun olmaması, misfire olmadığını göstermiyor. Hata kodu, misfire belli bir seviyeye ulaşınca 
veriliyor. Krank milinin dakikadaki dönme sayısı %2 ile %10 arasında artış veya azalış gösterdiğinde bu hata veriliyor ve hata lambası yanıyor.
Eğer fark %10'dan fazlaysa hata lambası yanıp sönüyor. Bende LPG'de lamba sabit idi, yani %2 - %10 arası.   
 
Dolayısıyla, hata lambası yanmasa da silindir misfire sayaçlarını incelemek gerektiğini düşündüm. 

İlk yapılacak şey, DashCommand uygulamasından Cylinder Misfire ile ilgili toplam 10 veriye bakmak 
(bkz. [Tahoe 96-98 Alacaklar veya Sahipleri İçin Bilgi ve İlk Yapılacaklar](/miscposts/2018-04-19-tahoe-sahipleri-icin-bilgi.html)).
8'i her silindir için teker teker misfire bilgisi, biri tüm silindirler misfire bilgisi, biri de 
misfire yüzünden hata kayıt bilgisi.
GM'in Tech 2 cihazında 1000 ateşlemede kaç misfire olduğunu gösteren güzel bir grafik var ve servisler buna bakıyor.
DashCommand ile bu şekilde toplayarak gitmek mümkün değil, ama grafik görünümünde son birkaç saniyeyi görüp kendiniz
toplayabilirsiniz.

Benim aracımda benzinde sürerken çok nadiren, alakasız silindirlerde 1-2 defa üstüste misfire oluyor.
Sanırım gözardı edilebilecek kadar küçük miktarda.

Ancak, yine de motorun zamanlama ayarından ve vakumda sızıntıdan şüpheleniyorum. Bunlara daha sonra bakacağım 
ve haklarında yazı yazacağım.

### Literatür

* Cylinder misfire: Silindirin ateşlememesi
* Crankshaft: Krank mili
* Camshaft: Kam mili, eksantrik mili
* Ignition coil: Ateşleme bobini