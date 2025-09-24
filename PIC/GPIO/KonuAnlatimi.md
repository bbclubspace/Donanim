# PIC16F887 Mikrodenetleyici ile GPIO 


## Sunum İçeriği

- [GPIO Nedir?](#gpio-nedir)
- [PIC16F887'de GPIO'nun Çalışma Prensibi](#pic16f887de-gpionun-calisma-prensibi)
- [Örnek 1: LED Yakma](#örnek-1-led-yakma)
- [Örnek 2: Buton Okuma](#örnek-2-buton-okuma)

---

## GPIO Nedir?

**GPIO**, "General Purpose Input/Output" (Genel Amaçlı Giriş/Çıkış) kelimelerinin kısaltmasıdır. Mikrodenetleyicilerde, çeşitli sensörler, anahtarlar, LED'ler veya motorlar gibi dış donanımlarla iletişim kurmak için kullanılan esnek pinlerdir. Her bir GPIO pini, duruma göre **giriş** veya **çıkış** olarak programlanabilir.

- **Giriş:** Pinin dış dünyadan bir sinyal (örneğin, bir butonun basılıp basılmadığı) okuması için kullanılır.
- **Çıkış:** Pinin dış dünyaya bir sinyal (örneğin, bir LED'i açma veya kapama) göndermesi için kullanılır.

---

## PIC16F887'de GPIO'nun Çalışma Prensibi

PIC16F887'daki GPIO pinleri, **TRIS** ve **PORT** olmak üzere iki ana yazmaç (register) ile kontrol edilir. Programlamaya başlamadan önce bu iki yazmacın amacını ve nasıl kullanıldığını anlamak çok önemlidir.

---

### TRIS Yazmaçları (Yön Ayarı)

**TRIS** yazmaçları, ilgili **PORT** pinlerinin yönünü (giriş mi, çıkış mı) belirler. Bu, programın en başında yapılması gereken en önemli ayardır.

- `TRISX` yazmacındaki `0` değeri, o pine karşılık gelen `PORTX` pinini **çıkış** olarak ayarlar.
- `TRISX` yazmacındaki `1` değeri, o pine karşılık gelen `PORTX` pinini **giriş** olarak ayarlar.

#### Örnekler:

| Kod | İkilik Karşılığı | Anlamı |
|---|---|---|
| `TRISB = 0x00;` | `0b00000000` | **PORTB**'deki tüm pinleri **ÇIKIŞ** olarak ayarlar. |
| `TRISB = 0xFF;` | `0b11111111` | **PORTB**'deki tüm pinleri **GİRİŞ** olarak ayarlar. |
| `TRISB = 0x02;` | `0b00000010` | Sadece **RB1** pinini **GİRİŞ**, diğer pinleri **ÇIKIŞ** olarak ayarlar. |
| `TRISD = 0xC3;` | `0b11000011` | **RD0, RD1, RD6, RD7** pinlerini **GİRİŞ**, diğerlerini **ÇIKIŞ** olarak ayarlar. |

---

### PORT Yazmaçları (Mantıksal Durum)

**PORT** yazmaçları, pinlerin anlık mantıksal durumunu (Lojik 0 veya Lojik 1) tutar. Pinlerin yönünü **TRIS** yazmacıyla belirledikten sonra, bu yazmaçları kullanarak pinlerin durumunu kontrol edersiniz.

- **Çıkış Olarak Kullanım:** Bir pini çıkış olarak ayarladıktan sonra, o pine lojik-1 (HIGH) veya lojik-0 (LOW) sinyali göndermek için **PORT** yazmacına değer yazarsınız.

#### Örnekler:

| Kod | İkilik Karşılığı | Anlamı |
|---|---|---|
| `PORTB = 0x01;` | `0b00000001` | Sadece **RB0** pinini HIGH (lojik-1) yapar, diğer pinler LOW olur. (Yani B0 pinine bağlı olan LED yanar, B portunda ki diğer pinlere bağlı LED ler ise yanmaz.)|
| `PORTB = 0xFF;` | `0b11111111` | **PORTB**'deki tüm pinleri HIGH yapar. |
| `PORTB = 0x0A;` | `0b00001010` | Sadece **RB1** ve **RB3** pinlerini HIGH yapar, diğerleri LOW olur. |

- **Giriş Olarak Kullanım:** Bir pini giriş olarak ayarladıktan sonra, o pindeki sinyali okumak için **PORT** yazmacının o bitini kontrol edersiniz.

#### Örnek:

| Kod | Anlamı |
|---|---|
| `if (PORTA.B0 == 1)` | **PORTA**'nın 0. pini (`RA0`) HIGH durumundaysa (`1` ise), if bloğu çalışır. |
| `if (PORTC.B5 == 0)` | **PORTC**'nin 5. pini (`RC5`) LOW durumundaysa (`0` ise), if bloğu çalışır. |

---

### Neden İkilik (`0b`) ve Onaltılık (`0x`) Kullanılır?

- **İkilik (`0b`) sistem:** Pinlerin yönünü veya durumunu tek tek, bit bazında düşünmek için idealdir. Örneğin, `0b10100000` yazarak hangi pinlerin açık/kapalı olduğunu görsel olarak kolayca anlayabilirsiniz.
- **Onaltılık (`0x`) sistem:** İkilik sayıların daha kompakt ve okunabilir bir şekilde ifade edilmesini sağlar. Örneğin, `0b11111111` yerine `0xFF` yazmak daha kolaydır.
---

**Örnek:** `0b11010110` (İkilik) sayısını onaltılığa çevirirken, 8 biti ikiye ayırırız: `1101` ve `0110`.
- `1101` ikilik sayısı onaltılıkta **`D`** harfine denk gelir.
- `0110` ikilik sayısı onaltılıkta **`6`** rakamına denk gelir.
Sonuç olarak, `0b11010110` ikilik sayısı, `0xD6` onaltılık sayısına eşittir. Bu ilişki sayesinde, uzun ikilik sayılar yerine daha kısa ve okunabilir onaltılık sayılarla çalışabiliriz.

---

### Pinlere Karşılıkları

Bir mikrodenetleyicinin portlarındaki pinler, genellikle D0, D1, D2, ... gibi isimlerle anılır. Bu isimlendirme, pinin port içindeki konumunu (bit numarasını) belirtir.

Örneğin, **PORTD**'yi ele alalım. Bu port 8 bittir ve D7'den D0'a kadar numaralandırılır.

| Bit Numarası | `7` | `6` | `5` | `4` | `3` | `2` | `1` | `0` |
|---|---|---|---|---|---|---|---|---|
| Pin Adı | RD7 | RD6 | RD5 | RD4 | RD3 | RD2 | RD1 | RD0 |

Şimdi bu pinleri, ikilik ve onaltılık sayı sistemleriyle ilişkilendirelim:

**Örnek:** `PORTD = 0b10000001;` komutu, aşağıdaki pinleri ayarlar:

| Bit | **7** | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|---|---|---|---|---|---|---|---|---|
| Değer | `1` | `0` | `0` | `0` | `0` | `0` | `0` | `1` |
| Pin | RD7 | RD6 | RD5 | RD4 | RD3 | RD2 | RD1 | RD0 |
| Durum | **HIGH** | LOW | LOW | LOW | LOW | LOW | LOW | **HIGH** |


Bu ikilik sayının onaltılık karşılığı `0x81`'dir. Gördüğünüz gibi, `0x81` kodu da aynı işi yapar; yani **RD7** ve **RD0** pinlerini HIGH (lojik-1), diğerlerini ise LOW (lojik-0) yapar.


Bu yazmaçları doğru bir şekilde anlamak ve kullanmak, GPIO ile yapacağınız tüm projelerin temelini oluşturur. Hangi pini giriş, hangi pini çıkış yapacağınıza karar verirken her zaman önce **TRIS** yazmacını, daha sonra da pinin mantıksal durumunu ayarlamak için **PORT** yazmacını kullanmayı unutmayın.

---

## Örnek 1: LED Yakma

Bu örnekte, **PORTB'nin 0. pinine (`RB0`) bağlı bir LED'i yakıp söndüreceğiz.**

### Devre Şeması

- PIC16F887
- `RB0` pinine bağlı 330 ohm'luk direnç ve LED
- PIC'in MCLR pini 5V'a pull-up dirençle bağlı, VDD-VSS bağlantıları yapılmış.

### MicroC Pro Kodu

```c
void main() {
  TRISB.B0 = 0;   // RB0 pinini ÇIKIŞ olarak ayarla (TRISB = 0x00 ya da 0b00000000)
  PORTB.B0 = 0;   // Başlangıçta RB0 pinine LOW sinyali gönder (LED kapalı)
  
  while(1) {
    PORTB.B0 = 1; // RB0 pinini HIGH yap (LED açılır)
    Delay_ms(1000); // 1 saniye bekle
    PORTB.B0 = 0; // RB0 pinini LOW yap (LED kapanır)
    Delay_ms(1000); // 1 saniye bekle
  }
}
```
---

## Örnek 2: Buton Okuma

Bu örnekte, **PORTA'nın 0. pinine (`RA0`) bağlı bir butona basıldığında, PORTB'nin 0. pinine (`RB0`) bağlı LED'i yakacağız.** Bu uygulama, bir girişi okuyup, bu girişe göre bir çıkışı kontrol etmenin temel bir örneğidir.

### Devre Şeması

- PIC16F887
- `RA0` pinine bağlı pull-down direnci ve buton
- `RB0` pinine bağlı 330 ohm'luk direnç ve LED

### MicroC Pro Kodu

```c
void main() {
  TRISA.B0 = 1;  // RA0 pinini GİRİŞ olarak ayarla (TRISA = 0x01 ya da 0b00000001)
  TRISB.B0 = 0;  // RB0 pinini ÇIKIŞ olarak ayarla (TRISB = 0x00 ya da 0b00000000)

  while(1) {
    if (PORTA.B0 == 1) {  // RA0 pininin durumu HIGH ise (butona basılmışsa)
      PORTB.B0 = 1;       // RB0'daki LED'i YAK
    }
    else {                // Değilse (butona basılmamışsa)
      PORTB.B0 = 0;       // RB0'daki LED'i SÖNDÜR
    }
  }
}