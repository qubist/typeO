# Pogo behavior and software overview

## Summary

- USB-C port is not powered, works with USB-OTG if externally powered, and requires enabling in software.
- Pogo pins are powered only if USB-C is plugged in, works with USB-OTG if enabled in software, and ID pin grounded.
    - "UPDATE: It seems like you need to have it plugged in to initially work, but after you do the initial fix, it works without plugging it in." (https://github.com/dps/remarkable-keywriter/issues/14#issuecomment-758293624)
- If you wanna use a keyboard, the command echo 2 > /sys/otgcontrol/control/otg1_controllermode needs to be run on every reboot to get OTG to work. This can be done automatically. It also seems like it might need to be plugged in to power by USB-C when this command is run for it to work. 

## From the Wiki

The remarkable 2 has two USB port, one USB-C port used for charging and connecting to a PC and a second one exposed on the pogo pins.
The chip used for charging the device (max77818) only supports one VBUS with back power. This bus is connected to the pogo pins, so only this port can be used for USB OTG.

=== Pogo Pins OTG ===

The pogo pinout is:

    (left to right)  
    
    GND, ID, D+, D-, VBUS

With the VBUS closest to the USB-C port. There is a custom linux drivers that controls the USB port dr mode and VBUS power ([[https://github.com/reMarkable/linux/tree/zero-sugar/drivers/misc/rm-otgcontrol]]). The driver monitors the status of the ID pin. If it gets grounded it tries to authenticate the device before changing the port to OTG mode. This authentication is intended to work over a one-wire protocol over the ID port, but is currently unimplemented. As a result the device detection is currently disabled by default ([[https://github.com/reMarkable/linux/commit/e27d817e3c968624f7ae454f9b643157a2d39dab]]).

Luckily there is an unauthenticated mode that can be enabled by writing to sysfs:

    echo 2 > /sys/otgcontrol/control/otg1_controllermode

Now whenever the ID pin is connected to ground, the VBUS supply will be set to OTG mode and the USB mode will change to host. This allows connecting any OTG device by connecting the pogo pins to a Micro-USB connector and using an OTG cable (or creating a custom pogo cable that connects ID to ground).

SOURCE: https://remarkablewiki.com/tech/rm2_otg_pogo
