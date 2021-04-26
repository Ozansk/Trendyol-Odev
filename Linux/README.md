
ÖDEVİN KONUSU:

Boyutu 1GB'den büyük olan dosyaları bulmak ve bu dosyaları taşımak. Bulmak için öncelikle find komutu kullanmalıyız:

`sudo find / -type f -size +1G` // -type ile birlikte aradığımızın bir dosya olduğunu belirttik. -size ile birlikte boyutunu belirttik.

Dosyayı isterken isim şeklinde de arama yaparak bulabiliriz. Bunu yapmak içinse '-name' kullanmalıyız:

`sudo find / -type f -name "*.txt" -size +1G` // -name ile birlikte txt olan dosyaların hepsini bulmasını sağladık.

Dosyanın ne kadar zamandır sistemde yer aldığına göre de bu işlemi gerçekleştirebiliyoruz:

`sudo find / -mtime +150 -size +1G` // -mtime +150 ile aradığımız dosyanın 150 günden beri sistemde olması gerektiğini belirtiyoruz.

Eğer bulduğumuz dosyayı belli bir konuma taşımak istersek bu komutu kullanabiliriz:

`sudo find / -type f -size +1G | xargs mv -t /tmp` // Öncelikle aradığımız dosya konumunu alıyoruz ve taşımamız gereken dizini belirtiyoruz.
