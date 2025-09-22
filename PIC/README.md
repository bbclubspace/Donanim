# PIC16F877 Mikrodenetleyici Başlangıç Rehberi

## 📋 İçindekiler

- [PIC16F877 Hakkında](#pic16f877-hakkında)
- [Teknik Özellikler](#teknik-özellikler)
- [Pin Diyagramı ve Açıklamaları](#pin-diyagramı-ve-açıklamaları)

## PIC16F877 Hakkında

PIC16F877, Microchip Technology tarafından üretilen, yüksek performanslı ve tamamen statik çalışan 8-bit CMOS bir mikrodenetleyicidir. Özellikle eğitim amaçlı projeler ve orta seviye uygulamalar için ideal olan bu mikrodenetleyici, geniş çevre birimi desteği ve kolay programlanabilirlik özellikleriyle öne çıkar.


## Teknik Özellikler

| Özellik | Değer |
|---------|--------|
| **İşlemci** | 8-bit RISC mimarisi |
| **Çalışma Frekansı** | DC - 20 MHz |
| **Program Belleği** | 8K x 14 words Flash |
| **Veri Belleği (RAM)** | 368 bytes |
| **EEPROM** | 256 bytes |
| **I/O Pinleri** | 33 adet |
| **Timer/Counter** | 3 adet (Timer0, Timer1, Timer2) |
| **PWM Kanalları** | 2 adet |
| **ADC** | 10-bit, 16 kanal |
| **USART** | 1 adet |
| **SPI** | Evet |
| **I2C** | Evet |
| **Çalışma Voltajı** | 2.0V - 5.5V |
| **Akım Tüketimi** | < 2 mA @ 5V, 4 MHz |
| **Paket Tipi** | 40-pin PDIP/PLCC/TQFP |

## Pin Diyagramı ve Açıklamaları



```
        
                      ┌─────────┐
     🔷RE3/MCLR/Vpp ──┤1     40├── RB7/ICSPDAT🟣
 🟢RA0/🟠AN0/🔷ULPWU ┤2     39├── RB6/ICSPCLK🟣
        🟢RA1 /🟠AN1──┤3     38├── RB5/🟠AN13/🔵T1G
     🟢RA2/🟠AN2/Vref ┤4     37├── RB4/🟠AN11🟢
  🟢RA3/🟠AN3/Vref+ ──┤5     36├── RB3/🟠AN9/🔷PGM
      🟢RA4/🔵T0CKI ──┤6     35├── RB2/🟠AN8🟢
   🟢RA5/🟠AN4/🟣SS ──┤7     34├── RB1/🟠AN10🟢
        🟢RE0/🟠AN5 ──┤8     33├── RB0/🟠AN12/🔷INT
        🟢RE1/🟠AN6 ──┤9     32├── Vdd🔴
        🟢RE2/🟠AN7 ──┤10    31├── Vss🔴
               🔴Vdd ──┤11    30├── RD7/🔵P1D🟢
               🔴Vss ──┤12    29├── RD6/🔵P1C🟢
        🟢RA7/🟡OSC1 ──┤13    28├── RD5/🔵P1B🟢
        🟢RA6/🟡OSC2 ──┤14    27├── RD4🟢
       🟢RC0/🔵T1OSI ──┤15    26├── RC7/🟣RX🟢
        🟢RC1/🔵CCP2 ──┤16    25├── RC6/🟣TX🟢
        🟢RC2/🔵CCP1 ──┤17    24├── RC5/🟣SDO🟢
         🟢RC3/🟣SCK ──┤18    23├── RC4/🟣SDI🟢
                🟢RD0 ──┤19    22├── RD3🟢
                🟢RD1 ──┤20    21├── RD2🟢
                        └─────────┘
```

## Renk Açıklamaları

| Emoji | Renk | Pin Tipi | Açıklama |
|-------|------|----------|----------|
| 🟢 | Yeşil | GPIO | Genel amaçlı giriş/çıkış pinleri |
| 🟠 | Turuncu | Analog | ADC, karşılaştırıcı, referans voltaj |
| 🔵 | Mavi | Timer | Timer girişleri, CCP, PWM çıkışları |
| 🟣 | Mor | İletişim | UART, SPI, I2C, ICSP |
| 🔴 | Kırmızı | Güç | Vdd, Vss güç pinleri |
| 🟡 | Sarı | Osilatör | OSC1, OSC2, saat sinyalleri |
| 🔷 | Mavi Elmas | Özel | MCLR, INT, PGM sistem pinleri |


### Port Açıklamaları

#### PORTA (RA0-RA5)
- **RA0-RA3**: Analog/Digital I/O pinleri
- **RA4**: Open-drain çıkış, Timer0 clock girişi
- **RA5**: Digital I/O, Master Clear

#### PORTB (RB0-RB7)
- **RB0**: External interrupt pin (INT)
- **RB1-RB7**: Digital I/O pinleri
- **RB6, RB7**: In-Circuit Serial Programming pinleri

#### PORTC (RC0-RC7)
- **RC0-RC2**: Digital I/O pinleri
- **RC3-RC5**: SPI interface pinleri
- **RC6**: USART TX pin
- **RC7**: USART RX pin

#### PORTD (RD0-RD7)
- **RD0-RD7**: Digital I/O / Paralel Slave Port

#### PORTE (RE0-RE2)
- **RE0-RE2**: Digital I/O / Analog input pinleri

### Güç ve Osilatör Pinleri
- **Vdd (11, 32)**: Pozitif güç kaynağı (+5V)
- **Vss (12, 31)**: Negatif güç kaynağı (GND)
- **MCLR (1)**: Master Clear (Reset) pin
- **OSC1 (13)**: Osilatör kristal/RC giriş
- **OSC2 (14)**: Osilatör kristal çıkış



