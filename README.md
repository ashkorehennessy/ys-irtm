# STM32 HAL library for YS-IRTM IR remote control module

## Usage

Copy the irtm.c and irtm.h to your project folder(Inc and Src folders).

Configure STM32CubeMX to use USART and enable the USART interrupt in the NVIC settings.

This IR sensor baud rate is 9600 by default. You can manually change the baud rate.

User code and address are used to distinguish different devices, you can modify the user code in irtm.h file.

In my example, I used USART1 for computer communication and USART2 for the IR sensor.

Include the header file in your main.c file.

```c
/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "irtm.h"
/* USER CODE END Includes */
```

If you are using multiple sensors, declare multiple structs.

```c
/* Private variables ---------------------------------------------------------*/
/* USER CODE BEGIN PV */
IRTM_t irtm1, irtm2;
/* USER CODE END PV */
```

In the main function, initialize the sensor.

```c
  /* USER CODE BEGIN 2 */
  IRTM_Init(&irtm, &huart2);
  /* USER CODE END 2 */
```

Write the HAL_UART_RxCpltCallback function.

```c
/* USER CODE BEGIN 0 */
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart) {
    if(huart->Instance == USART2) {
        if(IRTM_Read(&irtm) != 0) {
            received = 1;
        }
    }
}
/* USER CODE END 0 */
```

In the main loop, check if the data is received, and print the received key.

```c
  /* USER CODE BEGIN WHILE */
  while (1)
  {
      if(received) {
          sprintf(buf, "Received: %c\r\n", irtm.key);
          HAL_UART_Transmit(&huart1, (uint8_t *) buf, sizeof(buf), 1000);
          received = 0;
      }
    /* USER CODE END WHILE */
}
```
