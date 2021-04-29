
ÖDEVİN KONUSU VE TARİHİ: Docker Volume ve Mount yapısı.(11/04/2021)

1. Öncelikle bir volume oluşturmak için bu komutu kullanmalıyız:

`docker volume create temp` // temp adında bir volume oluşturuldu.

2. Volume oluşturulduğunda sanal makinemizde bu dizinde görüntüleyebiliriz:

`/var/lib/docker/volume/`

3. Oluşturulan bütün volume'ları listelemek için aşağıdaki komutu kullanabiliriz:

`docker volume ls` // komutla beraber hangi volume'lar olduğunu ismiyle beraber görüntüleniyor.

4. 'inspect' komutuyla bir volume hakkında ne zaman oluşturulduğu, hangi driver'd yer aldığı, konumu gibi birçok seçenek hakkında bilgi sahibi olunuyor:

`docker volume inspect temp` // temp volume hakkında bilgilere ulaşıldı.

5. Bir container'ı ayaağa kaldırmak ve oluşturmuş olduğumuz volume'u buraya mount etmek için aşağıdaki komutu kullanıyoruz:

`docker container run -it -v data:/temp centos /bin/bash`
