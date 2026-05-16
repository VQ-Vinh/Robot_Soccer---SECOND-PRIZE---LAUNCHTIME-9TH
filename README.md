# ⚽ Robot Soccer – ALICE

[![Launchtime 9th](https://img.shields.io/badge/Award-Second%20Prize-blue)](#)
[![STM32](https://img.shields.io/badge/MCU-STM32F103C8T6-03234B?logo=stmicroelectronics)](#)
[![Keil](https://img.shields.io/badge/IDE-Keil%20MDK--ARM-39458C)](#)
[![License](https://img.shields.io/badge/License-MIT-green)](#)

**Giải nhì** cuộc thi **Launchtime lần thứ 9** – Robot bai (soccer robot) điều khiển từ xa qua UART, sử dụng vi điều khiển STM32F103C8T6.

> **Second Prize** at **Launchtime 9th** – A UART-controlled differential-drive robot soccer car built around the STM32F103C8T6 microcontroller.

<img src="images/Robot_Soccer-MLIoT_ALICE.png" alt="Robot Soccer ALICE" width="640">

---

## ✨ Tính năng / Features

- 🚗 **Điều khiển từ xa** qua UART (9600 baud) – dùng module Bluetooth hoặc cáp serial
- ⚙️ **Vi sai 2 bánh** (differential drive) – 2 động cơ DC với driver L298N
- 🎛 **Điều chỉnh tốc độ** 8 mức (40%–100%)
- 🔦 **Đèn LED** trước & sau (bật/tắt)
- 📯 **Còi** (buzzer)
- ⏱ **Tự động dừng** sau 1 giây mất kết nối (safety timeout)
- 🔋 **Nguồn 12V DC**

---

## 🧱 Tổng quan phần cứng / Hardware Overview

| Linh kiện              | Chi tiết                              |
|------------------------|---------------------------------------|
| **Vi điều khiển**      | STM32F103C8T6 (ARM Cortex-M3, 72 MHz) |
| **Động cơ**            | 2× DC motor + driver L298N            |
| **Giao tiếp**          | USART1 (TX: PA9, RX: PA10, 9600 8N1)  |
| **Nguồn**              | 12V DC                                |
| **Đèn**                | LED trước (PB0), LED sau (PB1)        |
| **Còi**                | Buzzer (PB10)                         |
| **LED onboard**        | PC13 (active low)                     |
| **Thạch anh**          | 8 MHz HSE → PLL → 72 MHz SYSCLK       |

### Sơ đồ chân / Pin Mapping

| Chân    | Chức năng            | Kết nối               |
|---------|----------------------|-----------------------|
| PA0     | TIM2_CH1 (PWM)       | Motor trái LPWM       |
| PA1     | TIM2_CH2 (PWM)       | Motor trái RPWM       |
| PA9     | USART1_TX            | Serial / Bluetooth    |
| PA10    | USART1_RX            | Serial / Bluetooth    |
| PB0     | GPIO Output          | Đèn trước             |
| PB1     | GPIO Output          | Đèn sau               |
| PB8     | TIM4_CH3 (PWM)       | Motor phải RPWM       |
| PB9     | TIM4_CH4 (PWM)       | Motor phải LPWM       |
| PB10    | GPIO Output          | Còi                   |
| PC13    | GPIO Output          | LED onboard           |

---

## 📡 Giao thức điều khiển / Control Protocol

Gửi ký tự đơn qua UART (9600 baud, 8N1).

### Di chuyển / Movement

| Lệnh | Hành động      |
|------|----------------|
| F    | Tiến           |
| B    | Lùi            |
| L    | Quay trái      |
| R    | Quay phải      |
| G    | Tiến chéo trái |
| I    | Tiến chéo phải |
| H    | Lùi chéo trái  |
| J    | Lùi chéo phải  |
| S    | Dừng           |

### Tốc độ / Speed

| Lệnh | Tốc độ |
|------|--------|
| 0–9  | 40–94% (40 + digit × 6) |
| q    | 100%   |

### Phụ kiện / Accessories

| Lệnh   | Chức năng                   |
|--------|-----------------------------|
| `W` / `w` | Bật / tắt đèn trước       |
| `U` / `u` | Bật / tắt đèn sau        |
| `V` / `v` | Bật / tắt còi            |

### ⏱ Safety Timeout

Robot tự động dừng (`S`) sau **1 giây** không nhận được lệnh để tránh mất kiểm soát.

---

## 📂 Cấu trúc dự án / Project Structure

```
PUSH_GIT/
├── 3D_Design/                  # Thiết kế cơ khí (SolidWorks)
│   ├── Khungxe.SLDPRT          # Khung xe
│   ├── Asm1.SLDASM             # Lắp ráp tổng thể
│   └── *.STL                   # File in 3D (STL)
│
├── PCB_Design/                 # Thiết kế mạch điện (Altium Designer)
│   ├── Sch_ALICE.SchDoc        # Sơ đồ nguyên lý
│   ├── PCB_ALICE.PcbDoc        # Layout PCB
│   └── PCB_MLIoT_ALICE.PrjPcb  # Project Altium
│
├── SRC/                        # Firmware (STM32CubeMX + Keil)
│   ├── Core/
│   │   ├── Inc/                # Header files
│   │   └── Src/main.c          # 🧠 Chương trình chính
│   ├── Drivers/                # STM32 HAL Driver + CMSIS
│   └── MDK-ARM/
│       └── Controller_Car.uvprojx  # Keil project
│
└── images/                     # Hình ảnh tài liệu
```

---

## 🚀 Hướng dẫn build / Quick Start

### Yêu cầu / Requirements

- **IDE**: Keil uVision (MDK-ARM) v5.32+
- **Pack**: `Keil.STM32F1xx_DFP.2.4.1`
- **Toolchain**: ARM Compiler v6 (ARMCLANG)

### Build

1. Mở file project:  
   `SRC/MDK-ARM/Controller_Car.uvprojx`

2. Nhấn **Build** (F7) hoặc **Rebuild All**.

3. File `.hex` đầu ra tại:  
   `SRC/MDK-ARM/Controller_Car/Controller_Car.hex`

4. Nạp xuống STM32F103C8T6 bằng **ST-Link** hoặc **UART DFU**.

> ⚠️ Dự án được tạo từ **STM32CubeMX** (`Controller_Car.ioc`). Nếu cần thay đổi cấu hình ngoại vi, hãy mở file `.ioc` bằng CubeMX, chỉnh sửa và sinh code lại.

---

## 🖼 Thư viện ảnh / Gallery

| 3D CAD                          | Schematic PCB                    | PCB 3D View                      |
|---------------------------------|----------------------------------|----------------------------------|
| ![3D](images/3D.png)            | ![Schematic](images/Schematic.png) | ![PCB 3D](images/3D_PCB.png)   |

---

## 🏆 Giải thưởng / Award

**Giải nhì – Cuộc thi Launchtime lần thứ 9**  
*Second Prize – Launchtime 9th Competition*

---

## 🛠 Công nghệ sử dụng / Built With

- [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html) – MCU configuration & code generation
- [Keil MDK-ARM](https://www.keil.com/demo/eval/arm.htm) – Build & debug
- [Altium Designer](https://www.altium.com/) – PCB design
- [SolidWorks](https://www.solidworks.com/) – 3D mechanical design

---

## 📜 License

MIT License – xem file [LICENSE](LICENSE) để biết chi tiết.
