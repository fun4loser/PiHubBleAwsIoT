# Installation instructions

### Version info
Tested with python 2.7 and bluez 5.41.
Note that I've read of problems with bluez versions >= 5.43 as hcitools and hciconfig were deprecated.

## Steps
From fresh Raspbian image do:
1. From GUI
    1. Change Pi’s hostname to something meaningful (sudo nano /etc/hostname)
    2. Change Pi’s password
    3. Configure WiFi
    4. Enable ssh
2. From console
    1. sudo su -
    2. apt-get update
    3. apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
    4. apt-get install -y git ansible
    5. git clone https://github.com/mkieboom/raspberrypi-bluez
    6. cd raspberrypi-bluez
    7. ansible-playbook -i myhosts/raspberrypi_localhost raspberrypi-deployment.yml --connection=local
        1. Note - Martijn hardcoded Bluez version 5.41. This is an older version but works
        2. Note that the last step to edit rc.local fails but it's ok. I include rc.local.example to take its place anyway
3. reboot
4. From console:
    1. sudo pip install bluepy
5. mkdir AwsIot

Prepare Pi as AWS IOT Thing:
1. Login to your AWS account
2. Services -> AWS IoT Core
3. Learn
4. Connect to AWS IoT
5. Configure a Device
6. Linux
7. Python
8. Give thing a name (e.g. PiHubKitchen)
9. Download connection kit
10. Copy to your Pi
    1. scp ~/Downloads/connect_device_package.zip pi@xxx.xxx.xxx.xxx:/home/pi/AwsIot
11. ssh to Pi and unzip connection kit
12. pip install AWSIoTPythonSDK
12. From AWS Console
    1. Manage -> Things -> Thing Name -> Security -> Select Certificate -> Policies -> Select Policy -> Edit policy document
13. Paste the following and click “Save as new version"
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iot:Publish",
        "iot:Subscribe",
        "iot:Connect",
        "iot:Receive"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```
Now install and configure PiHubBleAwsIoT
1. cd ..
2. git clone https://github.com/jbdamask/PiHubBleAwsIoT.git
3. cd PiHubBleAwsIoT
4. Edit configuration file to include the right certs for the Pi
5. sudo cp rc.local.example /etc/rc.local
5. sudo python multicast_ble.py
