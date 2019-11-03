# Picframe
WiFi enhanced Pictureframe with cloud sync using a RaspberryPi nano.

I've started this project to share pictures with my grandmas. I wanted to share actual pictures of their grand and great-grandchildren.

Please feel free to build your own.
Contact me for frames.

## Used Parts

(amazon affiliate links)

* https://amzn.to/33aSveS - 1x FullHD display 7"
* https://amzn.to/2oDQby6 - 1x microUSB splitter cable 
* https://amzn.to/2N9K4Lq - 1x microHDMI flat cable 10 cm
* https://amzn.to/36uITOb - 1x microHDMI plug 90 °
*https://de.aliexpress.com/item/33058579088.html?spm=a2g0o.productlist.0.0.6cf82c86uWIagB&algo_pvid=56df71e7-d072-43b8-aa82-204e2c4a2e07&algo_expid=56df71e7-d072-43b8-aa82-204e2c4a2e07-1&btsid=ba4caa57-4bc3-47a0-b3c5-d40c616bcd54&ws_ab_test=searchweb0_0,searchweb201602_1,searchweb201603_52 - 1x microHDMI Plug 
* https://amzn.to/36BeGNI - 1x RaspberryPi Zero without header
* small wood screws about 7 mm - 2x

You can also order everything except the monitor on Aliexpress.


## Install

1. use [Etcher](https://www.balena.io/etcher/) to burn Raspbian Buster with desktop on your SD card.
2. upload ssh file to /boot partition of your SD card
3. upload wpa file to /boot partition. Don't forget to enter your WiFi credentials first.
4. ssh to your RaspberryPi and run `sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade`
to install latest updates.
5. Install slideshow by [NautiluX](https://github.com/NautiluX)

  ```
  wget https://github.com/NautiluX/slide/releases/download/v0.9.0/slide_pi_stretch_0.9.0.tar.gz
  tar xf slide_pi_stretch_0.9.0.tar.gz
  sudo mv slide_0.9.0/slide /usr/local/bin/
  ```

6. Install additional librarys used by slideshow

  `sudo apt install libexif12 qt5-default lightdm`

7. Install Rclone to sync your pictures.

  `curl https://rclone.org/install.sh | sudo bash`

8. Please follow the Rclone [website](https://rclone.org) for your preferred cloud space and run:

  `rclone config`

9. Autorun slideshow and prevent display from standby

  ```
  mkdir -p /home/pi/.config/lxsession/LXDE/
  nano /home/pi/.config/lxsession/LXDE/
  ```

  insert:

  ```
  @xset s noblank
  @xset s off
  @xset -dpms
  @slide -p -t 60 -o 0 -p /home/pi/picframe
  ```

  ```
  slide [-t rotation_seconds] [-o background_opacity(0..255)] [-b blur_radius] -p image_folder
		• image_folder: where to search for images (.jpg files), currently non-recursively
		• rotation_seconds(default=30): time until next random image is chosen from the given folder
		• background_opacity(default=150): opacity of the background filling image between 0 (black background) and 255
		• blur_radius(default=20): blur radius of the background filling image
  ```
10. Disable screen blanking, which the Raspberry Pi normally does after 10 minutes, by editing the following file:

  `nano /etc/lightdm/lightdm.conf`

  ```
	[SeatDefaults]
	xserver-command=X -s 0 -dpms
  ```

11. Cronjob to
  * switch on and off the frame
  * sync cloud folder to download new pictures

 `cd /etc/crontab -e`

 ```
 0 22 * * * /opt/vc/bin/tvservice -o
 0 7 * * * /opt/vc/bin/tvservice -p && sudo systemctl restart display-manager
 */60 * * * rclone sync remote:erika picframe
 ```

 Switch on at 7 am and switch off at 22 pm. Sync every 60 minutes.

12. To rotate the screen, depending on the build in direction of the screen, add value

 `display_rotate=2`

 in

 `/boot/config.txt`

 ```
 display_rotate = 0   Normal
 display_rotate = 1   90 degrees
 display_rotate = 2   180 degrees
 NOTE: You can rotate both the image and touch interface 180º by entering lcd_rotate=2 instead
 display_rotate = 3   270 degrees
 display_rotate = 0x10000   horizontal flip
 display_rotate = 0x20000   vertical flip
 ```

## Pictures

See folder pictures for pictures.
