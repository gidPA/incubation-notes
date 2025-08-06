1. Download the latest stable OS Image from https://jenkins.formulatrix.com/job/sys-group/job/os-image/job/fros-bookworm/
2. Insert the target SD Card to a card reader, then plug the card reader.
3. Find out the SD Card's identifier by running `lsblk`
4. Flash the OS Image with the command:
```bash
xzcat <os-image-name>.raw.xz | sudo dd of=/dev/<sda> bs=4M conv=fsync status=progress
```