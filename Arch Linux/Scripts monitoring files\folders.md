In Arch Linux I use Davinci Resolve to edit videos. But resolve doesn't allow some of the popular video and audio codecs and the free version doesn't allow mp4 export. There has to be a lot of conversions of files. To solve this I use incron.

I follow this guide https://wiki.archlinux.org/title/Incron for incron.

Incron allows me to set a folder or file for monitor and anytime for example a file is created in the folder a script I've made will run with that file and folder as input. So in my case after I've set up incron I type

```shell
incrontable -e
```
And type on a empty line the folder where OBS saves recordings, then after that the event when to trigger. I picked IN_CLOSE_WRITE. This fires up when I've stopped the recording. And after that I type the path of the script to run and after it as input `$@` `$#`.

I think `$#` is the file that started the event and `$@` the folder that it resides in. 

Now the script that I've made is 
```shell
if [[ "${2,,}" == *.mkv ]]; then
	ffmpeg -i "$1/$2" -c:a copy -c:v libsvtav1 -preset 8 -crf 35 -c:a pcm_s16le "$1/../mp4/$2.mp4" 
fi
```
This checks if the file name ends with .mkv. The using ffmpeg it will convert it into a format that resolve will accept and will save the result in a folder I've named pm4 with the same name but with .mp4. Resolve can accept .mp4 but the free version cannot export in mp4.

Incron is amazing.
