# STM32L4-VCP-Streaming

What I needed to change:
1. Follow this guide using CubeMX: https://wiki.st.com/stm32mcu/wiki/Getting_started_with_UART#UART_with_DMA
2. Make sure to enable the USART1 global interrupt in CubeMX
3. In Tracealyzer PSF Streaming Settings, make sure to set the right speed, matching the settings in MX_USART1_UART_Init (main.c)
2200000 (2.2 Mbaud) is the limit on the B-L475E-IOT01A1 it seems, using the onboard STLINK v2.1. The STLINK had been updated to the latest version (from STM32CubeIDE v1.15) and I also selected the firmware version without Mass storage, in case it would interfere.

If trying to run the UART too fast settings, you may need to power-cycle the board and/or restart Tracealyzer.
