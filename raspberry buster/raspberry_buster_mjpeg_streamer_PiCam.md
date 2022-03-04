# Raspberry PI - Setup Instructions

## Setup
### Prepare SD Card
On the computer
- Install [Raspberry Pi Imager](https://www.raspberrypi.org/software/)
- Select _Raspberry Pi OS (other)_ > _Raspberry Pi OS Lite (Legacy)_
- Click _Choose SD Card_, then _Write_
- Remove, re-insert SD Card
- To enable SSH, type
    ```
    $ touch /Volumes/boot/ssh
    ```
- Alternative (via command line)
    ```
    $ sudo raspi-config # Advanced Options > Enable SSH
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
- Enable the Ardu Cam (https://snfmitwelten.slack.com/archives/C01GPUC3B61/p1645694668522309)
    ```
    $ sudo raspi-config # Interface Options > Camera
    ```
- Increase GPU RAM
    ```
    $ sudo raspi-config > Performance Options > GPU Memory > 256
    ```
    > Required for highest resolution [v2 mode](https://picamera.readthedocs.io/en/latest/fov.html#sensor-modes).
- Setup WLAN
    ```
    $ sudo raspi-config # System Options > W LAN
    ```
- Install git
    ```
    $ sudo apt-get update
    $ sudo apt-get install git
- Install mjpeg streamer https://github.com/jacksonliam/mjpg-streamer
    ```
    $ cd ~
    $ sudo apt-get install cmake libjpeg9-dev
    $ sudo apt-get install gcc g++
    $ git clone https://github.com/jacksonliam/mjpg-streamer.git
	$ cd mjpg-streamer
	$ cd mjpg-streamer-experimental
	$ make
	$ sudo make install
    $ make
    $ ./mjpg_streamer
    ```	
	
## Test streaming server
On the Pi
- Install
    ```
    $ cd ~/mjpg-streamer/mjpg-streamer-experimental
    $ mkdir images
	$ cd images
    $ wget https://github.com/backdrop-contrib/sample_animal_content/blob/1.x-1.x/images/Fox.jpg
	$ wget https://github.com/backdrop-contrib/sample_animal_content/blob/1.x-1.x/images/Panda.jpg
	$ mv Fox.jpg fox.jpg
	$ mv Panda.jpg panda.jpg
	$ cd ..
    $ mjpg_streamer -i 'input_file.so  -f /home/pi/mjpg-streamer/mjpg-streamer-experimental/images/ --existing' -o 'output_http.so -w /home/pi/mjpg-streamer/mjpg-streamer-experimental/www -p 8080'
	```

## Test the Pi cam (TBD)
On the Pi
- Take a picture (add `-hf -vf` to flip it)
    ```
    $ raspistill -o ./cam.jpg
    ```
- <s>Record a video (add `-hf -vf` to flip it)</s>
    ```
    $ raspivid -t 60000 -w 640 -h 480 -fps 5 -b 1200000 -p 0,0,640,480 -o video.h264
    ```
- <s>Convert the video to MP4</s>
    ```
    $ MP4Box -add video.h264 video.mp4
    ```
- <s>Provide a video stream with VLC</s>
    ```
    $ raspivid -o - -t 0 -hf -w 800 -h 400 -fps 5 | cvlc -vvv stream:///dev/stdin --sout '#standard{access=http,mux=ts,dst=:8160}' :demux=h264
    ```
- Provide a video stream with mjpeg_streamer
    ```
    $ ./mjpg_streamer -i "input_uvc.so" -o "output_http.so -p 8090"
    ```	
	
	
	
	
## Test streaming server via camera
On the Pi
- Testing mjpef stream server with PiCam
	```
	$ /home/pi/mjpg-streamer/mjpg-streamer-experimental/mjpg_streamer -i "/home/pi/mjpg-streamer/mjpg-streamer-experimental/input_raspicam.so -x 640 -y 480 -fps 10" -o "/home/pi/mjpg-streamer/mjpg-streamer-experimental/output_http.so -p 8080"	
	```

Setup env via systemd
- MJPEG Service
    ```
    $ sudo wget -O /etc/systemd/system/mjpg-streamer.service https://raw.githubusercontent.com/mitwelten/mitwelten-iot-hardware-poc/main/RaspberryPi/PoEPiCam/mjpg-streamer.service
	$ sudo wget -O /etc/systemd/system/.mjpgconf https://raw.githubusercontent.com/mitwelten/mitwelten-iot-hardware-poc/main/RaspberryPi/PoEPiCam/.mjpgconf	
	```	

- Start mjpg-streamer.service
    ```
    sudo systemctl daemon-reload
    sudo systemctl enable mjpg-streamer.service
    sudo systemctl start mjpg-streamer.service
    ```    
