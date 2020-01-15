# pg_hba_conf-



PG_HBA_CONF  NEDİR & NE YAPAR

PostgreSQL'de istemci kimlik doğrulaması için oluşturulmuş konfigürasyon dosyasıdır.
pg_hba.conf satır başına bir kayıt alır.
Conf dosyası okunurken bir kayıt seçilirse ve doğrulama başarısız olursa, sonraki kayıtlar dikkate alınmaz. Kayıt eşleşmezse erişim reddedilir.


7 tane kayıt formatı vardır.
```
local      database  user  auth-method  [auth-options]
host       database  user  address  auth-method  [auth-options]
hostssl    database  user  address  auth-method  [auth-options]
hostnossl  database  user  address  auth-method  [auth-options]
host       database  user  IP-address  IP-mask  auth-method  [auth-options]
hostssl    database  user  IP-address  IP-mask  auth-method  [auth-options]
hostnossl  database  user  IP-address  IP-mask  auth-method  [auth-options]
```

Buradaki parametreler şu anlamlara geliyor;

local: unix domain soket kullanan bağlantılar için.
unix domain soket : Linux ve Unix bazlı sistemlerde prosesler arası iletişim kurma metotlarından biridir. İşletim sistemleri, aynı host üzerinde koşan proseslerin birbirleri ile haberleşmesi gerektiği durumlarda kullanılmak üzere IPC - Inter-Process Communication adı verilen mekanizmaları kullanıma sunarlar. Bu mekanizmalardan biri Unix Domain Socket'lerdir. İnternet soketlerine benzerler  fakat sadece aynı  host üzerinden erişilebilirler. İnternet soketleri iletişim kurmak için ağ altyapısını kullanırken Domain Socket'ler işletim sisteminin çekirdeği üzerinden haberleşirler. İnternet soketlerinin aksine ip katmanını kullanmadıkları için daha hızlıdırlar. Host dışı erişimi de engelledikleri için doğal olarak izolasyon da sağlamış olurlar.
Aynı host üzerinde bulunma zorunluluğu ölçeklenme ihtiyacı bulunan uygulamalarda Unix Domain Socket kullanımını imkansız hale getirir.[1]
[1] https://medium.com/@gokhansengun/unix-domain-socket-nedir-ve-ne-i%C5%9Fe-yarar-c72fe8decb30




host: tcp/ip kullanan bağlantılar için.
tcp/ip bağlantısı için, listen_Address  parametresi ayarlanmalıdır. yoksa default localhost olarak gelir.(postgresql.conf  dan ayarlanır)
(main ve taurus da bu kapalı, bunlara bağlanıl mıyor mu acep?)
--biz yönettiğimiz hiçbir clusterda postgresql.conf dosyasını düzenlemiyoruz
listen addresses  postgresql.auto.conf da ayarlı...


hostssl: tcp/ip bağlantılarının sadece  ssl olanlarını kabul eder.
hostnossl: tcp/ip bağlantılarının ssl olmayanlarını kabul eder.

database: kaydın hangi veritabanı ile eşleştiğini belirtir.”all” tüm veritabanları ile eşleşir.

user:    kaydın hangi veritabanı kullanıcı adlarıyla eşleştiğini belirtir. “all” tüm kullanıcılarla eşleşir.

address: eşleşilecek client adreslerini belirtir. 0.0.0.0/0 yaparsak herkese açmış oluruz. Bu alan bilgisayar adı, ip adresi yada özel bir ad olabilir. 
Tek bir host için maske uzunluğu 32 olmalıdır (ipv4)

auth-method: kimlik doğrulama yöntemini belirtir.
    trust: koşulsuz bağlantıya izin verir.
    reject: koşulsuz bağlantıyı red eder.
    sha-256: kimlik doğrulaması için sha-256 ister.
    md5: doğrulama için md5 veya sha256 ister.
    password: client’dan şifrelenmemiş parola ister.
    peer:sadece yerel ağda kullan. kullanıcının OS adıyla, DB adını kıyaslar.
    ldap,  ident, radius, cert, pam ve bsd diğer yöntemlerdir.


pg_hba.conf kayıtları her bağlantı denemesi için sırayla incelediğinden, kayıtların sırası önemlidir. 
# earlier records will have tight connection match parameters and weaker authentication methods, while later records will have      looser match parameters and stronger authentication methods


RELOAD
pg_hba_conf  içinde yapılan değişiklikler sonrasında reload yapmak gerekir.
“pg_ctl reload”  veya  SQL fonksiyonlarında  “pg_reload_conf(); “  ile yapılır.
windows işletim sistemini reload gerekmeden değişiklikleri okur.

Belirli bir veritabanına bağlanmak için pg_hba.conf  kontrollerini geçmek yetmez, ayrıca connect ayrıcalığı da elde edilmesi gerekir. Kullanıcıların database’lere girişlerini kısıtlamak için connect ayrıcalığı ile düzenlemeler yapılır.
