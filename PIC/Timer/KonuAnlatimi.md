# PIC16F887'de Timer (Zamanlayıcı) Kullanımı

## Sunum İçeriği

- [Timer Nedir ve Neden Kullanılır?](#timer-nedir-ve-neden-kullanilir)
- [PIC16F887'de Timer Türleri](#pic16f887ade-timer-turleri)
- [Timer0 Kullanımı](#timer0-kullanimi)
- [Timer1 Kullanımı](#timer1-kullanimi)
- [Timer2 Kullanımı](#timer2-kullanimi)
- [MicroC Pro for PIC ile Timer Kullanımı](#microc-pro-for-pic-ile-timer-kullanimi)

---

## Timer Nedir ve Neden Kullanılır?

Mikrodenetleyicilerdeki **Timer**, belirli bir süreyi ölçmek veya saymak için kullanılan özel bir donanım birimidir. Timer'lar, dışarıdan gelen sinyalleri veya mikrodenetleyicinin kendi saat darbesini (clock pulse) sayarak çalışır. Bu sayede, zaman bazlı işlemler (gecikme, periyodik görevler, darbe genişlik modülasyonu vb.) kesintisiz ve hassas bir şekilde gerçekleştirilebilir.

**Timer Kullanmanın Avantajları:**

- **Hassas Zamanlama:** Donanım seviyesinde çalıştığı için yazılım gecikme döngülerine göre çok daha hassas sonuçlar verir.
- **Ana Program Serbestliği:** Timer arka planda çalışırken ana program başka işlerle meşgul olabilir.
- **Düşük Güç Tüketimi:** Yazılım döngüleri yerine donanım tabanlı çalıştığı için CPU'yu daha az meşgul eder.

---

## PIC16F887'de Timer Türleri

PIC16F887 mikrodenetleyicisinde üç adet Timer bulunur:

| Timer   | Bit Sayısı | Özellikler                      | Kullanım Alanları                     |
|---------|------------|---------------------------------|---------------------------------------|
| Timer0  | 8-bit      | En basit timer, harici sayım yapabilir | Basit gecikmeler, buton debounce |
| Timer1  | 16-bit     | Uzun süreli sayım, harici osilatör | Gerçek zamanlı saat, frekans ölçümü |
| Timer2  | 8-bit      | PWM üretimi için optimize       | Motor kontrolü, LED parlaklığı       |


---

## Timer0 Kullanımı

Timer0, 8-bit bir sayıcıdır ve iki ana çalışma moduna sahiptir:

- **Timer Modu:** Mikrodenetleyicinin iç saat darbesini (Fosc/4) sayar.
- **Counter Modu:** Dışarıdan gelen harici sinyalleri (RA4 veya T0CKI) sayar.

### Timer0 Yazmaçları

Timer0'ı kontrol etmek için **TMR0** ve **OPTION_REG** yazmaçları kullanılır.

| Yazmaç | Açıklama |
|---|---|
| `TMR0` | 8-bitlik sayıcının kendisidir. 0'dan 255'e kadar sayar. Değer 255'i aştığında taşma gerçekleşir ve `T0IF` kesme bayrağı set edilir. |
| `OPTION_REG` | Timer0'ın çalışma modunu ve ön ölçekleyici ayarlarını kontrol eder. |

#### OPTION_REG Yazmaç Bitleri

| Bit No. | Bit Adı | Anlamı | Açıklama |
|---|---|---|---|
| **7** | `RBPU` | PORTB Pull-Up Enable | `0` olarak ayarlandığında, dahili PORTB pull-up dirençlerini etkinleştirir. `1` olduğunda devre dışı bırakır. Bu, harici bir pull-up direnci kullanmanıza gerek kalmadan buton okuma gibi işlemleri kolaylaştırır. |
| **6** | `INTEDG` | INT Kesme Kenar Seçimi | **RB0/INT** pinindeki harici kesmenin hangi kenarda tetikleneceğini belirler. `0` ise azalan kenar, `1` ise artan kenar ile kesme tetiklenir. |
| **5** | `T0CS` | Timer0 Saat Kaynak Seçimi | `0` ise dahili komut döngüsü saatini (Fosc/4) kullanır (Timer Modu). `1` ise **T0CKI/RA4** pininden gelen harici sinyali kullanır (Counter Modu). |
| **4** | `T0SE` | T0CKI Sinyal Kenar Seçimi | Sadece Counter Modunda (`T0CS=1`) geçerlidir. `0` ise azalan kenarda, `1` ise artan kenarda sayma yapar. |
| **3** | `PSA` | Ön Ölçekleyici Atama Biti | `0` ise ön ölçekleyiciyi Timer0'a atar. `1` ise ön ölçekleyiciyi **Watchdog Timer**'a atar. Timer0 için ön ölçekleyici kullanacaksanız bu bitin `0` olması gerekir. |
| **2** | `PS2` | Ön Ölçekleyici Değeri Biti | **PS2:PS0** bitleri birlikte, Timer0 için 1:2'den 1:256'ya kadar farklı ön ölçekleyici oranlarını ayarlar. |
| **1** | `PS1` | Ön Ölçekleyici Değeri Biti | - |
| **0** | `PS0` | Ön Ölçekleyici Değeri Biti | - |

> **Ön Ölçekleyici (PS2:PS0) Değerleri:**
>
> | PS2 | PS1 | PS0 | Oran |
> |:---:|:---:|:---:|:----:|
> | 0 | 0 | 0 | 1:2 |
> | 0 | 0 | 1 | 1:4 |
> | 0 | 1 | 0 | 1:8 |
> | 0 | 1 | 1 | 1:16 |
> | 1 | 0 | 0 | 1:32 |
> | 1 | 0 | 1 | 1:64 |
> | 1 | 1 | 0 | 1:128 |
> | 1 | 1 | 1 | 1:256|

---

## Timer1 Kullanımı

Timer1, 16-bit bir sayıcıdır ve 0'dan 65535'e kadar sayabilir. Timer0'a göre çok daha uzun süreli gecikmeler ve daha yüksek hassasiyetli zamanlama işlemleri için idealdir.

### Timer1 Yazmaçları

| Yazmaç | Açıklama |
|---|---|
| `TMR1H:TMR1L` | 16-bitlik sayıcının kendisidir. İki adet 8-bitlik yazmaç kullanılarak oluşturulur. |
| `T1CON` | Timer1'in çalışma modunu ve ön ölçekleyici ayarlarını kontrol eder. |

#### T1CON Yazmaç Bitleri

| Bit No. | Bit Adı | Anlamı | Açıklama |
|---|---|---|---|
| **7** | — | Okunmaz | Bu bit tanımlı değildir ve her zaman `0` olarak okunur. |
| **6** | — | Okunmaz | Bu bit tanımlı değildir ve her zaman `0` olarak okunur. |
| **5** | `T1CKPS1` | Ön Ölçekleyici Değeri (Bit 1) | **T1CKPS1:T1CKPS0** bitleri birlikte Timer1 için ön ölçekleyici oranını belirler: `00` = 1:1, `01` = 1:2, `10` = 1:4, `11` = 1:8 |
| **4** | `T1CKPS0` | Ön Ölçekleyici Değeri (Bit 0) | - |
| **3** | `T1OSCEN` | Timer1 Osilatör Enable | `1` olduğunda Timer1 için düşük güçlü 32.768 kHz kristal osilatörünü etkinleştirir. `0` olduğunda devre dışı bırakır. Bu osilatör gerçek zamanlı saat uygulamaları için kullanılır. |
| **2** | `T1SYNC` | Timer1 Harici Saat Senkronizasyonu | Sadece harici saat kaynağı seçildiğinde (`TMR1CS=1`) geçerlidir. `0` ise harici saat ile senkronize edilir, `1` ise senkronize edilmez (asenkron mod). |
| **1** | `TMR1CS` | Timer1 Saat Kaynak Seçimi | `0` ise dahili komut döngüsü saatini (Fosc/4) kullanır. `1` ise harici saat kaynağını kullanır (RC1/T1OSI/CCP2 ve RC0/T1OSO/T1CKI pinleri üzerinden). |
| **0** | `TMR1ON` | Timer1 Etkinleştirme Biti | `1` olduğunda Timer1'ı etkinleştirir ve sayma işlemi başlar. `0` olduğunda Timer1'ı durdurur ve TMR1H:TMR1L yazmaçlarını yazma işlemi için hazırlar. |

---

## Timer2 Kullanımı

Timer2, 8-bit bir sayıcıdır ve esnekliği sayesinde özellikle PWM uygulamalarında tercih edilir. En önemli özelliği, 255 değerini aşmak yerine **PR2** adı verilen programlanabilir bir limit değerine kadar saymasıdır.

### Timer2 Yazmaçları

| Yazmaç | Açıklama |
|---|---|
| `TMR2` | 8-bitlik sayıcının kendisidir. |
| `PR2` | Timer2'nin sayacağı maksimum değeri tutan yazmaçtır. TMR2 bu değere ulaştığında sıfırlanır. |
| `T2CON` | Timer2'nin çalışma modunu ve ön/son ölçekleyici ayarlarını kontrol eder. |

#### T2CON Yazmaç Bitleri

| Bit No. | Bit Adı | Anlamı | Açıklama |
|---|---|---|---|
| **7** | — | Okunmaz | Bu bit tanımlı değildir ve her zaman `0` olarak okunur. |
| **6** | `TOUTPS3` | Son Ölçekleyici Değeri (Bit 3) | **TOUTPS3:TOUTPS0** bitleri birlikte Timer2'nin çıkış son ölçekleyici oranını belirler. `0000` = 1:1, `0001` = 1:2, `0010` = 1:3, ..., `1111` = 1:16. Bu Timer2'nin taşma sinyalinin ne kadar bölüneceğini ayarlar. |
| **5** | `TOUTPS2` | Son Ölçekleyici Değeri (Bit 2) | - |
| **4** | `TOUTPS1` | Son Ölçekleyici Değeri (Bit 1) | - |
| **3** | `TOUTPS0` | Son Ölçekleyici Değeri (Bit 0) | - |
| **2** | `TMR2ON` | Timer2 Etkinleştirme Biti | `1` olduğunda Timer2'ı etkinleştirir ve sayma işlemi başlar. `0` olduğunda Timer2'ı durdurur. Timer2 PWM modülü ve diğer periyodik işlemler için kullanılır. |
| **1** | `T2CKPS1` | Ön Ölçekleyici Değeri (Bit 1) | **T2CKPS1:T2CKPS0** bitleri birlikte Timer2 için ön ölçekleyici oranını belirler: `00` = 1:1, `01` = 1:4, `1x` = 1:16 |
| **0** | `T2CKPS0` | Ön Ölçekleyici Değeri (Bit 0) | - |

## Timer Hesaplamaları

### Temel Hesaplama Formülü

```
Kesme_Süresi = (Prescaler × (Max_Değer - Başlangıç_Değeri)) / Fosc_4
```

- **Fosc:** Kristal osilatör frekansı (genellikle 4MHz → Fosc/4 = 1MHz)
- **Max_Değer:** Timer0 için 256, Timer1 için 65536

### Örnek Hesaplama (500ms gecikme - Timer1)

```
İstenilen süre: 500ms = 500,000μs
Prescaler: 1:8
Fosc/4: 1MHz (1μs per cycle)

Gerekli sayım = 500,000 / 8 = 62,500
Başlangıç değeri = 65,536 - 62,500 = 3,036
3,036 (decimal) = 0x0BDC (hex)

TMR1H = 0x0B
TMR1L = 0xDC
```

---

## Watchdog Timer nedir?
**Watchdog Timer (WDT)**, sistemin güvenilirliği için tasarlanmış özel bir güvenlik mekanizmasıdır. Adından da anlaşılacağı gibi "bekçi köpeği" görevini üstlenir.

### Watchdog Timer'ın Amacı

Mikrodenetleyici programları bazen beklenmedik durumlarla karşılaşabilir:
- **Sonsuz döngülere takılma**
- **Kod hatalarından dolayı donma**  
- **Elektriksel gürültüden etkilenme**
- **Stack overflow** gibi kritik hatalar

Bu durumları yakalamak için Watchdog Timer sürekli çalışır ve program düzgün çalıştığını kanıtlayamazsa **otomatik reset** atar.

### Watchdog Timer Çalışma Prensibi

```
Program Normal → CLRWDT Komutu → WDT Sıfırlanır → Devam Et
Program Dondu → CLRWDT Yok → WDT Süre Dolar → RESET!
```

## MicroC Pro for PIC ile Timer Kullanımı

MicroC Pro, Timer'ları kesme (interrupt) sistemi ile birlikte kullanmayı kolaylaştıran özel sözdizimi sunar.

### 1. Timer Kesme Servis Rutinini Oluşturma

Timer'dan kesme geldiğinde çalışacak kodlar, `interrupt` anahtar kelimesiyle tanımlanan fonksiyonun içine yazılır.

```c
void interrupt() {
  // Timer Kesme Servis Rutini (ISR)
  // Hangi timer'ın kesmesinin gerçekleştiğini kontrol et
}
```

### 2. Timer Kesme Bayrağını Kontrol Etme

ISR içinde, hangi timer'ın kesme ürettiğini bayrak bitlerini kontrol ederek buluruz.

```c
void interrupt() {
  if (INTCON.T0IF == 1) {    // Timer0 kesme bayrağı kontrol
    // Timer0 kesmesi için yapılacak işlemler
    INTCON.T0IF = 0;         // Bayrağı temizle!
  }
  
  if (PIR1.TMR1IF == 1) {   // Timer1 kesme bayrağı kontrol
    // Timer1 kesmesi için yapılacak işlemler
    PIR1.TMR1IF = 0;        // Bayrağı temizle!
  }
}
```

### 3. Örnek Uygulama: Timer0 ile 1 Saniyelik Gecikme

Bu örnekte, Timer0'ı kullanarak yaklaşık 1 saniyelik bir gecikme oluşturacağız.

```c
unsigned int timer0_sayac = 0; // Taşma sayısını tutacak değişken

void interrupt() {
  if (INTCON.T0IF == 1) {   // Timer0 taşma kesme bayrağı set edilmişse
    INTCON.T0IF = 0;        // Kesme bayrağını temizle
    TMR0 = 100;             // Timer'ı tekrar 100'den başlat
    timer0_sayac++;         // Taşma sayacını artır
  }
}

void main() {
  TRISC.B0 = 0;           // RC0 pinini çıkış yap (LED için)
  PORTC.B0 = 0;           // Başlangıçta LED'i kapat

  // Timer0 Ayarları
  OPTION_REG = 0b00000111;  // T0CS=0 (Timer modu), PSA=0, PS2:PS0=111 (1:256)
  TMR0 = 100;               // Timer'ı 100'den başlat

  // Kesme Ayarları
  INTCON.GIE = 1;           // Global kesmeleri etkinleştir
  INTCON.T0IE = 1;          // Timer0 kesmesini etkinleştir

  while(1) {
    if (timer0_sayac >= 25) { // 25 defa taşma (~1 saniye)
      PORTC.B0 = !PORTC.B0;   // LED'in durumunu değiştir
      timer0_sayac = 0;       // Sayacı sıfırla
    }
  }
}
```

### 4. Örnek Uygulama: Timer1 ile 100ms'lik Gecikme

Bu örnekte, Timer1 kullanarak 100ms'lik hassas bir gecikme oluşturacağız.

```c
void interrupt() {
  if (PIR1.TMR1IF == 1) { // Timer1 taşma kesme bayrağı kontrol
    PIR1.TMR1IF = 0;      // Kesme bayrağını temizle
    TMR1H = 207;          // Timer'ı tekrar 53036'dan başlat
    TMR1L = 100;          // (53036 = 0xCE64)
    PORTC.B0 = !PORTC.F0; // LED'in durumunu değiştir
  }
}

void main() {
  TRISC.B0 = 0;           // RC0 pinini çıkış yap (LED için)
  PORTC.B0 = 0;           // Başlangıçta LED'i kapat

  // Timer1 Ayarları
  T1CON = 0b00110001;     // Ön ölçekleyici: 1:8, Dahili Saat, Timer1 açık
  TMR1H = 207;            // Sayacın başlangıç değeri (üst 8 bit)
  TMR1L = 100;            // Sayacın başlangıç değeri (alt 8 bit)

  // Kesme Ayarları
  INTCON.GIE = 1;         // Global kesmeleri etkinleştir
  INTCON.PEIE = 1;        // Çevresel kesmeleri etkinleştir
  PIE1.TMR1IE = 1;        // Timer1 kesmesini etkinleştir

  while(1) {
    // Ana program burada çalışmaya devam eder
  }
}
```

### 5. Örnek Uygulama: Timer2 ile Periyodik LED Yanıp Sönmesi

Bu örnekte, Timer2'yi kullanarak yaklaşık 65ms'de bir LED'in durumunu değiştireceğiz.

```c
void interrupt() {
  if (PIR1.TMR2IF == 1) {   // Timer2 kesme bayrağı kontrol
    PIR1.TMR2IF = 0;        // Kesme bayrağını temizle
    PORTC.B0 = !PORTC.B0;   // LED'in durumunu değiştir
  }
}

void main() {
  TRISC.B0 = 0;           // RC0 pinini çıkış yap (LED için)
  PORTC.B0 = 0;           // Başlangıçta LED'i kapat

  // Timer2 Ayarları
  PR2 = 255;                // Timer2'nin maksimum sayım değeri
  T2CON = 0b01111110;       // Son Ölçekleyici 1:16, Ön Ölçekleyici 1:16

  // Kesme Ayarları
  INTCON.GIE = 1;           // Global kesmeleri etkinleştir
  INTCON.PEIE = 1;          // Çevresel kesmeleri etkinleştir
  PIE1.TMR2IE = 1;          // Timer2 kesmesini etkinleştir

  T2CON.TMR2ON = 1;         // Timer2'yi başlat

  while(1) {
    // Ana program burada çalışmaya devam eder
  }
}
```

### 6. Örnek Uygulama: Watchdog ile Güvenlik Sistemi
Bu örnekte, kapı, hareket ve cam sensörleri ile çalışan bir alarm sistemini Watchdog Timer ile koruma altına alacağız. Sistem yaklaşık 2.3 saniye içinde watchdog beslenmezse otomatik reset gerçekleştirecektir.

```c
unsigned char alarm_durumu = 0;
unsigned char sensor_okuma_sayaci = 0;

void main() {
    // WDT ayarları (Configuration'da WDT_ON olmalı)
    OPTION_REG = 0b00001111;    // WDT prescaler en yüksek (~2.3s)
    
    // Pin ayarları
    TRISB = 0xFF;               // Sensörler (giriş)
    TRISD = 0x00;               // Alarm çıkışları
    TRISC.B0 = 0;               // Sistem canlı LED'i
    
    PORTD = 0;                  // Alarm başlangıçta kapalı
    
    while(1) {
        // Sistem canlılık göstergesi
        PORTC.B0 = !PORTC.B0;
        
        // Sensörleri oku
        if (PORTB.B0 == 0) {    // Kapı sensörü
            alarm_durumu = 1;
        }
        if (PORTB.B1 == 0) {    // Hareket sensörü  
            alarm_durumu = 1;
        }
        if (PORTB.B2 == 0) {    // Cam sensörü
            alarm_durumu = 1;
        }
        
        // Alarm kontrolü
        if (alarm_durumu) {
            PORTD.B0 = 1;       // Siren
            PORTD.B1 = 1;       // Flaş
        }
        
        // Sistem sıfırlama butonu
        if (PORTB.B7 == 0) {
            alarm_durumu = 0;
            PORTD = 0;
            Delay_ms(1000);
        }
        
        // Kritik nokta: Watchdog'u besle
        asm CLRWDT;             // Watchdog Timer'ı sıfırla
        
        sensor_okuma_sayaci++;
        
        // Eğer bu nokta 2.3 saniyede gelmezsek reset!
        Delay_ms(100);
    }
}
```