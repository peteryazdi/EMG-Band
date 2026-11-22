# Control board pin map (draft)

MCU: STM32WB55RGV6, VFQFPN68 package (8 x 8 mm, 0.4 mm pitch), 49 GPIO.
Pin numbers and alternate functions are from ST datasheet DS11929, Table 16. Check every assignment in STM32CubeMX before the schematic is finalized.

All 49 GPIO are accounted for: 33 are used on the board and the other 16 go to the expansion header.

## ADS1299 (EMG front end) on SPI1

| MCU pin | Name | Function | ADS1299 pin |
|---|---|---|---|
| 19 | PA4 | GPIO output (chip select) | CS |
| 20 | PA5 | SPI1_SCK | SCLK |
| 21 | PA6 | SPI1_MISO | DOUT |
| 22 | PA7 | SPI1_MOSI | DIN |
| 18 | PA3 | GPIO input, interrupt (EXTI3) | DRDY |
| 17 | PA2 | GPIO output | START |
| 16 | PA1 | GPIO output | RESET |
| 15 | PA0 | GPIO output | PWDN |

ADS1299 pins not connected to the MCU:

| ADS1299 pin | Connection |
|---|---|
| CLKSEL | Tied high to DVDD (internal 2.048 MHz oscillator) |
| CLK | Test pad |
| GPIO1 to GPIO4 | Test pads |
| SRB1, SRB2 | Test pads (not used for differential channels) |
| BIASOUT | Reference electrode, through a series resistor |
| BIASINV, BIASREF | Bias amplifier feedback network (see datasheet) |
| AVDD, AVDD1 | +2V5_A |
| AVSS, AVSS1 | −2V5_A |
| DVDD | +3V3 |
| VREFP, VREFN, VCAP1 to VCAP4 | Capacitors as in the datasheet |
| IN1P to IN8N | Electrode inputs through the input filters |

## LSM6DSV16X (IMU) on SPI2

| MCU pin | Name | Function | IMU pin |
|---|---|---|---|
| 46 | PB12 | GPIO output (chip select) | CS |
| 47 | PB13 | SPI2_SCK | SCL/SPC |
| 48 | PB14 | SPI2_MISO | SDO |
| 49 | PB15 | SPI2_MOSI | SDA/SDI |
| 50 | PC6 | GPIO input, interrupt (EXTI6) | INT1 |
| 58 | PC10 | GPIO input, interrupt (EXTI10) | INT2 |

## Battery management

| MCU pin | Name | Function | Connects to |
|---|---|---|---|
| 6 | PB8 | I2C1_SCL | MAX17048 SCL |
| 7 | PB9 | I2C1_SDA | MAX17048 SDA |
| 59 | PC11 | GPIO input, interrupt (EXTI11) | MAX17048 ALRT |
| 60 | PC12 | GPIO input | BQ24074 CHG (charging status) |
| 61 | PD0 | GPIO input | BQ24074 PGOOD (USB power present) |
| 62 | PD1 | GPIO output | BQ24074 EN1 (input current limit) |
| 40 | PE4 | GPIO output | BQ24074 EN2 (input current limit) |
| 39 | PB1 | GPIO output | BQ24074 CE (charge enable) |

EN1, EN2 and CE also get pull resistors so the charger starts in a safe default (500 mA input limit, charging enabled) before the MCU boots.

## USB, debug, clocks and radio

| MCU pin | Name | Function | Connects to |
|---|---|---|---|
| 52 | PA11 | USB_DM | USB-C D− (through ESD array) |
| 53 | PA12 | USB_DP | USB-C D+ (through ESD array) |
| 54 | PA13 | SWDIO | SWD pad |
| 56 | PA14 | SWCLK | SWD pad |
| 63 | PB3 | SWO (trace output) | SWD pad |
| 8 | NRST | Reset | RESET button, SWD pad |
| 5 | PH3-BOOT0 | Boot select | BOOT0 button with pull-down |
| 3 | PC14-OSC32_IN | LSE | 32.768 kHz crystal |
| 4 | PC15-OSC32_OUT | LSE | 32.768 kHz crystal |
| 35 | OSC_IN | HSE | 32 MHz crystal |
| 34 | OSC_OUT | HSE | 32 MHz crystal |
| 31 | RF1 | Radio | MLPF-WB55-01E3 filter, then antenna |

## User interface

| MCU pin | Name | Function | Connects to |
|---|---|---|---|
| 2 | PC13 | GPIO input, interrupt (EXTI13) | USER button |
| 38 | PB0 | GPIO output | STATUS 1 LED |
| 27 | PB2 | GPIO output | STATUS 2 LED |

The POWER LED runs from +3V3 and the CHARGE LED from the BQ24074 CHG pin, so neither uses an MCU pin.

## Expansion header (16 spare GPIO)

Proposed: 2 x 10 header at 1.27 mm pitch with the 16 pins below, plus +3V3 and three GND pins.

| MCU pin | Name | Useful alternate functions |
|---|---|---|
| 23 | PA8 | TIM1_CH1, MCO, ADC1_IN15 |
| 24 | PA9 | USART1_TX, I2C1_SCL, SPI2_SCK, ADC1_IN16 |
| 51 | PA10 | USART1_RX, I2C1_SDA |
| 57 | PA15 | TIM2_CH1, SPI1_NSS |
| 64 | PB4 | I2C3_SDA, USART1_CTS |
| 65 | PB5 | USART1_CK, LPUART1_TX |
| 66 | PB6 | USART1_TX, I2C1_SCL |
| 67 | PB7 | USART1_RX, I2C1_SDA |
| 28 | PB10 | I2C3_SCL, LPUART1_RX, TIM2_CH3 |
| 29 | PB11 | I2C3_SDA, LPUART1_TX, TIM2_CH4 |
| 9 | PC0 | I2C3_SCL, LPUART1_RX, ADC1_IN1 |
| 10 | PC1 | I2C3_SDA, LPUART1_TX, ADC1_IN2 |
| 11 | PC2 | ADC1_IN3 |
| 12 | PC3 | ADC1_IN4 |
| 25 | PC4 | ADC1_IN13 |
| 26 | PC5 | ADC1_IN14 |

This gives a spare UART (USART1 on PB6/PB7 or PA9/PA10), a spare I2C bus (I2C3 on PB10/PB11 or PC0/PC1), timer outputs for PWM, and six analog inputs.

## Power pins

| MCU pin | Name | Connection |
|---|---|---|
| 30, 45, 68 | VDD | +3V3 |
| 1 | VBAT | +3V3 |
| 14 | VDDA | +3V3 through a ferrite bead |
| 13 | VREF+ | VDDA |
| 55 | VDDUSB | +3V3 |
| 33 | VDDRF | +3V3 |
| 32 | VSSRF | GND |
| 44 | VDDSMPS | +3V3 |
| 43 | VLXSMPS | SMPS inductor |
| 41 | VFBSMPS | SMPS output (see datasheet) |
| 42 | VSSSMPS | GND |
| 36, 37 | AT0, AT1 | Reserved, connect as the datasheet says |
| Exposed pad | VSS | GND |
