# FreeRTOS Project with Priority-Based Scheduling
This project utilized an STM32L476RG microcontroller and configured it as follows:

-The SysClock was set to 80 MHz. 

-UART 2 was activated for UART data transfer.

-FreeRTOS was activated using CMSIS_V1 with default configuration.

-Three tasks, namely "task1," "task2," and "task3," were created.

-The timebase source was configured to Timer1 "TIM1."

-Task 1 had below-normal priority (osPriorityBelowNormal), Task 2 had normal priority (osPriorityNormal), and Task 3 had real-time priority (osPriorityRealtime).

-SEGGER System View was integrated into the project.


This project serves as a demonstration of the straightforward nature of priority-based scheduling. In figure 1 from SEGGER System View, the SysTick interrupt, highlighted
in red, occurs at a frequency of 1 ms. Task 3, with the highest priority, enters the "Ready" state first, prompting the scheduler to initiate its execution. After completing 
its designated operations, Task 3 enters a blocked state for 3 ms using the osDelay(3) method. The image illustrates that Task 3 restarts after every 3 SysTick 
interrupts, corresponding to a 3 ms interval.

![Screenshot 05-12-2023 12 55 00](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/973d16f2-4f5c-4cae-a27b-5dfdaa63405e)


While Task 3 is active, Task 2 remains in the "Ready" state (see figure 2), awaiting the scheduler's signal to commence execution. After Task 3 finishes its job and enters the "Blocking" state,
the scheduler starts Task 2. During Task 2 execution, a SysTick interrupt occurs, and Task 1 enters the "Ready" state. After Task 2 completes its job and enters the blocking state
for 5 ms, the scheduler starts Task 1, which can only begin if no other task is in the "Ready" state.

![Screenshot 05-12-2023 12 56 31](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/1f14529b-e010-4ecd-a246-bc862b88dc7c)

All tasks utilize the printTaskText method to send UART messages. After Task 1 finishes its job, it is blocked for 5 ms, and the Idle State is invoked until a task enters the "Ready" state (see figure 3).

![Screenshot 05-12-2023 12 58 27](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/ba89b592-7fc7-46ed-a766-a2f078f14861)

This entire process repeats itself. In this project, the SysTick occurs every 1 ms due to the FreeRTOS configuration. During a SysTick interrupt, a task enters the "Ready" state, patiently
waiting for the scheduler to decide which task to start next.
