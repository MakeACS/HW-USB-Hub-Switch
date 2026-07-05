# Auto-Cleanup

This section of the repository documents an implementation of an auto-cleanup function using the USB Hub Switch's auxiliary port. 

When a session ends, this auto-cleanup will close all open windows on the computer, then display the screensaver. 

## Hardware

Any USB-capable MCU will work. In our example, we are using an [Adafruit Neo Trinkey](https://www.adafruit.com/product/4870), which is the perfect size and cost for this application. 

If using another device, make sure it fits inside the space in the enclosure; a 1.3 inch x 0.6 inch area is kept clear infront of the auxiliary USB port. Wider devices may work, but you will need to check if they clean the surrounding SMD components. Longer devices will need a different case design.

## Theory of Operation

The code is fairly simple, and executes in 4 steps;

  1. Check if we are in a development mode. At startup, we detect if a pin is high. If so, it means we were plugged in for some development purpose and should not attempt to continue (so we are not constantly closing software as we try to program the device).
  
  2. Delay for 20 seconds. There are situations where we do not want to cleanup everything on the computer, so a 20 second grace period is given for the user to re-activate the equipment, or for another user to "take over" the open windows.
  
  3. Close all open windows. This is done by emulating a keyboard, opening the Run Dialog (Win + R), and then executing the following PowerShell command;
  
    > Get-Process | Where-Object {$_.MainWindowTitle -ne ""} | Stop-Process -Force
  
  4. Force-show the screensaver. Similar to above, open the Run Dialog and execute the following;
  
    > scrnsave.scr /s

Once these 4 steps are completed, the device will suspend itself.
