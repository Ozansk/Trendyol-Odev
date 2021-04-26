1. Case

- 1.1: Öncelikle bir sanal makine kuruluyor(Virtualbox, VMware vs.). Daha sonra CentOS'un sitesinden uygun olan iso dosyası imdiriliyor. İndirmiş olunan iso dosyası herhangi bir sanal makinede Controller:IDE kısmına ekleniyor. Kurulan CentOS makinesi başlatılıyor ve gelen indirme özeti gerekli özellikler seçiliyor. Son geçiş aşamasında root ve user için gerekli isim ve şifreler belirleniyor. Makine kullanıma hazır hale geliyor. Terminal üzerinde vereceğimiz iki komut ile beraber CentOS güncel hale getiriliyor:

`yum install update -y` // -y güncelleme sırasında gelen sorulara direk yes yanıtı vermemizi sağlıyor.

`yum -y update`

Bu komutlardan sonra makineye reboot atılmalıdır. Çünkü sanal makinemiz kernel üzerinde de güncelleme yapabilir.


- 1.2: Öncelikle "sudo su -l" komutuyla root kullanıcısına geçiliyor.

`sudo useradd ozan.sk` // Bu komut ile birlikte ozan.sk adında bir kullanıcı oluşturuluyor.

`sudo passwd ozan.sk` // Bu komut ile birlikte oluşturulan kullanıcı için şifre atadık.

Az sonra gerçekleştireceğimiz işlemler bu kulllanıcı üzerinden olacak ve yapacağımız işlemler için sudo yetkisine ihtiyacımız olacak. Bu yüzden "ozan.sk" adlı kullanıcıya sudo izni vermek için sudoers dosyasında değişiklik yapılmalıdır.

`sudo vi /etc/sudoers` // bu komutla beraber belirtilen dosyaya ulaşıyoruz ve düzenleme yapabiliyoruz.

<img src="/Nisan19/sudo_yetkisi.png" alt="Sudo yetkisi"/>

- 1.3: Öncelikle `sudo fdisk -l` komutuyla şu anda bulunan disk alanlarına ulaşılıyor. Daha sonra parça alacağımız diskin dizini alınıyor. Örneğin `sudo fdisk /dev/xvdb` komutuyla bu diskin dizinine ulaşıyoruz. Daha sonra karşımıza gelen ekranda yardım için `m` tuşuna basılıyor. Gelen sayfada bu disk üzerinde yeni bir parça açmak için(add partition) `n` tuşuna basılıyor. Daha sonra gelen iki seçenek arasından `p` tuşuna basarak primary partition olduğunu belirtiyoruz. Burada gelen sorular içinde partition number olarak 1 verebiliriz(1-4 aralığında seçim yapılabiliyor.). Partition boyutuna 10GB vermek için +10G yazılıyor. Bu işlem tamamlandığında artık elimizde "/dev/xvdb1" dizini olarak 10GB'lik alan ayrılıyor. Bu alanı oluşturmak için:

`sudo mkfs.ext3 -b /dev/xvdb` // Artık alanı elde ettik.

Oluşturulan alanı mount etmek için öncelikle fstab dosyasında değişiklik yapılmalıdır. Bu yüzden aşağıdaki komutu uygulanıyor:

`sudo vi /etc/fstab`

Bu sayfada yeni bir satır oluşturarak alınan partition kısmını, nereye mount edeceğimizi, türünü ve yetkilerini giriyoruz.(/dev/xvdb, /bootcamp, ext3, defaults, 1 1).

bootcamp dizinini oluşturmak için aşağıdaki komut kullanılıyor.

`sudo mkdir -p /bootcamp`

En sonda ise artık oluşturduğumuz alanı mount komutuyla diske ekleniyor:

`mount /dev/xvdb /bootcamp`

- 1.4: Öncelikle belirtilen dizine gidiyoruz:

`cd /opt/bootcamp`

Daha sonra burada bootcamp adında bir file oluşturuyoruz:

`sudo touch bootcamp.txt`

Belirtilen dosyada yazı yazabilmek için dosyaya gerekli yetki sağlanmalıdır. Bu yüzden dosya için '666' yetkisi verilebilir:

`sudo chmod 777 bootcamp.txt` // rw_rw_rw_ yazma yetkisi sağlandı.

Oluşturulan file içine yazıyı 'echo' komutuyla eklenebilir:

`sudo echo 'merhaba trendyol' > bootcamp.txt` // Yazı dosyaya eklenmiş oldu.

- 1.5: Öncelikle home dizinindeyken belirtilen dosyayı bulmak için 'find' komutu kullanılabilir:

`sudo find / -type f -name 'bootcamp.txt' | xargs mv -t /bootcamp` // Burada arananın bir file olduğunu belirtmek için '-type', ismini belirtmek için '-name' kullanıldı. Daha sonra bulunan hedef konumu  mv -t komutu ile belirtildi.


2. Case

