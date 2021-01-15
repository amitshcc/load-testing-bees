# rtspbee-publisher
RTSP Bee Publisher

# Download BBB

* 480p HD [http://bbb3d.renderfarming.net/download.html](http://bbb3d.renderfarming.net/download.html)

# Convert video files using ffmpeg:

## 240p 256kbit/s 15FPS

``` sh
ffmpeg -i "big_buck_bunny_480p_surround-fix.avi" \
-pix_fmt yuv420p -vsync 1 -threads 0 -vcodec libx264 -r 15 -g 30 -sc_threshold 0 -b:v 256k -bufsize 768k -maxrate 256k -vf scale=320:240 \
-preset veryfast -profile:v baseline -tune film \
-acodec copy \
./rtsp_240p_256kbps_15fps.mp4
```

## 480p 750kbit/s 24FPS

``` sh
ffmpeg -i "big_buck_bunny_480p_surround-fix.avi" \
-pix_fmt yuv420p -vsync 1 -threads 0 -vcodec libx264 -r 24 -g 24 -sc_threshold 0 -b:v 750k -bufsize 768k -maxrate 750k -vf scale=640:480 \
-preset veryfast -profile:v baseline -tune film \
-acodec copy \
./480p_750kbps_24fps.mp4
```

## 720p 1,5Mbit/s 30FPS

``` sh
ffmpeg -i "big_buck_bunny_480p_surround-fix.avi" \
-pix_fmt yuv420p -vsync 1 -threads 0 -vcodec libx264 -r 30 -g 30 -sc_threshold 0 -b:v 1500k -bufsize 768k -maxrate 1500k \
-preset veryfast -profile:v baseline -tune film \
-acodec copy \
./720p_1500kbps_30fps.mp4
```

## 1080 4,5Mbit/s 30FPS

``` sh
ffmpeg -i "big_buck_bunny_480p_surround-fix.avi" \
-pix_fmt yuv420p -vsync 1 -threads 0 -vcodec libx264 -r 30 -g 60 -sc_threshold 0 -b:v 4500k -bufsize 768k -maxrate 4500k -vf scale=1920:1080 \
-preset veryfast -profile:v baseline -tune film \
-acodec copy \
./1080p_4500kbps_30fps.mp4
```

# Streaming RTSP

## Using ffmpeg

``` sh
ffmpeg -re -stream_loop -1 -fflags +igndts -i "./720p_1500kbps_30fps.mp4" \
-pix_fmt yuv420p -vsync 1 -threads 0 \
-vcodec copy \
-acodec aac -muxdelay 0.0 \
-rtsp_transport tcp -f rtsp \
rtsp://red5pro.server.com:8554/live/stream1
```

## RTSP publish bees for single Red5 Pro server

**FILE**: rtspbee-publisher.sh   
**USAGE**: rtspbee-publisher.sh [endpoint] [app] [streamName] [amount of streams to start] [amount of time to playback] [Red5 Pro server API key] [path to mp4-file]   
**EXAMPLE**:   
``` sh
./rtspbee-publisher.sh "red5pro.server.com" live stream1 10 10 abc123 /path_to_video_file/bbb_480p.mp4
```

## RTSP publish bees for Stream Manager

**FILE**: rtspbee-publisher_sm.sh   
**USAGE**: rtspbee-publisher_sm.sh [sm_endpoint] [app] [streamName] [amount of streams to start] [amount of time to playback] [Origin API key] [path to mp4-file]   
**EXAMPLE**:   
``` sh
./rtspbee-publisher_sm.sh "https://stream-manager.url/streammanager/api/4.0/event/live/streamname?action=broadcast" live stream1 10 10 abc123 /path_to_video_file/bbb_480p.mp4
```

> [Red5 Pro server API key] or [Origin API key]   
> If you don't know or didn't set the API key on your server please follow the instruction:   
> https://www.red5pro.com/docs/server/api/overview/


# Attacking

The following will deploy 10 RTSP publishers ( `0.2` seconds apart) which will broadcast for 30 seconds each. Their stream names will be appended by `_N` , where `N` rerpesents the number in the sequence that they were deployed - e.g., `stream1_0` , `stream1_1` , etc.

The script will also create `N` -number of copies of the MP4 file so that each process is working with their own file for broadcast.
