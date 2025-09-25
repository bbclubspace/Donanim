# PIC16F887'da Kesme (Interrupt) Kullanımı
## Sunum İçeriği

- [Kesme Nedir ve Neden Kullanılır?](#kesme-nedir-ve-neden-kullanılır)
- [PIC16F887'de Kesme Kaynakları](#pic16f887de-kesme-kaynakları)
- [Kesme Yazmaçları ve Ayarları](#kesme-yazmaçları-ve-ayarları)
- [MicroC Pro for PIC ile Kesme Kullanımı](#microc-pro-for-pic-ile-kesme-kullanımı)

---

## Kesme Nedir ve Neden Kullanılır?

Mikrodenetleyici programlamada **kesme (interrupt)**, ana program akışını durdurarak öncelikli bir görevi hemen yerine getirmemizi sağlayan bir mekanizmadır. Bir kesme olayı tetiklendiğinde (örneğin, bir butona basıldığında veya bir zamanlayıcı dolduğunda), mikrodenetleyici mevcut işini bırakır ve **Kesme Servis Rutini (ISR)** adı verilen özel bir alt programa atlar. Bu alt program tamamlandığında, ana program kaldığı yerden çalışmaya devam eder.

**Kesme Kullanmanın Avantajları:**

- **Anında Tepki:** Butona basma veya harici bir sinyal gibi olaylara anında tepki verir. Ana programın sürekli bir döngüde (`while(1)`) sensör durumunu kontrol etmesine gerek kalmaz.
- **Verimlilik:** Mikrodenetleyici, önemli bir olayın gerçekleşmesini beklemeden diğer görevleri yerine getirebilir. Bu, CPU döngülerinin boşa harcanmasını önler ve programı daha verimli hale getirir.

---

## PIC16F887'de Kesme Kaynakları

PIC16F887, birçok farklı kaynaktan kesme oluşturabilir. En yaygın kullanılan kesme kaynakları şunlardır:

- **Harici Kesme (`INT`):** **RB0** pinine gelen harici bir sinyal ile tetiklenir.
- **PortB'deki Durum Değişikliği (`RBIE`):** **RB4-RB7** pinlerinden herhangi birinin durumu değiştiğinde tetiklenir.
- **Timer Kesmeleri:** Dahili zamanlayıcılar (Timer0, Timer1, Timer2) belirli bir değere ulaştığında tetiklenir.
- **ADC Kesmesi:** Analog-Dijital Dönüştürme işlemi tamamlandığında tetiklenir.
- **USART Kesmesi:** Seri iletişimde veri alma veya gönderme işlemi tamamlandığında tetiklenir.

---

## Kesme Yazmaçları ve Ayarları

Kesmeleri aktif hale getirmek için bazı özel yazmaçların ayarlanması gerekir. En önemli yazmaçlar **INTCON**, **PIE1** ve **PIR1**'dir.

### INTCON Yazmacı (Ana Kesme Kontrolü)

Bu yazmaç, kesme sisteminin genel kontrolünü sağlar.

| Bit Adı | Anlamı | Açıklama |
|---|---|---|
| `GIE` | Global Interrupt Enable | Bu biti **1** yaparak tüm kesme sistemini etkinleştirirsiniz. Kesme kullanacaksanız bu bitin **1** olması zorunludur. |
| `PEIE` | Peripheral Interrupt Enable | Çevresel birim kesmelerini (Timer, ADC vb.) etkinleştirir. |
| `TOIE` | TMR0 Interrupt Enable | Timer0'ın taşma (overflow) kesmesini etkinleştirir.
| `INTE` | INT External Interrupt Enable | **RB0** pinindeki harici kesmeyi etkinleştirir. |
| `RBIE` | RB Port Change Interrupt Enable | **RB4-RB7** pinlerindeki durum değişikliği kesmesini etkinleştirir. |
| `TOIF` | TMR0 Interrupt Flag | Timer0'ın sayma işlemi tamamlandığında (taşma olduğunda) donanımsal olarak **1** olur. Bayrağı yazılımla **0** yaparak temizlemeniz gerekir.
| `INTF` | INT External Interrupt Flag | Harici kesme olayı gerçekleştiğinde donanımsal olarak **1** olur. Yazılımla **0** yapılmalıdır. |
| `RBIF` | RB Port Change Interrupt Flag | RB4-RB7 pinlerinde durum değişikliği olduğunda **1** olur. Yazılımla **0** yapılmalıdır. |

---

### MicroC Pro for PIC ile Kesme Kullanımı

MicroC Pro, kesme servis rutini (`ISR`) yazmayı çok kolaylaştıran özel bir sözdizimi sunar.

#### 1. Kesme Servis Rutinini Oluşturma

Kesme oluştuğunda çalışacak kodlar, `interrupt` anahtar kelimesiyle tanımlanan bir fonksiyonun içine yazılır.

```c
void interrupt() {
  // Kesme Servis Rutini (ISR)
  // Hangi kesmenin gerçekleştiğini kontrol et ve ilgili işlemi yap.
}
```

#### 2. Kesme Bayrağını Kontrol Etme
ISR içinde, hangi kesme kaynağının tetiklendiğini bayrak (flag) bitlerini kontrol ederek buluruz. Her kesmenin kendi bayrak biti vardır (**INTF, RBIF** vb.).

```c
void interrupt() {
  if (INTCON.INTF == 1) { // RB0 harici kesme bayrağı 1 ise
    // RB0 kesmesi için yapılacak işlemler
    // ...
    INTCON.INTF = 0; // Bayrağı temizle! Bu adım hayati önem taşır.
  }
  
  if (INTCON.RBIF == 1) { // PORTB durum değişikliği kesme bayrağı 1 ise
    // PORTB kesmesi için yapılacak işlemler
    // ...
    INTCON.RBIF = 0; // Bayrağı temizle!
  }
}
```

#### 3. Örnek Uygulama: Buton ile LED Kontrolü (Kesme ile)

```c
void interrupt() {
  if (INTCON.INTF == 1) {
    // LED'in durumunu tersine çevir
    PORTC.B0 = !PORTC.B0;
    
    // Kesme bayrağını temizle
    INTCON.INTF = 0;
  }
}

void main() {
  // Pinlerin yönlerini ayarla
  TRISB.B0 = 1;      // RB0'ı giriş yap (Buton için)
  TRISC.B0 = 0;      // RC0'ı çıkış yap (LED için)
  
  // Kesme Ayarları
  INTCON.GIE = 1;    // Global kesmeyi etkinleştir
  INTCON.INTE = 1;   // Harici kesmeyi etkinleştir (RB0)
  
  // LED'i başlangıçta kapalı tut
  PORTC.B0 = 0;

  while(1) {
    // Ana program burada çalışmaya devam eder
    // Kesme gerçekleşene kadar döner
  }
}
```