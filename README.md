# Medical device - from idea to production

![](/images/Case_beta.png)

> [!IMPORTANT] 
> This repository contains the work done to create a diagnostic medical device model for future production. Some details were omitted to preserve intellectual property.

### Preface
This repository shows the journey from conceiving the idea for a handheld medical diagnostic device through the various stages of prototyping and development to the preparation for the full-scale production model.

### Table of contents
 
- [Introduction](#introdution)
- [Hardware](#hardware)
    - [Hand made prototypes](#handmade-prototypes)
    - [Developemnt board](#development-board)
    - [Small PCBA batch](#small-pcba-batch)
    - [Final PCBA batch](#final-pcba-batch)
    - [Case creation process](#case-creation-process)
- [Software](#sotware)
    - [Yocto attempt](#yocto-attempt)
    - [Framework selection and environment creation](#framework-selection-and-environment-creation)
    - [Initial application and hardware HAT integration](#initial-application-and-hardware-hat-integration)
    - [User interface](#user-interface)
- [Serial production preperation](#serial-production-preperation)

### Introduction
This project started as an improvement to the existing version. The newer one had to introduce a touchscreen display, wireless connectivity, and a new and more ergonomic design. Since these are major changes, they would completely make any internal components redundant; the system design started from scratch.

### Hardware
The aim was to use off the shelf components supplied from reputable companies like Raspberry Pi single board computers and capacitive displays from Waveshare. The additional functionality like battery operation and system wake were to be incorporated into the HAT board that was to be plugged into the available GPIO pins of the Raspberry Pi computer for communication and toggling of diagnostic equipment.

### Handmade prototypes
The development was done in stages. At the start, the potential subcomponents had to be tested and evaluated on the merit of cost, availability, ease of use, and compatibility with other parts. These were made into hand assembled prototypes achieving the corresponding function - charging, boost, sleep/wake.

![](/images/subsystem_boards.jpeg)
*Subsystems tested on prototype boards*

### Development board
The candidtates that were the most promising were put on to the development board as to test their ability to work together and to start the initial software development work.

During this step, the firmware for the embedded processor, working as the handler for the attached power button, power circuitry, and the status of the battery, was developed. This included serial communication with the SBC as the IIC slave device, which was needed for the retrieval and transmission of the data pertaining to the shutdown, wake, and battery charge status. The CH32V003 was selected for its deep sleep functionality, which was extensively used to extend the lifetime of the device while in the off state from 30 to over 120 days at the target battery capacity, ease of development, availability, and enough general-purpose I/O required for the needed functionality.

While testing the complete PCB, problems with heat dissipation and the logic level differences manifested themselves. The selected battery management IC was prone to excessive heating, leading to reduced performance and elongated charge times. The requirement of 5 volt power supply for the embedded processor necessitated the addition of logic level shifters between the main computer and the MCU.

![](/images/connection_diagram.png)
*Connection diagram of devices subsystems*

![](/images/IMG_0169.jpeg)
*Bring-up of the subsystems on the development board*

### Small PCBA batch
The previousily tested parts of the circuits on the development board were refined and placed in the available outline of the insides of the final handheld device.
The first fully assembled PCBs of the final dimentions were ordered in a small quantity as to test the fabricators assembly capabilities and to mitigate any further errors that might arise from the later testing.
Here, the final software of the embedded low power processor was made to include the tweaks associated with the power saving and logic while the device is in the off state and is charging at the same time, this was done in conjunction with the Raspberry Pi's wake pin configuration.
Additional elements such as the IIS speaker driver and USB 2.0 to UART converter were tested and confirmed to be functional.

![](/images/board_versions.JPG)
*Revisions of hardware during the system bringup, beginnig with the hand made prototypes to the final preassembled board*

### Final PCBA batch
The last revision of the HAT board was sent to the company specializing in the pcb manufaturing and assembly, with additional changes to the routing of traces away from the power supply electronics, and as much as it could be, the inductive loops were reduced to a minimum.

![](/images/pcb_gerber.png)
*PCB made in KiCad design suite*

![](/images/panelized_boards.jpeg)
*HAT boards before the de-panelization*

### Case creation process
The case had to have an approximate size of a payment terminal and be handheld friendly. Since the hardware inside had the minimal volume that it could fit in, including the battery, the case was iterated upon to achieve the desired effect.

![](/images/fusion_cad_model.png)
*Fusion 360 CAD model of the case and the computer assembled in a sandwich fashion*

![](/images/rev_3_4_alt.JPG)
*Two revisions of the case made in FDM and MJF technologies*

![](/images/IMG_0646.jpeg)
*The HAT board inside and outside of the casing with the SBC and display attached*

### Sotware
The creation of the software running the entire device spanned multiple fields:

* Embedded microcontrollers
* Application development in a cross-platform framework
* Driver and library development for said application
* CRUD and internal communication with processes and external devices
* Cross-compilation and simulation
* OTA (Over the Air) updates and recovery from the failed state
* UI development

### Yocto attempt
The usage of Yocto was dictated by the need for a custom linux distribution which was ultimately cancelled thanks to the short timeline for the completion of the project. The initial work was done where the entire computer with the HAT attached ran the development application as intended. The custom layer embedding the support for the display, app framework, and OTA update capability would have easily enabled further development and would alleviate the problems later caused by the use of the modified Raspbian image.

While unfortunate, the ditching of Yocto as the base system made the time otherwise spent on debugging the confguration files available for the creation and refinement of different parts of the user application.

### Framework selection and environment creation
There were multiple contenders for the creation of the application and the user interface. React native, Flutter, and Qt each had their pros and cons. The one that prevailed was Flutter thanks to its touch-first interface, cross-compatibility, ability to interface with external libraries, and the compiled nature.

The ability to run the program on the development PC and the use of the ARM compatible docker container for cross compilation sped up the developemnt iteration time. Furthermore, the compilation and upload to the final device made the testing more seamless in terms of the perception in the final form.

The same way as the main application, the embedded libraries were compiled from C++ to shared libraries separately:

* ALSA sound controller
* Display brightness controller
* Piper TTS controller

The communication via IIC with the external devices was done via the embedded Linux compatibility Flutter package.

### Initial application and hardware HAT integration
The initial application was created to test peripherals and see what the user interface might look like. This step was crucial because of its significance in connecting multiple subsystems and running them in parallel. Then the main elements to be tested were the wireless connectivity, battery life in different states of operations and from the visual side - the user interface elements size, making them most confortable for the operator.

![](/images/IMG_0549.jpeg)
*Test application running on the device in a development setting while battery capacity testing*

![](/images/IMG_0639.jpeg)
*Debugging of the internal communication via Saleae logic analyzer*

### User interface
The final user interface employed the MVVM (Model View ViewModel) paradigm and was menu based with additional screens like lockscreen and the diagnostic examination. Extensive work was done to embed the classical behavior of a smartphone, like the status bar, screen dimming, and sound volume change. Below are exemplary views that were used for testing of the devices functionality.

1 | 2 | 3 
--- | --- | ---
![](/images/mockup_menu.png) | ![](/images/battery_state.png) | ![](/images/wireless_connection.png)

### Serial production preperation
The final element of the project was the creation of the first batch of devices running the production software. These devices were to be distributed to outside parties for evaluation and later be used for the full scale production once the tests concluded successfully. 

![](/images/IMG_8463.JPG)
*Small batch of development devices going out to be tested in a production environment*
