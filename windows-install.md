## Prerequisites:
- Windows 11 or any version of Windows that supports Linux GUI Apps. I don't know if Windows 10 does or not.
- Virtualization capabilities
- 8GB of RAM or more

## Step 1 (Installing WSL2)
Install WSL2. If you have WSL2 already, you can skip this step.
Open Powershell as an administrator and run this command
```
wsl --install
```
It will prompt a reboot, reboot and let it install Ubuntu. One that is done, make a username and password. It does not have to be the same as the system.

If you do not see the window for Ubuntu

## Step 2 (Getting the files)
Open a new Ubuntu terminal window, and run this command, replacing the WINDOWS_USER_NAME with the your Windows username, not the Linux one.
```
cd /mnt/c/users/WINDOWS_USER_NAME/Documents
```
Then run
```
sudo apt install git-all -y
```
Now clone the repository
```
git clone https://github.com/notAperson535/OneClick-macOS-Simple-KVM.git
```
Now cd into the newly created folder
```
cd OneClick-macOS-Simple-KVM
```

## Step 3 (installing qemu and virtualization capabilities)
Run the included windows installer
```
./windows-install.sh
```
Once you get to the menu (looks like this)
![image](https://user-images.githubusercontent.com/95918679/152704969-29fccfab-de68-4977-b2c7-4fb5b4b5c3cb.png)
Go to Virtualization and make sure your system processor type is selected, mine is Intel
![image](https://user-images.githubusercontent.com/95918679/152704984-213b067b-1a8e-45cf-ad23-330391c31583.png)
Then keep clicking exit, and save the configuration

Once when you see the nano window pop up (it looks like a window with a lot of text), use the arrow keys to navigate to the bottom and paste this command
```
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0
```
Now do ctrl+x, and hit enter twice

Now run these commands to make sure virtualization works
```
kvm-ok
cat /sys/module/kvm_intel/parameters/nested
```
If the first command returns `KVM acceleration can be used` and the second command returns `Y`, then you are good to go

Now cd into the OneClick-macOS-Simple-KVM directory, replacing the WINDOWS_USER_NAME with the your Windows username, not the Linux one.
```
cd /mnt/c/users/WINDOWS_USER_NAME/Documents/OneClick-macOS-Simple-KVM
```
And run `./setup.sh` to finish the setup and run the macOS VM!

Whenever you want to load up the machine again, run `sudo ./basic.sh` NOT `./setup.sh` as setup.sh will reset all of your progress, and you will have to reinstall macOS.

## You're done!

If the mouse is not aligned properly, edit the basic.sh file and change `-usb -device usb-kbd -device usb-tablet \` to `-usb -device usb-kbd -device usb-mouse \` or the other way around

Never close the terminal when the QEMU window is open

To fine-tune the system and improve performance, look in the `docs` folder for more information on [adding memory](docs/guide-performance.md), setting up [bridged networking](docs/guide-networking.md)(probably does not work and is not needed), adding [passthrough hardware (for GPUs)](docs/guide-passthrough.md)(this doesn't work on WSL2 yet), tweaking [screen resolution](docs/guide-screen-resolution.md), and enabling sound features.

## Troubleshooting

### Terminal Doesn't Respond

If the terminal stops responding when you run `sudo ./basic.sh`, then follow these steps

Uninstall Ubuntu by going to the app in settings and selecting uninstall. Once that is done, go to the Microsoft Store, search Ubuntu, and install it. Once the installation is done, open it, make a new username and password, and repeat the steps in Step 3, but instead of running `./setup.sh` run `sudo ./basic.sh`

Do this every time this happens

### cat /sys/module/kvm_intel/parameters/nested returns `N`, but KVM-OK Returns `KVM acceleration can be used`

Edit these commands in .wslconfig, which is located in your Windows user direcotry, replacing the WINDOWS_USER_NAME with the your Windows username, not the Linux one.
```
nestedVirtualization=true
kernel=C:\\Users\\WINDOWS_USER_NAME\\bzImage
debugConsole=true
pageReporting=true
kernelCommandLine=intel_iommu=on iommu=pt kvm.ignore_msrs=1 kvm-intel.nested=1 kvm-intel.ept=1 kvm-intel.emulate_invalid_guest_state=0 kvm-intel.enable_shadow_vmcs=1 kvm-intel.enable_apicv=1
```
