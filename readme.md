
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

The main idea is to **force the update software to recognize the bricked headphones/dongle** as a compatible device and allow the firmware to be reinstalled correctly.

### **General Steps**

1.  **Obtain the original update software**
    
    -   The firmware installer is needed. In this case, the relevant file is:
        
        ```
        HyperX_Firmware_Updater_Cloud_Flight_3114.exe
        ```
        It is important to get this version. Other versions will not work through this method. 
        Get it through your own means.
        
2.  **Analyze the binary with dnSpy and write the modification**
    
    -   Open the executable in **dnSpy** and locate through the assembly: 
    - `namespace: HyperXUpdater` --> `class: ShellViewModel` --> `method: Scan`
     - Identify the line that compares: `else if(list.Count  ==  2)`. Delete the "else". This is important as the next step will be tricking the installer into reading our own list of two devices.
    - Identify the line that compares: `else if (list.Count  ==  1)`
    - Inside that if, there should be a comparison where it checks `if (list[0].product != "Avnera")`
    - Apply an else statement to that if, and write:

```csharp
else
{
    IO.HXDeviceInfo hxdeviceInfo = new IO.HXDeviceInfo("Cloud Flight", "DONGLEVID", "DONGLEPID", "Unknown", "3.1.1.4", "Dongle?", "DONGLEFIRMWARE");
    IO.HXDeviceInfo hxdeviceInfo2 = new IO.HXDeviceInfo("Cloud Flight", "HEADSETVID", "HEADSEPID", "Unknown", "3.1.1.4", "Headset?", "HEADSETFIRMWARE");
    list.Clear();
    list.Add(hxdeviceInfo);
    list.Add(hxdeviceInfo2);
}
```
3.  **Modify device detection**
    
    -   In the previous step you wrote an else, complete `DONGLEVID`, `DONGLEPID`, `HEADSETVID`, `HEADSEPID` with your respective usb PID and VIDS.
    - Search in dnSpy "firmware.hxflight". You'll find a method that implements several strings with the firmware path needed. Complete `DONGLEFIRMWARE`and `HEADSETFIRMWARE` with the respective firmware of each device.
4.  **Fixing only one of the devices**
    
    It's highly possible that you only have bricked one of the devices. In my case it was the headset. What you have to do next is to search in the same `ShellViewModel`class that we were writing on and detect the method that updates the device that we DON'T want to update. 
    Ex. If your headset is detected as Avnera AV6202, search for `UpdateDongle`and replace the method implementation with:
     `await  Task.CompletedTask;`
     This will make so the method is completely ignored of the update process, so it will skip it.
        
## Force Installing the firmware ##
As you compiled and ran the modified software., it will give you the option to update. In my own experience it always throws an error at the end, but it worked to fix my headphones.

## **Final Considerations**

-   This method **does not modify** the firmware itself but rather the software that installs it.
    
-   It is recommended to back up the original files before making any changes.
    
-   I don't/won't provide support for this process, as it is pretty straightforward if you have the necessary knowledge. 
    

## **Disclaimer**

This document is for educational purposes only and for recovering devices with defective firmware. Modifying proprietary software without authorization is not recommended. Follow this procedure at your own risk.
