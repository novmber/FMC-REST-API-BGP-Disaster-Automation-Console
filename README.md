# FMC-REST-API-BGP-Disaster-Automation-Console
Disaster Recovery senaryolarında, Cisco FMC (Firepower Management Center) altındaki FTD (Firepower Threat Defense) firewall cihazlarının fiziksel arayüz durumlarını ve upstream omurga router'larındaki BGP anonslarını tek merkezden otomatize etmek üzere tasarlanmış mimari projeyi ve yol haritasını barındırır.


Sistem Mimarisi ve Bileşenler
Proje, operasyonel güvenliği ve ağ kararlılığını en üst düzeyde tutmak amacıyla asenkron bir backend ve taşma/kayma yapmayan esnek bir ön yüz mimarisi üzerine kurulmuştur.
Ön Yüz Tasarım Prensipleri (UI/UX)
Sıfır Yatay Kaydırma (Zero Horizontal Scroll): Uzun REST API URI'leri veya çok satırlı BGP komut setleri gelse bile, arayüz genişliği 1180px sınırına kilitlenir. İçerikler dinamik olarak kelime sınırlarından (word-break: break-all) alt satıra kırılır.

Durum Kontrollü Topoloji: (Ana Merkez) ve  (DR) lokasyonlarının aktiflik, pasiflik ve arayüz sağlık durumları anlık CSS sınıfları ile görselleştirilir.

2. Backend & Otomasyon Altyapısı
Asenkron Motor: FastAPI tabanlı async/await yapısı sayesinde cihaz sorgulamaları ve konfigürasyon basma işlemleri birbirini bloklamadan (non-blocking) paralel yürütülür.

Cisco FMC Entegrasyonu: Kimlik doğrulama token'ı alındıktan sonra bulk (toplu) interface durum güncellemeleri yapılır ve değişikliklerin devreye girmesi için asenkron DeploymentRequest tetiklenir.

BGP Entegrasyonu: Edge router'lara Netmiko üzerinden SSH kanalları açılarak felaket anında anons yönünü değiştirecek prefix-list ve route-map manipülasyonları gerçekleştirilir.

3. Sıkılaştırılmış Güvenlik (MFA/TOTP)
Cihazlarda yıkıcı veya rotayı değiştirecek aksiyonlar (Disaster Trigger / Rollback) çalıştırılmadan önce, operasyonu yapan mühendisin Authenticator uygulamasındaki (Google/Microsoft) zamana duyarlı 6 haneli TOTP token kodunu girmesi ve doğrulaması zorunludur.

📂 Öngörülen Klasör Ağacı
Geliştirme sürecinde takip edilecek modüler klasör yapısı:

Plaintext
disaster-console/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI ana uygulama ve HTTP rota (Route) yönetimi
│   ├── core/
│   │   ├── config.py           # .env dosyası ve çevresel değişken yönetimi
│   │   └── security.py         # JWT Token üretimi, şifreleme ve TOTP/MFA kontrolü
│   ├── services/
│   │   ├── fmc_service.py      # Cisco FMC REST API entegrasyon servisi
│   │   └── bgp_service.py      # Router SSH (Netmiko) otomasyon servisi
│   └── templates/
│       └── index.html          # Kayma/taşma sorunları çözülmüş kararlı dashboard arayüzü
├── .env.example                # Örnek konfigürasyon şablonu
├── requirements.txt            # Python bağımlılık listesi
└── disaster_automation.service # Linux Systemd servis şablonu

🛠️ Bağımlılıklar ve Dağıtım Gereksinimleri
Projenin üretim (Production) ortamında kararlı çalışabilmesi için aşağıdaki bileşenler ve kütüphaneler temel alınmıştır:

İşletim Sistemi: Ubuntu Server 20.04 LTS / 22.04 LTS

Çalışma Zamanı: Python 3.10+ & Virtualenv

Temel Paketler:

fastapi & uvicorn (Asenkron API Gateway ve Web Sunucu)

requests (Cisco FMC REST HTTP çağrıları)

netmiko (Router SSH konfigürasyon yönetimi)

pyotp (MFA / İki Aşamalı Doğrulama motoru)

python-jose & passlib (JWT ve Güvenli Kimlik Yönetimi)

🗺️ Proje Yol Haritası (Roadmap) & TODO
Geliştirme süreçlerini GitHub Issues ve Projects üzerinden takip etmek için belirlenen aşamalar:

[ ] Aşama 1: UI Stabilizasyonu & Mock Entegrasyonu

[ ] Taşmayan yeni HTML arayüz şablonunun backend şablon motoruna (Jinja2) entegre edilmesi.

[ ] Ön yüzde senaryo butonlarına basıldığında simüle edilen JavaScript log adımlarının backend API'ye bağlanması.

[ ] Aşama 2: Güvenlik ve MFA Altyapısı

[ ] /api/v1/auth rotalarının yazılması ve JWT üretim süreçleri.

[ ] pyotp entegrasyonu ile ilk kurulumda QR secret üretilmesi ve aksiyon onay pencerelerinde OTP doğrulama validasyonunun zorunlu kılınması.

[ ] Aşama 3: Cisco FMC ve Network Adaptörleri

[ ] FMC API token alma ve physical interface listeleme fonksiyonlarının fmc_service.py altında tamamlanması.

[ ] Netmiko bağlantı havuzu (connection pool) kurularak BGP neighbor shutdown/no-shutdown komut setlerinin test edilmesi.

[ ] Aşama 4: Linux Deployment ve Audit Log

[ ] Gerçekleşen tüm işlemlerin zaman damgası ve operatör bilgisiyle veritabanına/log dosyasına immutable (değiştirilemez) olarak yazılması.

[ ] systemd servis dosyasının sunucuya yüklenerek uygulamanın 4 worker ile 127.0.0.1:8000 üzerinde daimi canlıya alınması.

🤝 Katkıda Bulunma (Contributing)
Bu projeyi forklayın (git clone).

Yeni bir özellik için branch açın (git checkout -b feature/MfaGelistirmesi).

Değişikliklerinizi commit edin (git commit -am 'Eklendi: MFA Kontrol Noktası').

Branch'inizi push edin (git push origin feature/MfaGelistirmesi).

Bir Pull Request oluşturun.
