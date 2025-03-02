
# 🎧HyperX Cloud Flight - Avnera AV6202 Fix🎧
## Description

This tutorial aims to provide a guide for recovering **HyperX Cloud Flight** bricked headphones that, after a failed update, are no longer recognized correctly and appear as **Avnera AV6202**.

### **Important**

-   This tutorial is intended solely for **recovering devices** affected by a defective update.
    
-   No decompiled code or modified files are provided.
    
-   It is recommended to follow these steps only if you have experience with **reverse engineering and firmware**.
    
-   **I am not responsible** for any further damage to the device.
    

## **Detected Issue**

After running the official firmware updater, the headphones:

-   Stop getting detected as "HyperX Cloud Flight"and essentially, brick.
    
-   They register as **Avnera AV6202**.
    
-   The official update software **refuses to reinstall the firmware**, preventing recovery.
    

## **Proposed Solution**

The main idea is to **force the update software to recognize the headphones** as a compatible device and allow the firmware to be reinstalled correctly.

### **General Steps**

1.  **Obtain the original update software**
    
    -   The firmware installer is needed. In this case, the relevant file is:
        
        ```
        HyperX_Firmware_Updater_Cloud_Flight_3114.exe
        ```
        Get it through your own means.
        
2.  **Analyze the binary with dnSpy**
    
    -   Open the executable in **dnSpy** and locate through the assembly: 
    - `namespace: HyperXUpdater` --> `class: ShellViewModel` --> `method: Scan`
    
    - Identify the line that compares: `list.Count  ==  1`
    - Inside that if, there should be a comparison where it checks if the product name isn't `"Avnera"`
    - Apply an else statement to that if, and write:
`
else  
{  
list[0].category  =  "Headset";  
list[0].version  =  "3.1.1.3";  
list[0].fwpath  =  "USBModule.firmware.hxflight.flight_client_24C256B.ef";  
IO.HXDeviceInfo  hxdeviceInfo  =  new  IO.HXDeviceInfo("Cloud Flight",  "0951",  "1723",  "Unknown",  "3.1.1.4",  "Dongle?",  "USBModule.firmware.hxflight.flight_host_45PE10.ff");  
IO.HXDeviceInfo  hxdeviceInfo2  =  new  IO.HXDeviceInfo("Cloud Flight",  "170D",  "0101",  "Unknown",  "3.1.1.4",  "Headset?",  "USBModule.firmware.hxflight.flight_client_24C256B.ef");  
list.Clear();  
list.Add(hxdeviceInfo);  
list.Add(hxdeviceInfo2);  
}`
        
3.  **Modify device detection**
    
    -   Within the update software's code, locate the section that handles the supported hardware list.
        
    -   Add a new entry to force compatibility with the following hardware:
        
        -   **VID:**  `0x170D`
            
        -   **PID:**  `0x0101`
            
    -   Associate the correct firmware with these values so that the software recognizes them.
        
4.  **Save the changes and run the updater**
    
    -   Once the device detection is modified, save the executable and run it again.
        
    -   If the modification was successful, the program should recognize the headphones and allow firmware reinstallation.
        

## **Final Considerations**

-   This method **does not modify** the firmware itself but rather the software that installs it.
    
-   It is recommended to back up the original files before making any changes.
    
-   If the device is still not recognized, other factors may need to be checked, such as USB drivers or internal memory failures in the headphones.
    

## **Disclaimer**

This document is for educational purposes only and for recovering devices with defective firmware. Modifying proprietary software without authorization is not recommended. Follow this procedure at your own risk.
