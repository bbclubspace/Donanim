# PIC16F887'da ADC (Analog-Digital Converter) Kullanımı
## Sunum İçeriği

- [ADC Nedir ve Nasıl Çalışır?](#adc-nedir-ve-nasıl-çalışır)
- [PIC16F887'de ADC Özellikleri](#pic16f887de-adc-özellikleri)
- [ANSEL ve ANSELH Yazmaçları](#ansel-ve-anselh-yazmaçları)
- [MicroC Pro for PIC ile ADC Kullanımı](#microc-pro-for-pic-ile-adc-kullanımı)
- [Örnek Uygulamalar](#örnek-uygulamalar)

---

## ADC Nedir ve Nasıl Çalışır?

**ADC (Analog-to-Digital Converter)** yani **Analog-Dijital Dönüştürücü**, analog sinyalleri dijital değerlere çeviren bir elektronik devredir. Mikrodenetleyiciler sadece dijital sinyalleri (0 ve 1) anlayabildiği için, sensörlerden gelen analog değerleri (gerilim, sıcaklık, ışık şiddeti vb.) okumak için ADC kullanılır.

**ADC'nin Temel Kavramları:**

- **Çözünürlük (Resolution):** ADC'nin kaç bit ile çalıştığını belirtir.
- **Referans Gerilimi (Vref):** ADC'nin maksimum ölçüm değerini belirler
- **Örnekleme Zamanı (Sampling Time):** Analog sinyalin dijital değere çevrilmesi için geçen süre
- **Dönüştürme Zamanı:** ADC'nin bir ölçüm yapması için gereken süre

**ADC Dönüştürme Formülü:**

```
Dijital_Değer = (Analog_Gerilim × 1023) / Vref

Örnek: Vref = 5V, Giriş = 2.5V ise
Dijital_Değer = (2.5 × 1023) / 5 = 511
```

**ADC Kullanım Alanları:**

- **Sensör Okumaları:** Sıcaklık, ışık, nem, basınç sensörleri
- **Potansiyometre Okuması:** Analog kontrol düğmeleri
- **Batarya Seviye Ölçümü:** Güç kaynağı izleme
- **Ses İşleme:** Mikrofon sinyallerinin dijitalleştirilmesi

---

## PIC16F887'de ADC Özellikleri

PIC16F887 mikrodenetleyicisi güçlü bir ADC modülüne sahiptir:

**ADC Özellikleri:**

- **10-bit çözünürlük:** 0-1023 arası dijital değerler (1024 seviye)
- **14 kanal:** RA0-RA5, RE0-RE2, RB2-RB5 pinleri analog giriş olarak kullanılabilir
- **Programlanabilir referans gerilimi:** VDD, VSS, harici Vref
- **Programlanabilir örnekleme hızı:** Farklı clock kaynaklarıyla hız ayarı

**ADC Kanalları:**

| Kanal | Pin | Açıklama |
|-------|-----|----------|
| AN0 | RA0 | Analog Kanal 0 |
| AN1 | RA1 | Analog Kanal 1 |
| AN2 | RA2 | Analog Kanal 2 |
| AN3 | RA3 | Analog Kanal 3 |
| AN4 | RA5 | Analog Kanal 4 |
| AN5 | RE0 | Analog Kanal 5 |
| AN6 | RE1 | Analog Kanal 6 |
| AN7 | RE2 | Analog Kanal 7 |
| AN8 | RB2 | Analog Kanal 8 |
| AN9 | RB3 | Analog Kanal 9 |
| AN10 | RB1 | Analog Kanal 10 |
| AN11 | RB4 | Analog Kanal 11 |
| AN12 | RB0 | Analog Kanal 12 |
| AN13 | RB5 | Analog Kanal 13 |

---

## ANSEL ve ANSELH Yazmaçları

### ANSEL Yazmacı 

| Bit | Pin Adı | Pin Konumu | Açıklama |
|-----|---------|------------|----------|
| 7 | ANS7 | RE2 | `1` = AN7 analog giriş, `0` = RE2 dijital I/O |
| 6 | ANS6 | RE1 | `1` = AN6 analog giriş, `0` = RE1 dijital I/O |
| 5 | ANS5 | RE0 | `1` = AN5 analog giriş, `0` = RE0 dijital I/O |
| 4 | ANS4 | RA5 | `1` = AN4 analog giriş, `0` = RA5 dijital I/O |
| 3 | ANS3 | RA3 | `1` = AN3 analog giriş, `0` = RA3 dijital I/O |
| 2 | ANS2 | RA2 | `1` = AN2 analog giriş, `0` = RA2 dijital I/O |
| 1 | ANS1 | RA1 | `1` = AN1 analog giriş, `0` = RA1 dijital I/O |
| 0 | ANS0 | RA0 | `1` = AN0 analog giriş, `0` = RA0 dijital I/O |

### ANSELH Yazmacı 

| Bit | Pin Adı | Pin Konumu | Açıklama |
|-----|---------|------------|----------|
| 7-6 | - | - | Kullanılmıyor, her zaman `0` |
| 5 | ANS13 | RB5 | `1` = AN13 analog giriş, `0` = RB5 dijital I/O |
| 4 | ANS12 | RB0 | `1` = AN12 analog giriş, `0` = RB0 dijital I/O |
| 3 | ANS11 | RB4 | `1` = AN11 analog giriş, `0` = RB4 dijital I/O |
| 2 | ANS10 | RB1 | `1` = AN10 analog giriş, `0` = RB1 dijital I/O |
| 1 | ANS9 | RB3 | `1` = AN9 analog giriş, `0` = RB3 dijital I/O |
| 0 | ANS8 | RB2 | `1` = AN8 analog giriş, `0` = RB2 dijital I/O |

### Yaygın ANSEL/ANSELH Kombinasyonları

| Yapılandırma | ANSEL | ANSELH | Açıklama |
|--------------|-------|--------|----------|
| Tüm dijital | 0x00 | 0x00 | Hiçbir pin analog değil |
| Sadece AN0 | 0x01 | 0x00 | Sadece RA0 analog |
| AN0 ve AN1 | 0x03 | 0x00 | RA0 ve RA1 analog |
| RA pinleri analog | 0x1F | 0x00 | AN0-AN4 analog (RA0-RA3, RA5) |
| RE pinleri analog | 0xE0 | 0x00 | AN5-AN7 analog (RE0-RE2) |
| RB pinleri analog | 0x00 | 0x3F | AN8-AN13 analog (RB0-RB5) |
| Tüm analog pinler | 0xFF | 0x3F | Tüm 14 analog kanal aktif |


## MicroC Pro for PIC ile ADC Kullanımı

MicroC Pro, ADC kullanımını büyük ölçüde kolaylaştıran hazır fonksiyonlar sunar.

### 1. ADC Başlatma

```c
ADC_Init();  // ADC'yi başlat (otomatik yapılandırma)
```

### 2. ADC Okuma

```c
unsigned int adc_value;
adc_value = ADC_Read(0);  // AN0 kanalından oku (0-1023 arası değer)
```

### 3. Manuel ADC Yapılandırması

```c
void manual_adc_init() {
  ANSEL = 0x01;     // ANS0 = 1, diğerleri = 0
  ANSELH = 0x00;    // Tüm RB pinleri dijital
  Delay_us(20);     // ADC stabilizasyon süresi
}
```

### 4. ADC Değerini Gerilime Çevirme

```c
float adc_to_voltage(unsigned int adc_value) {
  return (adc_value * 5.0) / 1023.0;  // 5V referans için
}
```

---

## Örnek Uygulamalar

### Örnek 1: Işık Seviyesi Ölçümü ve LED Kontrolü

```c
void main() {
  unsigned int light_level;
  unsigned char led_brightness;
  
  // Pin ayarları
  TRISA.B0 = 1;     // RA0 giriş (LDR)
  TRISC.B0 = 0;     // RC0 çıkış (LED)
  TRISC.B2 = 0;     // RC2 çıkış (PWM LED)
  
  // ADC ve PWM başlat
  ADC_Init();
  PWM1_Init(1000);  // 1kHz PWM
  PWM1_Start();
  
  while(1) {
    // Işık seviyesini oku
    light_level = ADC_Read(0);
    
    // Karanlıkta LED yanar, aydınlıkta söner
    led_brightness = 255 - (light_level / 4);  // Ters orantılı
    
    // Normal LED kontrolü
    if (light_level < 200) {
      PORTC.B0 = 1;   // Karanlıksa LED yak
    } else {
      PORTC.B0 = 0;   // Aydınlıksa LED söndür
    }
    
    // PWM LED parlaklık kontrolü
    PWM1_Set_Duty(led_brightness);
    
    Delay_ms(100);
  }
}
```



### Örnek 2: ADC ile Servo Motor Kontrolü

```c
void main() {
  unsigned int pot_value;
  unsigned char servo_duty;
  
  // Pin ayarları
  TRISA.B0 = 1;     // RA0 giriş (Potansiyometre)
  TRISC.B2 = 0;     // RC2 çıkış (Servo PWM)
  
  // ADC ve PWM başlat
  ADC_Init();
  PWM1_Init(50);    // 50Hz servo frekansı
  PWM1_Start();
  
  while(1) {
    // Potansiyometre oku (0-1023)
    pot_value = ADC_Read(0);
    
    // Servo duty cycle hesapla
    // 0° = %5 duty, 180° = %10 duty
    servo_duty = 13 + ((pot_value * 12) / 1023);
    
    // Servo pozisyonunu ayarla
    PWM1_Set_Duty(servo_duty);
    
    Delay_ms(20);
  }
}
```

---

## ADC Hesaplamaları ve İpuçları

### Gerilim Hesaplama Fonksiyonları

```c
// 5V referans için gerilim hesapla
float calculate_voltage_5v(unsigned int adc_value) {
  return (adc_value * 5.0) / 1023.0;
}

// 3.3V referans için gerilim hesapla  
float calculate_voltage_3v3(unsigned int adc_value) {
  return (adc_value * 3.3) / 1023.0;
}

// Yüzde hesapla (0-100%)
unsigned char calculate_percentage(unsigned int adc_value) {
  return (adc_value * 100) / 1023;
}
```

### Sensör Kalibrasyonu

```c
// LM35 sıcaklık sensörü için
float lm35_temperature(unsigned int adc_value) {
  float voltage = (adc_value * 5.0) / 1023.0;
  return voltage * 100.0;  // 10mV/°C
}

// Basınç sensörü örneği (0.5-4.5V arası)
float pressure_sensor(unsigned int adc_value) {
  float voltage = (adc_value * 5.0) / 1023.0;
  if (voltage < 0.5) voltage = 0.5;
  if (voltage > 4.5) voltage = 4.5;
  return ((voltage - 0.5) / 4.0) * 100.0;  // 0-100% basınç
}
```


