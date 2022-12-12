# rk-player-qt
Simple video player, this player is written in Qt5 and supports rockchip's gstreamer.

## Currently supported boards 
* RK3399  
NanoPC T4  
NanoPC M4  
NanoPC NEO4  
Som-RK3399  

## How to compile
```
sudo apt-get update
sudo apt-get install libpulse-dev
git clone https://github.com/friendlyarm/rk-player-qt
cd rk-player-qt/
mkdir build
cd build/
/usr/local/Trolltech/Qt-5.10.0-rk64one-sdk/bin/qmake ../player.pro
make -j$(nproc)
```
## Run on FriendlyDesktop
```
su pi
export DISPLAY=:0.0
. /usr/bin/setqt5env
./player
```
## Run on FriendlyCore
*Note: Only FriendlyCore-Bionic is supported, FriendlyCore-focal is not supported.*
```
cat >run.sh <<EOL
#!/bin/bash
. /usr/bin/setqt5env-xcb
cd /opt/qt5-player

GEOMETRY=800x1280
. /etc/os-release
if [ \${UBUNTU_CODENAME} = "focal" ]; then
    if [ -f /sys/class/graphics/fb0/modes ]; then
        GEOMETRY=\`cat /sys/class/graphics/fb0/modes | cut -d'p' -f1 | cut -d':' -f2\`
    else
        echo "Not found any display, please connect hdmi or eDP lcd."
        exit 1
    fi
else
    HDMISTATUS=\`cat /sys/class/drm/card0-HDMI-A-1/status\`
    if [ x\$HDMISTATUS = x"disconnected" ]; then
        GEOMETRY=\`cat /sys/class/drm/card0-eDP-1/mode | cut -d'p' -f1\`
    else
        GEOMETRY=\`cat /sys/class/drm/card0-HDMI-A-1/mode | cut -d'p' -f1\`
    fi
fi
startx ./player -geometry \$GEOMETRY /home/pi/demo.mp4 -- -br +bs -dpi 218
EOL

chmod 755 ./run.sh
./run.sh
```
