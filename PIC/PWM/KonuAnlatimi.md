# PIC16F887'da PWM (Pulse Width Modulation) Kullanımı
## Sunum İçeriği

- [PWM Nedir ve Nasıl Çalışır?](#pwm-nedir-ve-nasıl-çalışır)
- [PIC16F887'de PWM Özellikleri](#pic16f887de-pwm-özellikleri)
- [MicroC Pro for PIC ile PWM Kullanımı](#microc-pro-for-pic-ile-pwm-kullanımı)
- [Örnek Uygulamalar](#örnek-uygulamalar)

---

## PWM Nedir ve Nasıl Çalışır?

**PWM (Pulse Width Modulation)** yani **Darbe Genişlik Modülasyonu**, dijital sinyallerin analog sinyalleri taklit etmesini sağlayan bir tekniktir. PWM, sabit bir frekansla açık (HIGH) ve kapalı (LOW) durumlar arasında geçiş yapan dijital bir sinyaldir.

**PWM'in Temel Kavramları:**

- **Periyot (Period):** Bir PWM döngüsünün tamamlanması için geçen süredir.
- **Frekans:** Saniyede kaç PWM döngüsünün tamamlandığını belirtir. `Frekans = 1/Periyot`
- **Duty Cycle (Görev Döngüsü):** Sinyalin bir periyot içinde ne kadar süre HIGH seviyede kaldığını yüzde olarak ifade eder.

```
PWM Sinyali:
     HIGH  |‾‾‾‾‾‾‾‾|     |‾‾‾‾‾‾‾‾|
           |        |     |        |
     LOW   |        |_____|        |_____
           
           ←---Periyot----→
           ←---ON--→←-OFF-→

Duty Cycle = (ON Süresi / Periyot) × 100%
```

**PWM Kullanım Alanları:**

- **Motor Hız Kontrolü:** DC motorların hızını kontrol etmek için
- **LED Parlaklık Kontrolü:** LED'lerin parlaklığını ayarlamak için
- **Servo Motor Kontrolü:** Servo motorların pozisyon kontrolü için
- **Güç Kontrolü:** Elektrikli cihazların güç tüketimini kontrol etmek için

---

## PIC16F887'de PWM Özellikleri

PIC16F887 mikrodenetleyicisi, **2 adet donanımsal PWM kanalına** sahiptir:

- **CCP1 Modülü:** **RC2** pini üzerinden PWM çıkışı sağlar
- **CCP2 Modülü:** **RC1** pini üzerinden PWM çıkışı sağlar

**PWM Özellikleri:**

- **8-bit PWM çözünürlük:** 0-256 arası değerlerle duty cycle kontrolü
- **Programlanabilir frekans:** Timer2 ile PWM frekansı ayarlanabilir
- **Donanımsal PWM:** CPU'ya yük bindirmeden otomatik PWM üretimi

**PWM Frekansı Hesaplama:**

```
PWM_Frekans = Fosc / (4 × (PR2 + 1) × TMR2_Prescaler)

Fosc: Osilatör frekansı (örneğin 8 MHz)
PR2: Timer2'nin karşılaştırma değeri
TMR2_Prescaler: Timer2'nin ön bölücü değeri (1, 4, 16)
```

---


## MicroC Pro for PIC ile PWM Kullanımı

MicroC Pro, PWM kullanımını kolaylaştıran hazır fonksiyonlar sunar.

### 1. PWM Başlatma

```c
PWM1_Init(5000);  // CCP1'de 5kHz PWM başlat
PWM2_Init(5000);  // CCP2'de 5kHz PWM başlat
```

### 2. PWM Başlatma (Manuel Ayar)

```c
PWM1_Start();     // CCP1 PWM'i başlat
PWM2_Start();     // CCP2 PWM'i başlat
```

### 3. Duty Cycle Ayarlama

```c
PWM1_Set_Duty(128);  // %50 duty cycle (0-255 arası)
PWM2_Set_Duty(64);   // %25 duty cycle
```

### 4. PWM Durdurma

```c
PWM1_Stop();      // CCP1 PWM'i durdur
PWM2_Stop();      // CCP2 PWM'i durdur
```

---

## Örnek Uygulamalar

### Örnek 1: LED Parlaklık Kontrolü

```c
void main() {
  // PWM pinini çıkış yap
  TRISC.B2 = 0;     // RC2'yi çıkış yap (CCP1)
  
  // 5kHz PWM başlat
  PWM1_Init(5000);
  PWM1_Start();
  
  unsigned char brightness = 0;
  
  while(1) {
    // LED parlaklığını artır
    for(brightness = 0; brightness < 255; brightness++) {
      PWM1_Set_Duty(brightness);
      Delay_ms(10);
    }
    
    // LED parlaklığını azalt  
    for(brightness = 255; brightness > 0; brightness--) {
      PWM1_Set_Duty(brightness);
      Delay_ms(10);
    }
  }
}
```


### Örnek 2: Servo Motor Kontrolü

```c
void main() {
  // PWM pini ayarı
  TRISC.B2 = 0;     // RC2'yi çıkış yap
  
  // 50Hz PWM başlat (Servo motor için standart)
  PWM1_Init(50);
  PWM1_Start();
  
  while(1) {
    // 0 derece pozisyon (1ms pulse)
    PWM1_Set_Duty(13);    // ~5% duty cycle
    Delay_ms(1000);
    
    // 90 derece pozisyon (1.5ms pulse)  
    PWM1_Set_Duty(19);    // ~7.5% duty cycle
    Delay_ms(1000);
    
    // 180 derece pozisyon (2ms pulse)
    PWM1_Set_Duty(25);    // ~10% duty cycle
    Delay_ms(1000);
  }
}
```

### Örnek 3: İki PWM Kanalı ile RGB LED Kontrolü

```c
void main() {
  // PWM pinlerini çıkış yap
  TRISC.B1 = 0;     // RC1 (CCP2) - Mavi LED
  TRISC.B2 = 0;     // RC2 (CCP1) - Kırmızı LED
  
  // Her iki PWM kanalını başlat
  PWM1_Init(1000);  // CCP1 - Kırmızı
  PWM2_Init(1000);  // CCP2 - Mavi
  PWM1_Start();
  PWM2_Start();
  
  unsigned char red_duty = 0;
  unsigned char blue_duty = 0;
  
  while(1) {
    // Kırmızı artarken mavi azalsın
    for(red_duty = 0; red_duty < 255; red_duty++) {
      blue_duty = 255 - red_duty;
      
      PWM1_Set_Duty(red_duty);   // Kırmızı parlaklık
      PWM2_Set_Duty(blue_duty);  // Mavi parlaklık
      
      Delay_ms(20);
    }
    
    Delay_ms(500);
  }
}
```

---

## PWM Hesaplamaları ve İpuçları

### Duty Cycle Hesaplama

```c
// 0-100% arası duty cycle hesaplama
unsigned char calculate_duty_percentage(unsigned char percentage) {
  return (percentage * 255) / 100;
}

// Kullanım örneği:
PWM1_Set_Duty(calculate_duty_percentage(75)); // %75 duty cycle
```

### Frekans Hesaplama Örneği

8MHz osilatör frekansı için:

```
PR2 = 199, Prescaler = 1:4
PWM_Frekans = 8,000,000 / (4 × (199 + 1) × 4) = 2.5 kHz
```

