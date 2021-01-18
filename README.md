# Raspberry-RTSP

## Compile and run a native C++ RSTP server
From https://kevinsaye.wordpress.com/2018/10/17/making-a-rtsp-server-out-of-a-raspberry-pi-in-15-minutes-or-less/ and https://github.com/mpromonet/v4l2rtspserver
1. `sudo apt install git cmake`
1. `git clone https://github.com/mpromonet/v4l2rtspserver.git`
1. `cd v4l2rtspserver && cmake . && make && sudo make install`
1. `v4l2rtspserver /dev/video0 -W 1280 -H 720`
1. Open on remote client: rtsp://{IPAddressOfYourPI}:8554/unicast

On Raspberry 3 A+
| Streams | Bandwidth | CPU |
|---------|-----------|-----|
|1        | 20 Mbps   |12%  |
|2        | 40 Mbps   |21%  |


## Use VLAN to run a RSTP server
From https://www.linux-projects.org/uv4l/tutorials/rtsp-server/ and https://medium.com/@petehouston/use-vlc-to-play-camera-with-different-formats-17cf839b72d0 and https://sites.google.com/view/how2raspberrypi/streaming-video-with-vlc.
The options are:
- `cvlc -vvv v4l2c:///dev/video0:width=640:height=480:chroma=MJPEG --sout '#rtp{sdp=rtsp://:8554/}'`
- `vlc v4l2:///dev/video0:chroma=mjpg`
- `cvlc --no-audio v4l2:///dev/video0 --v4l2-width 1280 --v4l2-height 720 --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/}' -I dummy`

| Streams | Bandwidth | CPU |
|---------|-----------|-----|
|1        | 20 Mbps   |12%  |
|2        | 40 Mbps   |21%  |


## Record audio
Easy test from https://learn.adafruit.com/usb-audio-cards-with-a-raspberry-pi/recording-audio
1. `arecord --device=hw:1,0 --format S16_LE --rate 44100 -c1 test.wav`

Record from python, from https://makersportal.com/blog/2018/8/23/recording-audio-on-the-raspberry-pi-with-python-and-a-usb-microphone
1. `sudo apt install libportaudio0 libportaudio2 libportaudiocpp0 portaudio19-dev`
1. `sudo apt install python3-pip`
1. `pip3 install pyaudio`
1. Use the scripts to list the audio devices and to record