- 2.1: Öncelikle hangi makine üzerinde bu işlemin gerçekleştirileceğini belirtmek için hosts.yml dosyamıza gerekli hostlar giriliyor:
```
---

all:
    dev-server:
        hosts: ansible.host=192.168.135.111 // bir server ip'si verilmelidir.
        vars:
            nginx_port: 80 // nginx'in hangi portta bize yenıt vereceği belirtiliyor.

```

hosts.yml dosyası yazıldıktan sonra nginx'e ait kurulumu makinemize yapmalıyız. Bu yüzden bir dockerfile(.nginx/dockerfile path'i üzerinde) oluşturuluyor:

```
FROM ubuntu: 18.04 // makinemizin adı ve sürümü değişkenlik gösterebilir.

RUN apt-get update \
    && apt-get install -y nginx \   // nginx kurumu gerçekleştiriliyor.
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && echo "daemon off;" >> /etc/nginx/nginx.conf

ADD default /etc/nginx/sites-available/default


FROM nginx:latest
COPY --from=build /usr/local/app/dist/app /usr/share/nginx/html // Uygulamaya ait gerekli konumun nginx'e kopyalanması 

EXPOSE 80 // çalışacağı portu belirtiyoruz.
CMD ["nginx"] // docker üzerindeki image adı olacak.

```
     
Son olarak bir ansible playbook(file ismi docker.yml) ile hem docker kurulumu yapılıyor. Hem de nginx için bir container oluşturularak çalışır hale getiriliyor:

```
---

- hosts: dev-server // hosts.yml dosyasında belirtmiş olduğumuz variable'lar ve gerekli server ip'si alındı.
  gather_facts: no
  remote_user: ubuntu // makine duruma bağlı değişebilir.
  become: yes

  tasks:
    - name: Install Docker // İlk task olarak docker ve python3 indirmesi gerçekleşiyor.
      apt:
        name: python3-pip, docker.io
        state: present

    - name: Start Docker service // Docker kurulumu sonrası servis başlatılıyor(komut satırında systemctl kullanılıyor.).
      service:
        name: docker
        state: started

    - name: Install docker python module // Docker için gerekli python modülü indiriliyor.
      pip:
        name: docker

    - name: Create an image and push nginx // Bir image oluşturularak nginx'e ait docker file buraya yerleştiriliyor.
      docker_image:
        build:
            path: ./nginx
        name: location/nginx
        tag: v1
        force_source: yes
        push: yes
        source: build
      become: no 
    
    - name: Create nginx container // nginx için container oluşturuluyor ve çalışacağı prot burada belirtiliyor.
      docker_container:
        name: nginx
        image: location/nginx:v1
        state: started
        recreate: yes
        published_ports:
          - "{{ nginx_port }}:{{ nginx_port }}"

```

Son olarak hazırlanmış olan docker.yml dosyası ansible-playbook komutuyla çalıştırılıyor ve belirtilen ip'de 80 portunda uygulama çalışır hale geliyor:

`ansible-playbook -i hosts docker.yml`


- 2.2: Öncelikle bir 'playbook.yml' dosyası oluşturuluyor. Burada nginx kurulumu gerçekleşitiriliyor:

```
- hosts: all
  remote_user: ubuntu
  vars:
    http_port: 80
    document_root: /var/...    // server'a ait dökümanın bulunduğu yer
    app_root: “Hosgeldin_DevOps  // Statik sayfanın bulınduğu dosya
  
  tasks:
    - name: Update apt cache and install Nginx  // nginx ubuntu makinemize kuruluyor.
      apt:
        name: nginx
        state: latest
        update_cache: yes
    
    - name: Copy website files to the server's document root  // Oluşturulan statik sayfa dosyaları kopyalanıyor.
      copy:
        src: "{{ app_root }}"
        dest: "{{ document_root }}"
        mode: preserve

    - name: Check that a page returns a status 200 and fail if the word AWESOME is not in the page contents
      uri:
        url: www/...               // Belirlenmiş olan server adı.
        method: POST
        src: "{{app_root}}"
        dest: /etc/nginx/sites-available/default       
      failed_when: bootcamp!=devops

    - name: Apply normal page of nginx   // Eğer gelen request uygun değilse app sayfamıza dönüyor.
      template:
        src: "{{document_root}}" // işlem öncesi olması gereken default dosyalar alınıyor ve available kısma kopyalanıyor.
        dest: /etc/nginx/sites-available/default
      notify: Restart Nginx
      failed_when: bootcamp==devops

    - name: Enable new site   // Burada alınmış olan dosyalar sayfaya iletiliyor ve kullanıcıya sunuluyor.
      file:
        src: /etc/nginx/sites-available/default
        dest: /etc/nginx/sites-enabled/default
        state: link
      notify: Restart Nginx

    - name: Allow all access to tcp port 80 // sunulacak port'a izin veriliyor.
      ufw:
        rule: allow
        port: '80'
        proto: tcp

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted

```

Son olarak oluşturduğumuz playbook.yml çalıştırılıyor ve request'e bağlı olarak gidilecek sayfa belirleniyor:

`ansible-playbook -i hosts playbook.yml`  







