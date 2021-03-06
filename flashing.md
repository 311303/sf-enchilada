# Flashing

## NOTE on dual booting

If your current Android system is installed on slot b, you will be able to dual boot between SFOS and android with ease, and you can even run non LOS ROMs (like OOS) on slot b.

**NOTE: Android OTAs must be flashed from TWRP, with slot A as your active slot (payload-style OTAs are always flashed to the inactive slot).**

If not, you will need to switch your active slot to slot a with `fastboot --set-active=a` and then flash an update package from TWRP. If this doesn't work, you can switch the active slot by booting to TWRP and nvigating to "Reboot" -> "Slot A", which will switch the active slot to A upon next boot.

Sailfish OS will ONLY work if installed to SLOT A due to current limitations in Treble support.

#### Disclaimer: I am not responsible for any loss of data or stability due to what you decided to do to your phone.


You must be running OOS 9.x on slot A prior to following these instructions.

1. Grab [this TWRP](https://dl.twrp.me/enchilada/twrp-3.3.1-2-enchilada.img.html) then boot into fastboot mode
```sh
# update packages are always flashed to inactive slot (in this case SLOT A)
fastboot --set-active=b
fastboot boot twrp-3.3.1-2-enchilada.img
```
2. Format /data (Wipe -> Format Data)
#### Please note running SFOS with an encrypted /data partition can cause issues
3. Flash [dm-verity and forcencrypt disabler](https://zackptg5.com/android.php#disverfe) to disable encryption on SLOT B, you should flash this after flashing any OTAs to prevent data being encrypted.
4. Dirty flash Lineage OS 16.0, grab the [latest nightly from here](https://wiki.lineageos.org/devices/enchilada/).
5. Reboot back into TWRP, navigate to the reboot menu and check the current slot, if it's SLOT A then you're good to go, otherwise flash LOS again.
6. Copy the SFOS install zip to your storage and flash!
7. Reboot!
