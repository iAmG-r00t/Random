# 0:2 iPod Antiforensics

- **Main Goal:** Building your own antiforensic disk out of an iPod by patching
  the [Rockbox framework](https://www.rockbox.org/)

&nbsp;

- The [Rockbox](./rockbox/) code is the one we are patching..
- This are just notes, for me to maybe use in future so just ignore them.

## Summary

1. USB Mass Storage is just a wrapper for SCSI.

   - SCSI means; small computer interface. It is a set of standards for
     physically connecting and transferring data between two computers and
     peripheral devices. _Source:[Wikipedia](https://en.wikipedia.org/wiki/SCSI)_
   - One can implement these protocols and make his or her own disks.

2. A legitimate host will follow the filesystem and partition data structure,
   while a malicious host will read the disk image from the beginning to the
   end.

   - There are alot of ways to distinguish hosts, but this one is the easiest
     and has the fewest false positives.

3. By overwritting its contents as it is being imaged, a disk can destroy
   whatever evidence or information the forensics investigator wishes to
   obtain.

### Exceptions

- Although one should note that there are some high end forensics software
  that will image a disk backward from the last sector toward the first.
- A law-enforcement forensics lab will never mount a volume before imaging it.
- There is also a risk that an antiforensic disk might be identified as such by
  a forensic investigator. Hence if he does he will get to bypass the defenses,
  example by reverting to the recovery ROM or read the disk directly.

## Show Notes

- Rockbox exposes its hard disk to the host through USB Mass Storage, where the
  handler functions implement each of the SCSI commands needed for that
  protocol.
- To add antiforensics, it is necessary only to hook two of these functions:
  **SCSI_READ_10** READ(10) and **SCSI_WRITE_10** WRITE(10).
- In the file [usb_storage.c](./rockbox/firmware/usbstack/usb_storage.c)
- The first function to patch is the **handle_scsi()**, near the **SCSI_READ_10** case.
- The second function to patch is the **send_and_read_next()** call.
- In both, it is important to add code to both for;
  - observe incoming requests for illegal traffic.
  - Overwrite sectors as they are requested after the disk has detected tampering. ? How does the disk detect tampering ?
- Due to code duplication, you will find that some data leaks through **send_and_read_next()** if you only patch **handle_scsi()**.
- One should also note that on an iPod, there will never be any legitimate reads over USB to firmware partition.

`check the online version for the rest of the code and where to place it.`

### To Read

- [ ] Implementation and Implications of a Stealth Hard-Drive Backdoor by Zaddach, Kurmus et al.
