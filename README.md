# Raspberry-RTSP
Stream RSTP video from Raspberry 3 A+, with USB Microsoft Live Cinema webam (720p)


## Compile and run a C++ RSTP server
From  and https://github.com/mpromonet/v4l2rtspserver and https://kevinsaye.wordpress.com/2018/10/17/making-a-rtsp-server-out-of-a-raspberry-pi-in-15-minutes-or-less/
1. `sudo apt install git cmake libasound2-dev` optionally liblivemedia-dev, but it may break links
1. `git clone https://github.com/mpromonet/v4l2rtspserver.git`
1. `sudo su root`
1. `cd v4l2rtspserver && cmake . && make && make install`
1. `v4l2rtspserver /dev/video0 -W 1280 -H 720`
1. Open on remote client: `vlc rtsp://{IPAddressOfYourPI}:8554/unicast`

Performance:
| Streams | Bandwidth | CPU |
|---------|-----------|-----|
|1        | 18.8 Mbps | 5%  |
|2        | 37.3 Mbps | 7%  |



## Use VLAN to run a RSTP server
From https://www.linux-projects.org/uv4l/tutorials/rtsp-server/ and https://medium.com/@petehouston/use-vlc-to-play-camera-with-different-formats-17cf839b72d0 and https://sites.google.com/view/how2raspberrypi/streaming-video-with-vlc.
1. Install vlc on raspberry `sudo apt install vlc`
1. Start server:
    - Simple: `cvlc v4l2:///dev/video0 --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/}'`
    - Resized: `cvlc v4l2:///dev/video0 --v4l2-width 640 --v4l2-height 360 --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/}'`
    - Ready for audio: `cvlc v4l2:// :input-slave=alsa:// :v4l2-vdev="/dev/video0" :v4l2-width=480 :v4l2-height=360 :v4l2-chroma="MJPG" --sout '#rtp{sdp=rtsp://:8554/}'`
1. Connect from remote with remote vlc client: `vlc rtsp://{IPAddressOfYourPI}:8554/`

Performance:
| Streams | Bandwidth | CPU |
|---------|-----------|-----|
|1        | 20 Mbps   |12%  |
|2        | 40 Mbps   |21%  |

### Transmit also audio, does not work...
cvlc v4l2:// :input-slave=alsa://hw:1,0 :v4l2-vdev="/dev/video0" :v4l2-width=1280 :v4l2-height=720 :v4l2-chroma="mjpg" --sout '#transcode{acodec=mpga,ab=128,channels=2,samplerate=44100,threads=4,audio-sync=1}:rtp{mux=ts,sdp=rtsp://:8554/}'

Best result so far: audio track sent, but there is no sound: `cvlc v4l2:// :input-slave=alsa://hw:1,0 :v4l2-vdev="/dev/video0" :v4l2-width=640 :v4l2-height=360 :v4l2-chroma="MJPG" --sout '#rtp{sdp=rtsp://:8554/}'`


https://stackoverflow.com/questions/49846400/raspberry-pi-use-vlc-to-stream-webcam-logitech-c920-h264-video-without-tran
List all devices: `v4l2-ctl --all`


To get audio https://unix.stackexchange.com/questions/58526/trouble-getting-vlc-to-record-from-the-webcam-via-command-line
- `cvlc v4l2:///dev/video0 --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/unicast}'`
- `cvlc v4l2:// :input-slave=alsa:// :v4l-vdev="/dev/video0" --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/}'`
- `cvlc v4l2:// :v4l-vdev="/dev/video0" --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/}'`
- `cvlc v4l2:// :v4l-adev="/dev/pcm" :v4l-vdev="/dev/video0" --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/}'`
- `vlc v4l2:// :v4l-vdev="/dev/video0" :v4l-adev="/dev/pcm" :v4l-norm=3 :v4l-frequency=-1 :v4l-caching=300 :v4l-chroma="" :v4l-fps=-1.000000 :v4l-samplerate=44100 :v4l-channel=0 :v4l-tuner=-1 :v4l-audio=-1 :v4l-stereo :v4l-width=480 :v4l-height=360 :v4l-brightness=-1 :v4l-colour=-1 :v4l-hue=-1 :v4l-contrast=-1 :no-v4l-mjpeg :v4l-decimation=1 :v4l-quality=100 --sout="#transcode{vcodec=theo,vb=2000,fps=12,scale=0.67,acodec=vorb,ab=90,channels=1,samplerate=44100}:standard{access=file,mux=ogg,dst=output.ogg}"`
- `vlc v4l2:// :v4l-vdev="/dev/video0" :v4l-adev="/dev/pcm" :v4l-norm=3 :v4l-frequency=-1 :v4l-caching=300 :v4l-samplerate=44100 :v4l-channel=0 :v4l-tuner=-1 :v4l-audio=-1 :v4l-stereo"`

- No audio, -I dummy = cvlc: `vlc --no-audio v4l2:///dev/video0 --v4l2-chroma MJPG --sout '#rtp{sdp=rtsp://:8554/unicast}' -I dummy`
- `cvlc -vvv v4l2c:///dev/video0:width=640:height=480:chroma=MJPEG --sout '#rtp{sdp=rtsp://:8554/unicast}'`
- `cvlc v4l2c:///dev/video0:width=640:height=480:chroma=mjpeg --sout '#rtp{sdp=rtsp://:8554/}'`
- `cvlc v4l2:///dev/video0:chroma=mjpg`




## Record audio

Easy test from https://learn.adafruit.com/usb-audio-cards-with-a-raspberry-pi/recording-audio
1. List USB recording devices: `lsusb -t`, `arecord -l` 
1. Record from card 1, device 0: `arecord --device=hw:1,0 --format S16_LE --rate 44100 -c1 test.wav`

Record from python, from https://makersportal.com/blog/2018/8/23/recording-audio-on-the-raspberry-pi-with-python-and-a-usb-microphone
1. `sudo apt install libportaudio0 libportaudio2 libportaudiocpp0 portaudio19-dev`
1. `sudo apt install python3-pip`
1. `pip3 install pyaudio`
1. Use the scripts to list the audio devices and to record


## 16:9 Resolutions
Good resolutions for resize should be multiple of 8: https://pacoup.com/2011/06/12/list-of-true-169-resolutions/
- 128 x 72
- 256 x 144
- 384 x 216
- 512 x 288
- **640 x 360**
- 768 x 432
- 1024 x 576
- **1280 x 720**
- **1920 x 1080**


DVD is 720 x 480


