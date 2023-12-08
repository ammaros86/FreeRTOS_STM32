# FreeRTOS Project with Cooperative-Based Scheduling

In this project, an STM32L476RG microcontroller was utilized and configured as follows:

- The SysClock was set to 80 MHz.

- UART 2 was activated for UART data transfer.

- FreeRTOS was activated using CMSIS_V1 with the default configuration.

- Three tasks, namely "task1" and "task2," were created with the same priority "osPriorityNormal."

- The timebase source was configured to Timer1 "TIM1."

- SEGGER System View was integrated into the project.

- The FreeRTOS Configuration File "FreeRTOSConfig" was modified, with configUSE_PREEMPTION set to 0, indicating that preemption scheduling will not be used.

This project aims to demonstrate cooperative scheduling. As mentioned earlier, two tasks were initialized with the same priorities. Since preemption scheduling
is disabled, cooperative scheduling is automatically activated. Other tasks can only execute when the "Running" status transitions to the blocked status or when 
tasks are explicitly and manually instructed to enter the blocked status by calling the "taskYIELD()" function. As we use the "CMSIS-RTOS API" in this project, we
can invoke the "taskYIELD()" method by calling the API method "osThreadYield()," which, in turn, calls the "taskYIELD()" method.

In this example project, Task1 invokes the "printTaskText()" method seven times consecutively to transmit the text "Task 01 \n" over UART. After that, Task1 calls 
the "osThreadYield()" method to execute Task2 and enters the blocked status. Task1 calls the "printTaskText()" method only once to transmit the text "Task 02 \n" 
over UART and then calls the "osThreadYield()" method, entering the blocked state and allowing Task1 the opportunity to start in the running status.

The following image from SEGGER System View clearly indicates that Task1 has a longer execution time than Task2, and the tasks cooperate to determine which one executes first.

![Screenshot 08-12-2023 12 53 11](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/afd3adcb-accf-41a9-9626-4be1230a18f4)

The subsequent image shows the data received via the UART interface, demonstrating that Task1 is executed seven times consecutively before Task2 is executed.

![Screenshot 08-12-2023 12 53 54](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/2899807f-2cda-4eb7-8922-25437be6dbaa)






