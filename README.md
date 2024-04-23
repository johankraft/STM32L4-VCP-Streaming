# STM32L4-VCP-Streaming

This project demonstrates how to stream data at fairly high speed using the Virtual Com Port on the B-L475E-IOT01A1 board. This is driven by USART1 and provided over the STLINK USB connector. This board uses the older STLINK v2.1 which is limited to about 2 MHz transmission speed. 

WIth this demo I was able to achive 210 KB/s and with very low overhead (< 1%) thanks to using DMA transmission (offloads the CPU).

What I needed to change:
1. Follow this guide using CubeMX: https://wiki.st.com/stm32mcu/wiki/Getting_started_with_UART#UART_with_DMA
2. Make sure to enable the USART1 global interrupt in CubeMX (this is mentioned but easy to miss)
3. In Tracealyzer PSF Streaming Settings, select "Serial Port" and make sure to set the right speed - matching the settings in MX_USART1_UART_Init (main.c)

Summary of guide (see 1):
- Open the UART.ioc file in the STM32CubeIDE project.
  Under Connectivity - USART1: Add the DMA request "USART_Tx" with the following settings:
    -  Channel: "DMA1 Channel 4"
    -  Direction: "Memory to Peripheral"
    -  Priority "Low"
    -  DMA Request Settings - Mode: "Normal", Increment Address ("Memory" selected)
    -  Data width: "Byte" for both Peripheral and Memory.
  -  Under USART1 - Select NVIC settings and enable USART1 global interrupt

You should now be able to send data using HAL_UART_Transmit_DMA(&huart1, buff, size). The function returns quickly and the DMA transmission continues in the background. If calling HAL_UART_Transmit_DMA again, before the previous transmission is finished, you get return code HAL_BUSY. In that case, delay the task for a millisecond or so and try again.

The USART transmission speed is set in MX_USART1_UART_Init (main.c). It seems as 2200000 (2.2 Mbaud) is the top speed on the B-L475E-IOT01A1. This was tested with an updated STLINK v2 (the onbaord debugger on the board) that had been updated to the latest version from STM32CubeIDE v1.15. When updating it, and I  selected the firmware version "STLINK+VCP", i.e. without Mass storage in case it would cause additional load on the USB bus.

If trying to run the UART too fast settings, you may need to power-cycle the board and/or restart Tracealyzer.
