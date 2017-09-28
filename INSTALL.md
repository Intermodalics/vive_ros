# Installation instructions

Installation instructions based on: `https://www.gamingonlinux.com/articles/first-steps-with-openvr-and-the-vive-on-linux.7229`

## Install Ubuntu 16.04 LTS.
## Install latest graphics card driver.
E.g. for `GeForce GTX 970/PCIe/SSE2` use driver version `384.69` or later. You
can find the latest Nvidia drivers on
https://launchpad.net/~graphics-drivers/+archive/ubuntu/ppa .

## Copy udev rules.
Copy the file `88-vive.rules` to the folder `/etc/udev/rules.d`. Then run:
```
sudo /etc/init.d/udev restart
```

## Connect and setup steamvr hardware to your computer.

## Install steam via ubuntu software center (if the debian package not works).
After start, steam should detect connected vr hardware and ask to install
required driver. Agree, to install steamvr. 

## Start steam and steamvr via vr icon in steam.
Steam will probably show an error messages that some i386 shared libraries are
missing. Try to install all those shared libraries in the `*:i386` version.
Afterwards, do the `Room setup` via steamvr. Close steam and reboot the
computer.

## Check installation of steamvr and connected vr hardware.
You should now see a `$HOME/.local/share/Steam` folder. The program `vrcmd`
should show a list of connected devices. 
```
$HOME/.local/share/Steam/steamapps/common/SteamVR/bin/linux64/vrcmd
```
E.g. you have a setup with:
- 2 Base stations,
- 1 Head mounted display,
- 1 HTC vive tracker.

The program `vrcmd` should show:
- HMD - generic\_hmd
- Tracker - {htc}vr\_tracker\_vive\_1\_0
- Tracking Reference - lh\_basestation\_vive
- Tracking Reference - lh\_basestation\_vive

## Create and build catkin workspace.
### Catkin version of openvr.
```
git clone git@github.com:Intermodalics/openvr.git
```
### Catkin package vive\_ros with support for htc vive tracker.
```
git clone git@github.com:Intermodalics/vive_ros.git
git checkout feature/support_htc_vive_tracker
```
### Build your workspace and copy shared libraries from steamvr.
```
catkin build
source devel/setup.bash
roscd vive_ros/scripts
sh copy_steamvr_shared_libraries.sh
```

### Start the vr server.
```
roslaunch vive_ros server_vr.launch
```

### Start the vive node.
```
roslaunch vive_ros vive.launch
```
For the setup with one tracker you should see an output like this.
```
[vive_node] Got request from device type 3 (tracker).
[ INFO] [1506597298.716160711]:  [VIVE] Number of connected devices: [6] .
[ INFO] [1506597298.716184447]:  [VIVE] Is HMD connected? 1
[ INFO] [1506597298.716192427]:  [VIVE] Is Controller connected? 1
[ INFO] [1506597298.716199488]:  [VIVE] Is Generic Tracker connected? 1
[ INFO] [1506597298.716207459]:  [VIVE] Is Generic Tracking Reference connected?0
[ INFO] [1506597298.716215603]:  [VIVE] Is Generic Display Redirect connected? 0
...
```
You should see now some `tf` topics like `/world`, `/world_vive`, `tracker1`.

### Error handling.
After starting the server, there is some processes in the background that
make problems if you restart a node.
```
vrdashboard
vrmonitor
vrcompositor
vrserver --keepalive
```
You can `kill -9` them, including the
vrserver.
```
ps asfx | grep "vr"
kill -9 [process-id]
```
