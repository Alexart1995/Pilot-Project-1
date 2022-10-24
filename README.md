# Pilot Project 1 - PiRacer
Please find the subject file in subject folder.
# Table of contents
- Assemble PiRacer
- RasberryPi Setup
- PiRacer Setup
# Assemble PiRacer
Use Racer AI Kit of WAVESHARE to assemble your PiRacer. </br>
Carefully go through the instruction using PDF. </br>
On Step 5 go to Rasberry Setup topic and PiRacer Setup. </br>
After these steps done finish an assemble </br>
  - [Manual Assembly PDF](https://www.waveshare.com/w/upload/a/a2/Piracer_pro_ai_kit-en2.pdf)

# RasberryPi Setup
### Step 1. Install operating Systems
img 1 img 2 img 3
1. Install Putty (link)
2. Download Raspian Legacy (link)
3. Install on your computer. As a result while launching you should see img1
4. Download RasberryPi Imager (link)
5. Connect you SD card in your computer ( labtop )
6. Change Operating System from step 4 (img 2)
7. Change advanced options to connect WIFI
8. Click WRITE to Download Rasberri Pi on SD card
9. Move to ```/boot``` directory
10. Enter command ```touch ssh```
11. Insert your SD card to PiRacer
12. Connect to monitor 
13. Find IP of your Car (img)
### Step 2. Connecting to the Pi
1. Connect IP of PiRacer to computer(laptop) with Putty (img)
2. Connect to Pi (img)
    - login: ```audi```
    - password: ```seame2022```
### Step 3. Update and Upgrade 
```
sudo apt-get update --allow-releaseinfo-change
sudo apt-get upgrade
```
### Step 4. Raspi-config
```
sudo raspi-config
```
- change default passward and login for Pi (as you wish)
- change hostname (as you wish)
- enable ```Interfacing Options``` - ```I2C```
- enable ```Interfacing Options``` - ```Camera```
- enable ```Interfacing Options``` - ```SSH```
- enable ```Advanced Options``` - ```Expand Filesystem``` so you can use your whole SD card storage
- ```Advanced options```-```GL Driver```-```G2 GL (Fake KMS)```
- Choose ```<Finish>``` and Reboot to complete Raspi-config
### Step 5. Install Dependencies
``` sudo apt-get install build-essential python3 python3-dev python3-pip python3-virtualenv python3-numpy python3-picamera python3-pandas python3-rpi.gpio i2c-tools avahi-utils joystick libopenjp2-7-dev libtiff5-dev gfortran libatlas-base-dev libopenblas-dev libhdf5-serial-dev libgeos-dev git ntp ```
### Step 6. Install OpenCV Dependencies   
``` sudo apt-get install libilmbase-dev libopenexr-dev libgstreamer1.0-dev libjasper-dev libwebp-dev libatlas-base-dev libavcodec-dev libavformat-dev libswscale-dev libqtgui4 libqt4-test ``` 
### Step 7. Setup Virtual Env 
``` 
python3 -m virtualenv -p python3 env --system-site-packages
echo "source ~/env/bin/activate" >> ~/.bashrc
source ~/.bashrc
```
### Step 8. Install Donkeycar Python Code
1. Create Directory 
```
cd ~
mkdir projects
cd projects
```
2. Get the latest donkeycar from Github
```
git clone https://github.com/autorope/donkeycar
cd donkeycar
git checkout main
pip install -e .[pi]
pip install https://github.com/lhelontra/tensorflow-on-arm/releases/download/v2.2.0/tensorflow-2.2.0-cp37-none-linux_armv7l.whl
```
3. Validate tensorflow install
```
python -c "import tensorflow; print(tensorflow.__version__)"
```
### Step 9. Install Open CV 
1. Install OpenCV 
```
sudo apt install python3-opencv
# if failed
# pip install opencv-python
```
2. Test to see if import succeeds
```
python -c "import cv2"
# if no errors, you have OpenCV installed
```
### Step 10. Install Service of OLED Display
```
cd ~
git clone https://github.com/waveshare/pi-display
cd pi-display
sudo ./install.sh
```
```
sudo pip3 install flask
```
```
cd pidisplay
python display_server.py
```
if you have problems 
```
# Change from .utils -> from utils
sudo nano display_server.py
sudo reboot
```
# PiRacer Setup
### Create Donkeycar from Template
1. Connect PiRacer by SSH
```
donkey createcar --path ~/mycar
```
2. Configure I2C PCA9685 ( servo driver board )
```
sudo apt-get install -y i2c-tools
sudo i2cdetect -y 1
```
- If Pi doesn’t display like below, exchange your PiRacer expansion board
```
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- 3c -- -- -- 
40: 40 -- 42 -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: 70 -- -- -- -- -- -- --
```
### Web Control (img)
1. Open Raspberry Pi terminal and run the follow commands
```
source ~/env/bin/activate
cd ~/mycar
python manage.py drive
```
2. Go to ```http://<raspberrypi_ip_address>:8887```
- You can use your mouse and Joystick to control PiRacer
### Calibrate DonkeyCar
1. Adjust the value to make the servo turns to the center place and remember the value ( In our case, 380 was center )
```
cd ~/mycar
donkey calibrate --channel 0 --bus=1
```
2. Test if 280, and 480 can let the servo turns to the far left and the far right
3. Modify the config.py file and change variables
```
sudo nano config.py
# Change values
# STEERING_LEFT_PWM = 480
# STEERING_RIGHT_PWM = 280
# THROTTLE_FORWARD_PWM = 500
# THROTTLE_STOPPED_PWM = 370
# THROTTLE_REVERSE_PWM = 220
```
### Gamepad Input mapping
1. Connect GamePad with PiRacer
2. Create joystick creator wizard
```
cd ~/mycar
donkey createjs
```
3. To map button, axis, control follow instructions on the screeb (img)
In mapping steps of steering and throttle, you should set in opposite direction 
( For example, If you want to throttle Left Stick: Vertical, you need to register Right Stick: Horizontal in the throttle step )
4. Change configuration
```
cd ~/mycar
sudo nano config.py
# USE_JOYSTICK_AS_DEFAULT = True
# JOYSTICK_DEVICE_FIL = "/dev/input/js0"
# CONTROLLER_TYPE = 'custom'
```
```
# test with gamepad
python manage.py drive --js
```
