Create timelapse
================

If you want to add timestamp to your video use: https://github.com/kimmobrunfeldt/pieces/blob/master/burn-timestamp.py

After you have the set of new timestamped images, create timelapse.

ffmpeg
------

Install https://gist.github.com/clayton/6196167

Assuming we'd have files 0.jpg, 1.jpg, 2.jpg, ... and so on.

```
fmpeg -framerate 25 -i %00d.jpg -c:v libx264 -profile:v high -crf 20 -pix_fmt yuv420p output.mp4
```

From: https://askubuntu.com/questions/610903/how-can-i-create-a-video-file-from-a-set-of-jpg-images

Mencoder(MPlayer)
-----------------

Install

    brew install mplayer

Create Motion picture video(MPNG or MJPEG)

    mencoder "mf://*.png" -mf fps=8:type=png -ovc lavc -lavcopts vcodec=mpeg4:vbitrate=21600000 -vf scale=1920:1080 -o outputfile.avi

Encode a HD time-lapse video with H.264 codec and MP4 format
http://ubuntuforums.org/showthread.php?t=1850196

*Warning: This method caused some jammed frames!*

    FPS=60
    # Create raw video from images with H.264 codec
    mencoder "mf://*.png" -mf fps=$FPS -vf scale=1440:-1 -ovc x264 -x264encopts pass=1:bitrate=6000:crf=20:preset=veryslow -nosound -ofps $FPS -of rawvideo -o rawoutput.264
    # Convert raw video to mp4 format
    ffmpeg -f h264 -i rawoutput.264 -r $FPS -vcodec copy muxed.mp4
    rm rawoutput.264


To MPEG-4

    mencoder "mf://*.png" -mf w=1440:h=1080:fps=20:type=png -ovc lavc -lavcopts vcodec=mpeg4:mbd=2:trell -oac copy -o output.avi

ImageMagick
-----------

Install

    brew install imagemagick

Run

    convert -limit thread 10 -delay 8 -quality 100 *.png outvideo.avi

convert was built without OpenMP feature. I built ImageMagick with OpenMP by installing newer version of gcc with brew.

