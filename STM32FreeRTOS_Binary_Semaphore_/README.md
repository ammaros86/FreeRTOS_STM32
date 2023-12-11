# FreeRTOS Project with Binary Semaphor  

In this project, an STM32L476RG microcontroller was used and configured as follows:

- The SysClock was set to 80 MHz.

- UART 2 was activated for UART data transfer.

- FreeRTOS was enabled using CMSIS_V1 with the default configuration.

- Three tasks, namely LowTask, MediumTask, and HighTask were created with different priorities.

- LowTask has the lowest priority "osPriorityBelowNormal", MediumTask has the medium priority "osPriorityNormal," and HighTask has the highest priority "osPriorityAboveNormal."

- The User Button connected to PC13 is utilized as a digital input.

- The timebase source was configured to Timer1 "TIM1."

- SEGGER System View was integrated into the project.

- FreeRTOS is configured to use preemption scheduling, and a binary semaphore named "BinarySemaphore" is initialized.

This project aims to demonstrate the use of binary semaphores and their advantages. 

Initially, as indicated in the following figure, no semaphore is used, and the button is not utilized. The three tasks are executed sequentially, with the scheduler allowing 
the HighTask with higher priority to run first, followed by the MediumTask, and finally the LowTask. This sequence repeats. Each task sends its text via the UART interfaces 
and is blocked for 20 systicks.

![Screenshot 11-12-2023 14 24 22](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/a8bd8b9f-7bb9-4e34-b469-9a3da18c91e8)

Expanding on the LowTask: After transmitting the text "LOW Task entered" over the UART interface, the LowTask enters a continuous loop, awaiting the activation of the User Button. 
Initially, the HighTask is executed, followed by the LowTask. Since the button has not been pressed during the execution of the LowTask, it continues to run without entering the
blocked state. This is because the osDelay() function cannot be invoked. However, as illustrated in the figure, the LowTask is subject to interruption by the HighTask and MediumTask.

![Screenshot 11-12-2023 14 25 24](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/a0730826-6fb5-4284-a05b-2e0a28f15be9)

Upon pressing the button, the LowTask completes its execution and enters the blocked state. Subsequently, the three tasks are executed consecutively in a minimal time span. 
The time between the execution of the three tasks represents the Idle Task, visible in the figure. This period, where no task is actively running, occurs only after a button press. 
Following this, the loop in the LowTask reverts to blocking its function until the button is pressed again. Once the button is not pressed again, the LowTask can no longer be blocked, and 
there is no further occurrence of an Idle Task.

![Screenshot 11-12-2023 14 47 16](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/945ff93e-5808-44d3-9a96-61c954513bbd)

To prevent the HighTask and MediumTask from proceeding before the LowTask completes its task, the binary semaphore comes into play, referred to here as priority inversion. With the use 
of semaphores, the HighTask and MediumTask cannot execute while the LowTask is in its continuous loop waiting for the button press. The created semaphore "BinarySemaphore" is first defined 
using the function "osSemaphoreDef()" and then created using the function "osSemaphoreCreate()". This function returns a semaphore ID, "BinarySemaphoreHandle". The semaphore is managed with
two functions, "osSemaphoreWait()" and "osSemaphoreRelease()". The "osSemaphoreWait()" function is called to wait for the availability of the semaphore. If the semaphore is available, the 
remaining code of the task is executed; otherwise, it waits for the semaphore to be released. The "osSemaphoreRelease()" function is called to release the semaphore for other tasks.

In this project, these two functions are called in each of the three tasks. In each task, the text "X Task entered" is first printed, where "X" represents "LOW," "Medium," or "High." 
When the LowTask calls the "osSemaphoreWait()" method, it blocks the execution of the other tasks. However, the "High" and "Medium" tasks are only executed for a very short time and transmit 
the messages "High Task entered" and "Medium Task entered" until the "osSemaphoreWait()" function is called, after which these tasks are blocked until the button is pressed, and the 
LowTask releases the semaphore.

The following figure indicates that the three tasks are initially called sequentially based on priorities. After that, the LowTask with the semaphore blocks the other two tasks.
After 20 SysTicks have elapsed, the HighTask attempts to take over resources, sends its initial text, and is immediately blocked. 

![Screenshot 11-12-2023 14 52 28](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/e37f5be1-294c-46df-a85d-eb9132bd15b9)

The MediumTask is executed similarly to the HighTask, which was not shown in the previous figure. The subsequent figure zooms in to show the execution of the MediumTask. 
After these two attempts by the High and Medium tasks to execute, only the LowTask continues to work and wait for the button press, while the other two tasks are in a blocked state.

![Screenshot 11-12-2023 14 53 45](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/c149b586-a0f4-4ee3-9aff-398052c59d8e)


After the button is pressed, the semaphore is released by the LowTask, resulting in the ability for all tasks to be executed. This occurs multiple times while the button is pressed (see the following figure). 
Once the button is released, the LowTask again blocks with the semaphore, preventing the other tasks from executing. The use of a semaphore in this example clearly demonstrates its effectiveness as a solution
to synchronize the three tasks for this purpose, ensuring that all tasks are executed. However, it leads to a phenomenon known as priority inversion, where the High and Medium tasks can no longer take resources 
and execution time for themselves. Instead, they must wait for the LowTask to complete its function.

![Screenshot 11-12-2023 14 57 30](https://github.com/ammaros86/FreeRTOS_STM32/assets/56800295/c6776625-f8e6-4d5b-b115-d63f87de1340)

