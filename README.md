# Read through this guide if you have an issue connection with device

## #1. Do you see an LED light up on the pcb a few seconds after plugging it into your usb port? This is a green led that is labeled 'GPIO 17 DEV LED'

->if it doesn't turn on, try to unplug and plug back in the pcb as well as flick the power switch. Try a few variations of this. Each time, wait a few seconds to see if the LED turns on. 

->if that still doesn't work you can try to reflash firmware with the arduino ide with this script 
https://github.com/Cerelog-ESP-EEG/ESP-EEG/blob/main/firmware/esp32_firmware.ino
Let me know if this gives you an issue connecting to the board, 
If you flash: Board: Navigate to Tools > Board > ESP32 Arduino and select 'ESP32 WROOM DA Module'. Port: Navigate to Tools > Port and select the COM port corresponding to your Cerelog board.

-> Also, your computer might be missing the driver for usb. You need the driver for CH340C IC.[https://www.silabs.com/software-and-tools/usb-to-uart-bridge-vcp-drivers ](https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers/all#drivers-if-you-need-them) Note: V2 devices will have this IC removed so you can skip this step if your PCB says V2. If it says V1.3 you will need this if you computer can't connect



## #2 If by chance you are plugging it into your computer with a usb dongle can you try plugging it straight into the port?

## #3. [If using Brainflow read this step]

I'm assuming you are running these Python test scripts with the brainflow api. Did you follow the build instructions here? (A.1 and A.2) https://www.cerelog.com/eeg_researchers_guide.html
The might be a bit different on Linux 

also

Try to run this brainflow test I have. Its usually very responsive and plots all the data. 
https://github.com/shakimiansky/Shared_brainflow-cerelog/blob/master/python_package/cerelog_tests/filtered_plot.py

## #5. [For Lunux users]

Do you have access to a windows pc or Mac? Linux often has random bugs. Make sure you download the CH340 driver.  On Linux the in-kernel drivers for it appear to have a bug that affects switching baud rates, so after compiling and installing the manufacturer's drivers it should fix this 

## #6. [Try this if still stuck]

You can try to connect to the standalone Python test script here. It's deprecated but should still be able to connect as a debug tool. https://github.com/shakimiansky/Shared_Cerlog_ESP_EEG_BCI.git
And the instructions:
https://www.cerelog.com/eeg_researchers_guide.html
Scroll down to 
Method B: Legacy Python Web Plotter (Deprecated)
