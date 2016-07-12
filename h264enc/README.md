Proof of Concept hardware accelerated H264 encoder for sunxi
============================================================

This is a basic example for using the H264 hardware encoder for sunxi SoCs.

*It is just a proof of concept and not recommended for production use!*

U P D:
-------
Modified for devices which are using ion memory allocator and VE hardware 
older than or equal to VE1633 i.e. A31s, A80, A33, H3, H8.
Sources are stored in common_ion folder. 
Makefile has a hardcoded parameter ION = _ion to build from folder common_ion
Modified version of the software has been tested on OPI PC board based on H3 CPU
with cmd line as shown below

    encoding from usb camera:
    ffmpeg -f v4l2 -video_size 640x480 -i /dev/video0 -pix_fmt nv12 -r 25 -f rawvideo pipe: | ./h264enc - 640 480 test.h264
  
    decoding:
    mpv --vo=vdpau --hwdec=vdpau --hwdec-codecs=all test.h264 


Limitations:
------------

- no B frames
- constant QP
- only raw nv12 or nv16 input and bytestream output
- ... many more

The old mplayer doesn't seem to like the raw bytestream output, maybe our
bytestream is still a little bit erroneous.


Usage:
------

    make

    ffmpeg -i <inputfile> -pix_fmt nv12 \
       -vf pad="width=trunc((iw+15)/16)*16:height=trunc((ih+15)/16)*16" \
       -f rawvideo pipe: | ./h264enc - <width> <height> <outputfile>

It is *important* that the input data is in nv12 format and has a width and
height multiple of 16, this is ensured by the "-vf pad" above.

For example:

    ffmpeg -i bigbuckbunny.mpg -pix_fmt nv12 \
       -vf pad="width=trunc((iw+15)/16)*16:height=trunc((ih+15)/16)*16" \
       -f rawvideo pipe: | ./h264enc - 854 480 bigbuckbunny.264

    ffplay bigbuckbunny.264
