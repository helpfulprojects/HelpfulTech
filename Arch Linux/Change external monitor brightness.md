When I work on the computer for a whole day my eyes get tired. To try and solve this problem I decided to change the brighntess of my monitor and the blue light.

To control the brithness usually https://wiki.archlinux.org/title/Backlight#xbacklight is enough. But it's for intel cpu and I think it doesn't work on external monitor.
I used this guide instead https://wiki.archlinux.org/title/Backlight#External_monitors
Check if i2c-dev kernel module is loaded with `lsmod | grep dev`. If not, create a file `/etc/modules-load.d/i2c-dev.conf`
```
# Load i2c-dev at boot
i2c-dev

#end of file
```
I restarted and now i2c-dev is loaded and we should have access to the external monitor.
With `ddcutil setvcp 10 70` I set the currrent brightness to 70. But I do not do it manually, I have it automatically update based on the time of day with redshift that also operates the blue light. 
I did the usual install and set up of redshift https://wiki.archlinux.org/title/Redshift with sudo pacman -S and adding a config file with my location
I made it automatically start with systemclt --user enable redshift-gtk.service
There is also redshift.service, but the gtk one has a icon bellow where I can quickly enable/disable redshift.
This fixes the blue light issue, now for the brithness as well. I connected redshit with the ddcutil command with a hook script in redshift.
Create a file `.config/redshift/hooks/brightness.sh`
```
#!/bin/sh
# Set brightness via xbrightness when redshift status changes

# Set brightness values for each status.
# Range from 1 to 100 is valid
brightness_day=30
brightness_transition=20
brightness_night=10
# Set fps for smoooooth transition
fps=1000
# Adjust this grep to filter only the backlights you want to adjust
backlights=($(xbacklight -list | grep ddcci*))

set_brightness() {
	ddcutil setvcp 10 $1
}

if [ "$1" = period-changed ]; then
	case $3 in
		night)
			set_brightness $brightness_night 
			;;
		transition)
			set_brightness $brightness_transition
			;;
		daytime)
			set_brightness $brightness_day
			;;
	esac
fi
```

Change
```
brightness_day=30
brightness_transition=20
brightness_night=10
```
To whatever you want. Then restart the redshift service and you are done
