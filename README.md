# ⚡ Giải Phẫu Lỗi `Contents mismatch` (KeilC + ST-Link + STM32F103C8T6 @ 72MHz) & Chuyển Đổi STM32CubeIDE

![MCU](https://img.shields.io/badge/MCU-STM32F103C8T6-0284c7?style=flat-square&logo=stmicroelectronics)
![Core](https://img.shields.io/badge/Core-ARM_Cortex--M3_(72_MHz)-7e22ce?style=flat-square&logo=arm)
![Driver](https://img.shields.io/badge/Driver-STM32_LL_(Low--Layer)-10b981?style=flat-square)
![IDE](https://img.shields.io/badge/IDE-Keil_µVision5_%7C_STM32CubeIDE-f59e0b?style=flat-square)
![Test Cases](https://img.shields.io/badge/Verified-80_Test_Cases-e11d48?style=flat-square)

Kho lưu trữ này chứa mã nguồn thực nghiệm, bộ dữ liệu kiểm thử **80 Test Cases (`TC_01` ➔ `TC_80`)**, kết quả dịch ngược nhị phân (`.FLM` / `.dll`) và báo cáo kỹ thuật tương tác giải quyết triệt để lỗi **`Contents mismatch at: 08000000H ... Error: Flash Download failed - "Cortex-M3"`** khi nạp Flash lần thứ 2 (`F8 #2`) trên vi điều khiển **STM32F103C8T6** chạy xung nhịp **72 MHz (HSE PLL)**.

---

## 📊 Báo Cáo Kỹ Thuật Tương Tác (`Flash-STM32F103C8T6.html`)

Ngay tại thư mục gốc của dự án là tệp báo cáo tương tác độc lập **`Flash-STM32F103C8T6.html`** (không cần cài đặt thêm thư viện ngoài):
* 🔴 **Tab A (Hồ sơ Quá khứ — Khi còn bật `.dbgconf`):** Lưu lại quá trình điều tra ban đầu khi `Pack: Debug Description = Enable` âm thầm vô hiệu hóa các tùy chọn `Connect / Reset` và cách vượt rào bằng script `flash_init.ini`.
* 🟢 **Tab B (Hồ sơ Chuẩn hóa — Sau khi tắt `.dbgconf` & Ma trận 80 Test Cases):** Giải phẫu tương tác 3 bên (PC Host – ST-Link V2 – STM32), mô phỏng trực quan từng bước chạy của thuật toán `.FLM` trên SRAM (`0x20000000`) và bộ lọc tra cứu toàn bộ 80 Test Cases.

👉 **Cách xem:** Mở trực tiếp tệp `Flash-STM32F103C8T6.html` bằng bất kỳ trình duyệt web nào (Chrome, Edge, Firefox).

---

## 📂 Cấu Trúc Thư Mục Dự Án

```text
Flash-STM32F103C8T6/
├── Flash-STM32F103C8T6.html          # Báo cáo kỹ thuật tương tác (2 Tab: Quá khứ vs Chuẩn hóa 80 TC)
├── README.md                         # Tài liệu hướng dẫn sử dụng kho mã nguồn
├── .gitignore                        # Cấu hình lọc bỏ file rác biên dịch cho KeilC & CubeIDE
│
├── LED_PC13_LL/                      # [Project 1] Keil µVision5 (Sử dụng thư viện LL, 72 MHz HSE)
│   ├── DebugConfig/                  # Chứa file .dbgconf (Thủ phạm chèn ép Connect/Reset khi bật Pack)
│   ├── Drivers/                      # Thư viện CMSIS & STM32F1xx LL (Add thủ công, không phụ thuộc RTE)
│   ├── RTE/Device/STM32F103C8/       # Cấu hình thiết bị CMSIS-Pack
│   ├── User/                         # startup_stm32f103xb.s & system_stm32f1xx.c
│   ├── flash_init.ini                # Script cưỡng bức hạ xung về HSI 8 MHz trước khi .FLM chạy
│   ├── main.c                        # Mã nguồn chính cấu hình 72 MHz LL (2 Wait States chuẩn)
│   └── STM32F103C8.uvprojx           # File Project Keil µVision5
│
└── LED_PC13_LL_CUBEIDE/              # [Project 2] STM32CubeIDE (Sử dụng thư viện LL, 72 MHz HSE)
    ├── Core/                         # Chứa main.c (Đã xử lý giữ cổng SWD bằng NOJTAG), Startup, Inc/Src
    ├── Drivers/                      # Thư viện chuẩn CMSIS & STM32F1xx_HAL_Driver (LL)
    ├── LED_PC13_LL_CUBEIDE.ioc       # File cấu hình phần cứng STM32CubeMX
    └── STM32F103C8TX_FLASH.ld        # Linker Script cho trình biên dịch GNU ARM GCC