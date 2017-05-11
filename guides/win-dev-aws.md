# Windows development machine on AWS
This outlines the steps needed to set up windows machines in AWS, and install the bare minimum requirements for development machines (notepad++, OpenSSH, gdb).
I used windows server 2008R2 SP1 64-bit, procedure for other version should be very similar.

**Important:** If you just want to use a machine already set up, like the windows 2008R2 dev machine, scroll down to "Spawning a new machine".

## Installation / First time setup
### Select country
Instances/Images are stored per country, so make sure you have selected the appropriate country.
If unsure, use Ireland.

### AWS EC2 Wizard
https://console.aws.amazon.com/ec2/v2/home
"Launch Instance"

Windows Server 2008 R2 Base (64-bit - free tier eligible) from "Advanced wizard".

Use security group win-dev-security-group if it exists, or create it: ssh option for port 22, and TCP port 3389 for RDP(Warning will tell you to do this), "Any IP" for both ports.

### Remote Desktop
Click Connect button, this will show you how to connect with Remote Desktop.
It will need the .pem file you downloaded while launching the instance to decrypt the password.
Download the RDP file, add it to your remote desktop client, edit the settings to add the password.
(I also changed resolution to 1920x1080, and disabled the full screen by default options - full screen still works)

### Settings
Control Panel -> Folder Options -> View -> Files and Folders:
  * "Hidden files and folder": Check "Show hidden files"
  * Uncheck "Hide extensions for known file types"

Control Panel -> Windows Firewall -> Turn Windows Firewall on or off
  * "Home or work...": Check "Turn off Windows Firewall"
  * "Public network": Check "Turn off Windows Firewall"

### Internet Explorer
Use Recommended security and compatibility settings when prompted.

### Install a text editor
notepad doesn't recognize unix style line endings, it's recommended to use something else.
I installed notepad++: https://notepad-plus-plus.org/download/v7.3.1.html
Add website to trusted websites when prompted.

### Download SSH
Download: https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v0.0.7.0

Right click the 32 bit download, "Save target as...", add the site to trusted sites.
If download fails because of the security pop-up or settings, just click retry or the link again.
32 bit programs should work fine with 64 bit windows.

### Install SSH

Install Win32 OpenSSH, following these instructions worked for me:

https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH

**Important:** follow the instructions closely.
It is very hard to debug if you skip something.
Make sure you are running the commands in powershell administrator mode and in the correct folder.
Note that there are 2 installers, one for sshd and one for key based auth.
The auth installer `install-sshlsa.ps1` worked for me on windows 2008 R2, I did not follow the *special* steps.
Skip the steps regarding firewall, and secure host key.

### Install gbd
Download: ftp://ftp.equation.com/gdb/64/gdb.exe

Move the exe to `C:\bin\`

### Modify PATH
Control Panel -> System -> Environment Variables -> Path -> Edit.

Ctrl+A, Ctrl+C to copy the contents, paste into a text editor to edit.

Add openssh(`C:\Program Files\OpenSSH\`) and gdb(`C:\bin\`) to path.
Copy paste back to text Environment Variable menu.
```
%SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;%systemroot%\System32\WindowsPowerShell\v1.0\;%systemroot%\System32\WindowsPowerShell\v1.0\;C:\Program Files\Amazon\cfn-bootstrap\;C:\Program Files\OpenSSH;C:\bin
```
Restart the machine, and connect using RDP again.

### Test ssh
Powershell:
```
ssh localhost
```
Paste or type password from AWS EC2 Management console (same as user and RDP password).

### Add ssh keys
`ssh-copy-id` doesn't work, windows machine doesn't have `exec`

Manually create and edit:
`C:\Users\Administrator\.ssh\authorized_keys`

Add the contents of your .pub key file.

### Disable password based ssh login
Add 3 lines to `C:\Program Files\OpenSSH\sshd_config`:
```
PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM no
```

Example file (removed commented lines):
```
AuthorizedKeysFile	.ssh/authorized_keys

PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM no

Subsystem	sftp	sftp-server.exe
PubkeyAcceptedKeyTypes ssh-ed25519*,ssh-rsa*,ssh-dss*,ecdsa-sha2*
```

### Restart ssh server daemon
Powershell:
```
net stop sshd
net start ssh
```

**Warning:** I've had some issues with key based `ssh localhost` on the vm, so don't do this to test.

### Test
You are now done with everything needed in Remote Desktop.
Restart the machine one last time.
Log in from ssh remotely, try to run gdb.

### Turn off the machine
Right click the instance -> Instance State -> Stop.
Don't terminate, this will delete everything.

### Create an image (AMI)
Right click the instance -> Create Image.
Follow the wizard.
**Important:** Save the password for the machine somewhere, this will not be retrievable from the image/copies.
After you are done terminate the instance.
Billing for the instance will only stop once you terminate, not on stop.
The associated instance volume should be deleted automatically on termination.

## Spawning a new machine
### Start the machine
The images are located in AWS EC2 Ireland, or whichever country you used in the previous step.
Go to Images -> AMIs in the AWS EC2 Console.
Click the image (win2008R2-dev) and click Launch.
Most defaults are correct, change security group to win-dev-security-group.

### IP Address
The IP Address can be found in the AWS EC2 management console.
Click the Instance to see the IP.

### SSH
To log in to the windows development vm
```
ssh Administrator@IP_Address
```
Password login is disabled for ssh, but available via Remote Desktop.
Ask someone to add your ssh key to authorized_keys if you cannot log in.

### Administrator password
Ask someone to give you the password.
You cannot retrieve the password from the AMI.
If your ssh key is added to authorized_keys you should not normally need the password.

### Terminate the machine when done (!)
Right click the instance -> Instance State -> Terminate.
The associated volume should delete automatically (when you terminate the instance).
We are billed by the hour until you terminate the machine.
If you need to save the machine for later, create an Image from the instance before terminating.
