---
title: "FreeRTOS"
description: "An introduction to FreeRTOS."
slug: "FreeRTOS"
categories:
    - Embedded Systems
    - Real-Time Systems
---

# FreeRTOS
- **Q:** Why would we need an RTOS?
- **A:** Because we want to run multiple tasks concurrently. 

- **Q:** What is an RTOS?
- **A:** An RTOS is an operating system that is designed to handle real-time tasks.


## Example
we would create task and a queue. The task would send a message to the queue and the other task would receive the message from the queue.

```c
For instance
```c
QueueHandle_t xQueue;

void setup() {
    serial.begin(9600);
    xQueue = xQueueCreate(10, sizeof(int));
    xTaskCreate(task1, "Task1", 100, NULL, 1, NULL);
    xTaskCreate(task2, "Task2", 100, NULL, 1, NULL);
}

void loop() {
    vTaskStartScheduler();
}

void LedBlink() {
    unsigned char message;
    while(1) {
        xQueueReceive(xQueue, &message, portMAX_DELAY);
        for i <message {
            digitalWrite(13, HIGH);
            delay(100);
            digitalWrite(13, LOW);
            delay(100);
        }

    }
}

void taskReceive(void *pvParameters) {
    unsigned char message;
    while(1) {
        if Serial.available() {
            int command = Serial.read();
            if command == 'A' {
                message = 5;
                xQueueSend(xQueue, &message, 0);
            } else if command == 'B' {
                message = 10;
                xQueueSend(xQueue, &message, 0);
            }
        }
    }
}    
```

