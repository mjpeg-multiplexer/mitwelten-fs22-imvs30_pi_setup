# Raspberry PI - Setup Instructions

## Setup
### Prepare SD Card
On the computer
- Install [Raspberry Pi Imager](https://www.raspberrypi.org/software/)
- Select _Raspberry Pi OS (other)_ > _Raspberry Pi OS Lite (32-bit)_
- Click _Choose SD Card_, then _Write_
- Remove, re-insert SD Card
- To enable SSH, type
    ```
    $ touch /Volumes/boot/ssh
    ```

### Prepare Raspberry Pi 3B+
On the Pi
- Insert prepared SD Card
- connect display via HDMI
- connect usb keyboard


### Setup bullseye
On the Pi
- Change the password
    ```
    $ passwd
    ```
- Change Keyboard Setting
    ```
    $ sudo raspi-config # Location Options > Change Keyboard Layout > Other > Switzerland
	$ sudo reboot
    ```	
- Expand the filesystem
    ```
    $ sudo raspi-config # Advanced Options > Expand Filesystem
    ```
- Enable the Pi Cam
    ```
    $ sudo raspi-config # Interface Options > Camera
    ```
- Increase GPU RAM
    ```
    $ sudo raspi-config > Performance Options > GPU Memory > 128 (max! or libcamera lib won't work)
    ```
    > Required for highest resolution [v2 mode](https://picamera.readthedocs.io/en/latest/fov.html#sensor-modes).
- Setup WLAN
    ```
    $ sudo raspi-config # Interface Options > W LAN
    ```
- Install git
    ```
    $ sudo apt-get update
    $ sudo apt-get install git
- Install steameye
    ```
    $ cd ~
    $ sudo apt-get install cmake libjpeg9-dev
    $ sudo apt-get install gcc g++
    $ git clone https://github.com/ccrisan/streameye.git
    $ cd streameye
    $ make
    $ sudo make install
    ```	

## Test streaming server
On the Pi
- Run a speed test
    ```
    $ cd ~/streameye
    $ mkdir images
	$ cd images
    $ wget https://github.com/backdrop-contrib/sample_animal_content/blob/1.x-1.x/images/Fox.jpg
	$ wget https://github.com/backdrop-contrib/sample_animal_content/blob/1.x-1.x/images/Panda.jpg
	$ cd ..
    $ while true; do
    for file in images/*.jpg; do
        cat $file
        echo -n "--separator--"
        sleep 0.5
    done
done | streameye -s "--separator--"
	```

## Test the Arducam (TBD) https://snfmitwelten.slack.com/archives/C01GPUC3B61/p1645694668522309
On the Pi

Setup steps on RPi 3b+ with Raspbian Bullseye:
- Follow these instructions: https://www.arducam.com/docs/cameras-for-raspberry-pi/raspberry-pi-libcamera-guide/#how-to-install-libcamera-d9f38d46-8576-43e2-9375-e225c272095f
- sdfdfdf
    ```
    $ Run sudo raspi-config --> Advanced Options --> Enable Glamor graphic acceleration and reboot
    ```
- sdfdfdf
    ```
    $ sudo nano /boot/config.txt --> add dtoverlay=vc4-kms-v3d,cma-256  and reboot.
    ```


libcamera-vid -t 0 --width 640 --height 480 --framerate 10 --inline -n --codec mjpeg  -o -| streameye

    ```
    --FrameBoundary
Content-Type: image/jpeg
Content-Length: 5689
    ```




