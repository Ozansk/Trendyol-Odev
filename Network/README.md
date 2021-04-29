
ÖDEVİN KONUSU VE TARİHİ: Verilen network sorunlarını çözmek.(18/04/2021)

S1 = PC0 makinemizin IP'si yok. Bu yüzden öncelikle DHCP deniyoruz. Fakat başarısız oluyor. Bu yüzden DHCP sunucumuzu açıyoruz. Dah sonra PC0 makinemizden www.falanca.com'un IP'sine ping atıyoruz ve sorunun DNS'den kaynaklı olduğunu görüyoruz.

S2, S6, S9 = Belli bir problem yok.

S3 = PC0 makinemizin IP'si yok. Bu yüzden öncelikle DHCP deniyoruz. Fakat başarısız oluyor. DHCP sunucumuzun servisler kısmında default gateway 192.168.1.1 olarak ayarlanmalıdır. Start IP adresi son hanesi için belli bir sayı seçilebilir(10 verebiliriz). Böylece çözüme ulaşıyoruz.

S4, S5, S7, S8 = Öncelikle bütün bilgisayarlarda DHCP için bir sorunumuz olmadığı gözlemlendi. Her bilgisayar ip'sini aldıktan sonra PC0 ve PC1 arasında ping atıldı ve switch 1'de sorun olmadığı gözlemlendi. PC2'nin switch'inde de aynısı denendi ve problem olmdığı gözlendi. Fkat PC0 ve PC2 arasında ping olmadı. Bu yüzden sorunların hepsinin router'dan kaynaklı olduğu gözlemlenmiş oldu. 

S10 = Öncelikle bilgisayarlar arası ping atarak switchlerde ya da router'da problem var mı diye bakıldı. Fakat sorun olmadığı görüldü. Bu yüzden sorunun switch ile DHCP sunucusu arasında olduğu görüldü ve kablodan kynaklı problem olduğu çözüldü.
