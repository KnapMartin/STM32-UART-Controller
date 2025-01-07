# Usage
1. Include header
```
#include "uart_controller.h"
```  
1. Init a mutex, semaphore and a queue (use your own length) in global scope
```
osMutexId_t mutexUARTtxHandle;
const osMutexAttr_t mutexUARTtx_attributes = {
  .name = "mutexUARTtx"
};

osSemaphoreId_t semaphoreUARTtxHandle;
const osSemaphoreAttr_t semaphoreUARTtx_attributes = {
  .name = "semaphoreUARTtx"
};

osMessageQueueId_t queueUARTrxHandle;
const osMessageQueueAttr_t queueUARTrx_attributes = {
  .name = "queueUARTrx"
};
```
2. Create "UartController" object in global scope
```
UartController uart;
```
4. Assign UART, mutex, semaphore and queue handles to "UartController" object. Call "init" method
```
uart.setHandleUart(&huart2);
uart.setHandleMutexTx(&mutexUARTtxHandle);
uart.setHandleSemTx(&semaphoreUARTtxHandle);
uart.setHandleQueueRx(&queueUARTrxHandle);
if (uart.init() == false)
{
    Error_Handler();
}
```
5. Create a mutex, semaphore and a queue
```
mutexUARTtxHandle = osMutexNew(&mutexUARTtx_attributes);
semaphoreUARTtxHandle = osSemaphoreNew(1, 0, &semaphoreUARTtx_attributes);
queueUARTrxHandle = osMessageQueueNew (64, sizeof(uint8_t), &queueUARTrx_attributes);
```
6. Define UART interrupt callbacks in main source file
```
void HAL_UART_TxCpltCallback(UART_HandleTypeDef *huart)
{
    uart.updateInterruptTx(huart);
}

void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)
{
    uart.updateInterruptRx(huart);
}
```
Now you can use "send" and "recieve" methods from tasks.

