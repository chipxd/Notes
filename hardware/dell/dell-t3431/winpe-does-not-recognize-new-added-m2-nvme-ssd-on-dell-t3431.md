# WinPE Does Not Recognize New Added M2 NVME SSD on Dell T3431

## Problem
* Add a new M2 NVME SSD for Dell T3431
* Press F12 to Boot From a USB drive installed Win10 PE
* Open "Disk Manager" and there's no SSD found

## Root Cause
* In BIOS Settings > "System Configuration" > "SATA Operation"
* It's "RAID on" by default

## Solution
* Set "SATA Operation" to "AHCI"
* Apply and reboot

## References
* [WePE Can not Recognize M.2 NVME SSD](https://github.com/northbright/Notes/blob/8b5fa3b3bb5d0f3b950a9d2061e9dcaa6506d92b/Windows/backup-and-restore/wepe-can-not-recognize-m2-nvme-ssd.md)
