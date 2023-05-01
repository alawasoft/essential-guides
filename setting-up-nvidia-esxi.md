Here's a summary of the steps to configure an NVIDIA GPU in an Ubuntu VM running on VMware ESXi:

1. Disable the Nouveau kernel driver:
   ```
   sudo nano /etc/modprobe.d/blacklist-nouveau.conf
   ```
   Add the following lines:
   ```
   blacklist nouveau
   options nouveau modeset=0
   ```
   Update the initramfs and reboot:
   ```
   sudo update-initramfs -u
   sudo reboot
   ```

2. Remove the Intel Microcode package:
   ```
   sudo dpkg -l | grep intel
   sudo apt purge intel-microcode
   sudo update-grub
   sudo shutdown -h 0
   ```

3. Add `hypervisor.cpuid.v0=FALSE` to the VM Configuration Parameters (VM options ➝ Advanced ➝ Configuration Parameters).

4. Update Ubuntu and install required packages:
   ```
   sudo apt update
   sudo apt install gcc make build-essential libglvnd-dev pkg-config
   ```

5. Download the appropriate NVIDIA driver for your GPU from the NVIDIA website.

6. Stop the X server and switch to a text console:
   ```
   sudo service lightdm stop
   sudo init 3
   ```

7. Make the NVIDIA driver installer executable and run it with the `-m=kernel-open` parameter:
   ```
   chmod +x file_name.run
   sudo su
   ./file_name.run -m=kernel-open
   ```
   Choose the default options during the installation.

8. Check for the existence of the `nvidia.conf` file in `/etc/modprobe.d/`. If it doesn't exist, create it and add the following line:
   ```
   options nvidia NVreg_OpenRmEnableUnsupportedGpus=1
   ```
   To create and edit the file, use:
   ```
   sudo nano /etc/modprobe.d/nvidia.conf
   ```

9. Reboot the virtual machine.

10. Check the GPU status and driver version using `nvidia-smi`.

This process should enable the NVIDIA GPU in your Ubuntu VM running on VMware ESXi.