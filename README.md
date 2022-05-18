# FFMPEGCV is an alternative to OPENCV for video read and write.
The ffmpegcv provide Video Reader and Video Witer with ffmpeg backbone, which are faster and powerful than cv2.

- The ffmpegcv is api **compatible** to open-cv 
- The ffmpegcv can use **GPU accelerate** encoding and decoding. 
- The ffmpegcv support much more video **codecs** v.s. open-cv.
- The ffmpegcv support **RGB** & BGR format as you like.
- The ffmpegcv can **resize video** to specific size with/without **padding**.

In all, ffmpegcv is just similar to opencv api. But is faster and with more codecs.

## Install
> conda install ffmpeg
>
> pip install ffmpegcv

## GPU Accelate
- Support NVIDIA card only.
- The **Windows** ffmpeg supports NVIDIA accelerate.
- The **Linux** ffmpeg didn't orginally support NVIDIA accelerate.
Please re-compile the ffmpeg by yourself.
See the [link](https://docs.nvidia.com/video-technologies/video-codec-sdk/ffmpeg-with-nvidia-gpu/)
- The **MacOS** ffmpeg didn't supports NVIDIA at all.

## Video Reader
---
The ffmpegcv is just similar to opencv in api.
```python
# open cv
import cv2
cap = cv2.VideoCapture(file)
while True:
    ret, frame = cap.read()
    if not ret:
        break
    pass

# ffmpegcv
import ffmpegcv
cap = ffmpegcv.VideoCapture(file)
while True:
    ret, frame = cap.read()
    if not ret:
        break
    pass

# alternative, recommand
cap = ffmpegcv.VideoCapture(file)
nframe = len(cap)
for frame in cap:
    pass
```

Use GPU to accelerate decoding. It depends on the video codes.
h264_nvcuvid, hevc_nvcuvid ....
```python
cap_cpu = ffmpegcv.VideoCapture(file)
cap_gpu = ffmpegcv.VideoCapture(file, codec='h264_cuvid') #NVIDIA GPU0
cap_gpu0 = ffmpegcv.VideoCaptureNV(file)         #NVIDIA GPU0
cap_gpu1 = ffmpegcv.VideoCaptureNV(file, gpu=1)  #NVIDIA GPU1
```

Use rgb24 instead of bgr24
```python
cap = ffmpegcv.VideoCapture(file, pix_fmt='rgb24')
ret, frame = cap.read()
plt.imshow(frame)
```

Crop video.
```python
cap = ffmpegcv.VideoCapture(file, crop_xywh=(0, 0, 640, 480))
```

Resize the video to the given size.
```python
cap = ffmpegcv.VideoCapture(file, resize=(640, 480))
```

Resize and keep the aspect ratio with black border padding.
```python
cap = ffmpegcv.VideoCapture(file, resize=(640, 480), resize_keepratio=True)
```

Crop and then resize the video.
```python
cap = ffmpegcv.VideoCapture(file, crop_xywh=(0, 0, 640, 480), resize=(512, 512))
```

## Video Writer
---
```python
# cv2
out = cv2.VideoWriter('outpy.avi',
                       cv2.VideoWriter_fourcc('M','J','P','G'), 
                       10, 
                       (w, h))
out.write(frame1)
out.write(frame2)
out.release()

# ffmpegcv, default codec is 'h264' in cpu 'h265' in gpu.
out = ffmpegcv.VideoWriter('outpy.avi', None, 10, (w, h))
out.write(frame1)
out.write(frame2)
out.release()
```

frameSize is decided by the size of the first frame
```
out = ffmpegcv.VideoWriter('outpy.avi', None, 10)
```

Use GPU to accelerate encoding. Such as h264_nvenc, hevc_nvenc.
```python
out_cpu = ffmpegcv.VideoWriter('outpy.avi', None, 10)
out_gpu0 = ffmpegcv.VideoWriterNV('outpy.avi', 'h264', 10)        #NVIDIA GPU0
out_gpu1 = ffmpegcv.VideoWriterNV('outpy.avi', 'hevc', 10, gpu=1) #NVIDIA GPU1
```

Input image is rgb24 instead of bgr24
```python
out = ffmpegcv.VideoWriter('outpy.avi', None, 10, pix_fmt='rgb24')
out.write(cv2.cvtColor(frame, cv2.COLOR_BGR2RGB))
```

## Video Reader and Writer
---
```python
import ffmpegcv
vfile_in = 'A.mp4'
vfile_out = 'A_h264.mp4'
vidin = ffmpegcv.VideoCapture(vfile_in)
vidout = ffmpegcv.VideoWriter(vfile_out, vidin.fps)

for frame in vidin:
    vidout.write(frame)

vidin.release()
vidout.release()
```
