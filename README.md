# EME-EUI-SEITech Solutions-firmware-update-over-CAN-with-Gateway-ECU-STM32F103C8T6
1. [TeamMembers](#team_memebers)
2. [Project_overView](#project_over_view)
3. [MainECU](#mainECU)
4. [GatewayECU](#gatewayECU)
5. [Video_link](#video_link)

## <a name="team_memebers">**TeamMembers**</a>:
1. Youssef Khaled Ahmed (558)
2. Mark Ehab Tawfik (201)
3. Hazem Mohamed Ezzeldin (1297)
4. Azza Saeed Mohammed Gad (912)
5. MennaTullah Ahmed Farrag Hefny (116)

## <a name="project_over_view">**Project_overView**</a>:
### Sequence diagram:
![whole_program_SequenceDiagram](https://github.com/YoussefKhaledAhmed/firmware-over-CAN/assets/101673979/3b4fe281-bbbe-418d-aaae-83006fb61f52)


### Description:
The PC sends the first record when there is an update to the gateway ECU through **UART**, then the gateway ECU saves the first record and sends a message to the main ECU through **CAN** to notify it about the update when the application that is running on the main ECU is available for update it returns to the bootloader that sends an ACK to the gateway ECU which indicates the availability of the main ECU to receive the update. \
when the gateway ECU receives the ACK it sends the first record to the main ECU through **CAN** and after parsing the record it sends an ACK to the gateway ECU that sends an ACK to the PC to receive the rest of the hex file till it reaches the end-of-file record.


## <a name="mainECU">**MainECU**</a>:
### FlowChart:
![MainECU](https://github.com/YoussefKhaledAhmed/firmware-over-CAN/assets/101673979/b25c8e78-6d74-411f-ba11-43cd246e667e)

### Description:
this ECU holds **three programs** bootloader, APP_1 (first application), APP_2 (second application). As shown in the flowchart the bootloader loads whether the first application or the second application in the memory then gives it control by jumping to its reset handler.

### Bootloader design:
the bootloader design is simple as shown in the flowchart where there is a SysTick timer that is responsible for checking the allowed time to the bootloader **that is configurable**.
we have two main scenarios:
first: there is an application to be flashed.
second: there isn't any application.
the bootloader handles the first scenario by first flashing the update in the corresponding bank by checking on the address upper part. and after flashing it checks on the AppSwitch button if pressed or not then it checks on the **option byte DATA0** that holds the following:

![option byte DATA0](https://github.com/YoussefKhaledAhmed/firmware-over-CAN/assets/101673979/4500c2b1-9825-4f41-8e78-1a9d81f6f869)

according to the drawing above if the current app ID that is completely flashed is APP_2 and the **AppSwitch button** is **pressed** then jump to **APP_1** and if the button **isn't pressed** then jump to **APP_2** and vice versa.
but, ***one important note*** any jump the bootloader will make sure that the application already exists in the memory and exists correctly otherwise it **won't jump** this is done by checking on the flags that are in the option byte DATA0.

the bootloader handles the second scenario by checking on the current ID and if the button is pressed and then jumping to the corresponding app. The only difference is that there is no flashing.

## <a name="gatewayECU">**GatewayECU**</a>:
### Description:
this ECU just takes the hex file as records from the server, **PC** in our case, through **USART** then forwards the received record to the main ECU through **CAN**.


## <a name="video_link">**Video_link**</a>:
[Video link](https://youtu.be/GRoJVQK0cgg?si=4eCEZmQRkep7GVR4)
