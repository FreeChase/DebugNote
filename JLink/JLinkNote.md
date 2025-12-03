# J-Link、JTAG 以及 J-Link 常用调试命令整理



1. J-Link 是什么？

   J-Link 是 SEGGER 的硬件调试器，用于 ARM / RISC-V 等芯片。
   功能包括：
   - 烧录 Flash
   - 在线调试（断点、单步、继续）
   - 寄存器访问
   - 内存读写
   - RTT/SWO 调试输出
   - 通过 GDB 提供调试桥接（供 Eclipse/Vitis/Keil/IAR 等使用）

   本质：USB ↔ JTAG/SWD 调试适配器。


2. JTAG 是什么？

   JTAG = IEEE 1149.1 调试与边界扫描标准。

   ARM 中常见两种：
   1) JTAG（4~5 条线）
      - TCK, TMS, TDI, TDO, TRST(optional)
   2) SWD（2 条线）
      - SWCLK, SWDIO

   Cortex-M 一般用 SWD；
   Cortex-A / R（如 A53）用 JTAG。


3. J-Link 调试链路

   IDE/GDB → JLinkGDBServer → J-Link → JTAG/SWD → MCU/SoC

   Eclipse、Vitis 本质都是调 GDB → GDB 调 J-Link。


4. J-Link 工具套件

   - JLinkExe / JLink（Cmdline 调试器）
   - JLinkGDBServer（给 GDB 用）
   - JFlash（烧录 Flash）
   - RTT Viewer（实时调试输出）
   - Ozone（高级 GUI 调试器）


5. J-Link 常用调试命令

   以下命令用于 JLinkExe/J-Link Commander。


   5.1 启动命令
   ```bash
   JLinkExe
   或 Windows：
   JLink.exe

   使用参数直接连接：
   JLinkExe -device STM32F407VG -if SWD -speed 4000
   ```

   5.2 设置目标芯片
   ```bash
   device Cortex-M4
   device STM32F407VG
   device IMX6ULL
   ```

   5.3 连接
   ```bash
   connect
   ```

   5.4 读取内存
   ```bash
   mem32 0x20000000 10
   mem16 0x20000000 10
   mem8  0x20000000 10
   ```

   5.5 写内存
   ```bash
   w4 0x20000000 0x12345678   ; 写 32bit
   w2 0x20000000 0xABCD       ; 写 16bit
   w1 0x20000000 0xAB         ; 写 8bit
   ```

   5.6 烧录 Flash
   ```bash
   loadfile app.elf
   loadfile app.bin, 0x08000000
   ```

   5.7 复位
   ```bash
   r
   reset
   reset halt       ; 停在 reset 后
   ```

   5.8 运行
   ```bash
   go
   g
   ```

   5.9 单步
   ```bash
   step       ; step into
   stepover   ; step over
   ```

   5.10 设置 PC
   ```bash
   setpc 0x08001234
   ```
   5.11 断点
   ```bash
   bp 0x08001234
   bc 0x08001234
   bl     ; 查看断点列表
   ```

6. J-Link GDB Server 常用命令
   ```bash
   启动方式：
   JLinkGDBServer -device STM32F407VG -if SWD -speed 4000

   输出：
   GDB Server listening on port 2331

   GDB 连接：
   target remote localhost:2331
   ```

7. RTT（实时调试输出）
   ```bash
   打开 RTTViewer：
   JLinkRTTViewer.exe

   代码输出：
   SEGGER_RTT_WriteString(0, "Hello RTT\n");

   RTT 比串口快，也不占用管脚，是 J-Link 强项。
   ```

8. 常见 FAQ

   - SWD 更适用于 Cortex-M；JTAG 适合 Cortex-A 多核调试。
   - Cortex-A53（如 i.MX8）基本必须使用 JTAG。
   - Eclipse/Vitis 调试本质 = GDB + J-Link。
   - JFlash 用于专业 Flash 量产烧录。

