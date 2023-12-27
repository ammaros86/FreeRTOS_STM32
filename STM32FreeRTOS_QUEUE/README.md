# FreeRTOS Project with the Use of Queue
This project utilized an STM32L476RG microcontroller and configured it as follows:

-The SysClock was set to 80 MHz. 

-UART 2 was activated for UART data transfer.

-FreeRTOS was activated using CMSIS_V1 with default configuration.

-Three tasks, namely "SendTask1," "SendTask2," and "ReceiveTask," were created.

-The timebase source was configured to Timer1 "TIM1."

-SendTask1 and SendTask2 had normal priority, while ReceiveTask had a higher priority (osPriorityAboveNormal).

-SEGGER System View was integrated into the project.


This project serves as a demonstration of the use of queues, employed here to facilitate data transfer among tasks. SendTask1 and SendTask2 are responsible for placing values (100 or 200) into the queue, while ReceiveTask reads the written values from the queue. The queue is defined using the "osMessageQDef" method, allowing it to hold up to 32 values, each of size uint32_t. The "osMessageCreate" method creates the queue and returns "myQueue01Handle," which is used for reading from the queue.

Both SendTask1 and SendTask2 use the same method, "StartSendTask," to send data to the queue. The "xQueueSendToBack" method is utilized to place data at the back (tail) of the queue.

In the ReceiveTask, the "uxQueueMessagesWaiting" method is initially called to check whether the queue is empty. Subsequently, the "xQueueReceive" method is used to extract a uint32_t value from the tail of the queue. This method is called with a wait delay of 100 ms, causing the task to wait for a valid message in the queue before reading. This allows the send tasks sufficient time to write a value to the queue before attempting to read it.

After reading the value from the queue, the "printTaskText" method is invoked to send it over UART. The execution flow is illustrated in Figure 1, where SendTask1 and SendTask2 have lower priority than ReceiveTask. They are scheduled to run only when ReceiveTask is in a waiting state for a value to be written to the queue. Notably, the "osDelay" method is not used in this project. Both SendTask1 and SendTask2 execute for a short duration before the scheduler returns to the higher-priority task, ReceiveTask.
 
 ![Screenshot 27-12-2023 10 15 36](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/8882c2bc-bc50-4e7c-bf2e-ba03eb30e3de)

The following figure show the received Data over UART: 


![Screenshot 27-12-2023 10 15 51](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/84fbaaa0-fa8d-44a7-9541-b60c7a43e3cf)
