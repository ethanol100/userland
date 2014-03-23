RaspiMJPEG is an OpenMAX-Application based on the mmal-library, which is comparable to and inspired by RaspiVid and RaspiStill. RaspiMJPEG can record 1080p 30fps videos and 5 Mpx images into a file. But instead of showing the preview on a screen, RaspiMJPEG streams the preview as MJPEG into a file. The update-rate and the size of the preview are customizable with parameters and independent of the captured image/video. Once started, the application receives commands with a unix-pipe and showes its status on stdout and writes it into a status-file. The program terminates itself after receiving a SIGINT or SIGTERM.


Example usage as MJPEG-Streamer:
raspimjpeg -w 640 -h 360 -d 5 -q 70 -of /path/to/image.jpg

This command creates the image "path/to/image.jpg" with the size 640x360 px and updates it every time, 5 frames are captured, which gives (30/5=) 6fps. The image proportions are recommended to be 16:9, the jpg-quality is set with -q. This way used, RaspiMJPEG serves as MJPEG-streamer and the preview can be shown on a website for example. To achieve a high update-rate (e.g. "-d 1"), it is recommended to save the file into the ram and not on the sd-card (e.g. "-of /dev/shm/image.jpg").
If -w, -h and -d are not set, the default setting is used (320x180, 6fps, quality 85).
This way used, RaspiMJPEG can also record a timelapse with many frames per second, if the -of parameter is set as /pat/to/image%04d.jpg.


Example usages as MJPEG-Streamer with video and image capture:
raspimjpeg -w 320 -h 180 -d 5 -of /path/to/image.jpg -cf /path/to/pipe -vf /path/to/video.h264 -if /path/to/image_%04d.jpg

This command does the same as the one above, but it is also listening on the pipe, defined with the parametet -c. If another program writes "ca 1" into the pipe (shell: echo "ca 1" > /path/to/pipe), the application continues with the MJPEG-stream, but starts also a H264-capture 1080p 30fps into the defined file. The capture is stopped with the command "ca 0" via pipe. A new capture overwrites the defined file, except the filename includes a %d for the video-number. To make MJPEG and H264 possible, it is recommended to write the image file only into the ram, as described above.
If you write "im" into the pipe, it captures an image and saves it with the path given after -if. Other pipe-options are described below.


Example usage as MJPEG-streamer with video capture and status-output:
raspimjpeg -w 320 -h 180 -d 5 -of /path/to/image.jpg -cf /path/to/pipe -vf /path/to/video.h264 -sf /path/to/textfile.txt

Until now, RaspiMJPEG wrote its status into stdout/stderr. With this new command, the status is also written into a textfile (no logging, just the newest status). Possible messages and their meanings are:
ready     --> MJPEG is streaming, not capturing
video     --> MJPEG is streaming and video is capturing
boxing    --> MJPEG is streaming and video is packed into mp4
image     --> MJPEG is streaming and image is capturing
timelapse --> MJPEG is streaming and timelapse is active
halted    --> RaspiMJPEG is idle
md_ready  --> See 'ready', motion is detection active
md_video  --> See 'video', motion is detection active
md_boxing --> See 'boxing', motion is detection active
errror    --> An error occured and the application terminated


Possible parameters:
-ic   set the offset for image output numbering
-vc   set the offset for video output numbering


Possible Pipe-Commands:
ca 1  start video capture
ca 0  stop video capture
im    capture image
tl    start timelapse, parameter is time between images in 1/10 seconds.
tl 0  stop timelapse
px    set video+img resolution/framerate (AAAA BBBB CC DD EEEE FFFF; video = AxB px, C fps, boxed with D fps, image = ExF px)
sh    set sharpness (range: [-100;100]; default: 0)
co    set contrast (range: [-100;100]; default: 0)
br    set brightness (range: [0;100]; default: 50)
sa    set saturation (range: [-100;100]; default: 0)
is    set ISO (range: [100;800]; default: 0=auto)
vs 1  turn on video stabilisation
vs 0  turn off video stabilisation
ec    set exposure compensation (range: [-10;10]; default: 0)
em    set exposure mode (range: [off/auto/night/nightpreview/backlight/spotlight/sports/snow/beach/verylong/fixedfps/antishake/fireworks]; default: auto)
wb    set white balance (range: [off/auto/sun/cloudy/shade/tungsten/fluorescent/incandescent/flash/horizon]; default: auto)
mm    set metering mode (range: [average/spot/backlit/matrix]; default: average)
ie    set image effect (range: [none/negative/solarise/posterize/whiteboard/blackboard/sketch/denoise/emboss/oilpaint/hatch/gpen/pastel/watercolour/film/blur/saturation/colourswap/washedout/posterise/colourpoint/colourbalance/cartoon]; default: none)
ce    set colour effect (A BB CC; A=enable/disable, effect = B:C)
ro    set rotation (range: [0/90/180/270]; default: 0)
fl    set flip (range: [0;3]; default: 0)
ri    set sensor region (AAAAA BBBBB CCCCC DDDDD, x=A, y=B, w=C, h=D)
qu    set output image quality (range: [0;100]; default: 85)
bi    set output video bitrate (range: [0;25000000]; default: 17000000)
rl 0  disables raw layer
rl 1  enables raw layer
ru 0  halt RaspiMJPEG and release camera
ru 1  restart mjpeg-stream
md 1  start motion detection
md 0  stop motion detection
