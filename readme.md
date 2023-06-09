# Real Time Object Detection System with Multiple Cameras
This project aims to build a real-time object detection system that can capture video streams from multiple cameras using RTSP protocol, analyze the video frames to detect objects, and save detected object images in a local storage directory.

For object detection algorithm, I have used the pre-trained YOLOv8 model in this project. The model file was exported in ONNX format because we can then leverage the benefits of using ONNX runtime. ONNX runtime optimizes and accelerates machine learning inferencing. 
ONNX runtime optimizes and accelerates machine learning inferencing.
 
While running the system can handle if any camera goes offline unexpectedly. At that time the whole system doesn't crash, only streams the camera which is online. If there is no active camera available it terminates the program.

## Development Pipeline 
The development pipeline consists of the following tasks:

- Capturing video frames from multiple cameras using RTSP protocol in an efficient way with lower CPU usage and displaying the streaming feed.
Implementing an object detection algorithm using a pre-trained model such as YOLO.

- Integrating the object detection algorithm with the streaming script to detect objects in real-time from multiple camera sources and display the video stream with bounding boxes around the detected objects.

- Adding a feature of saving detected object images in a local storage directory. Filename should contain detected object name and current timestamp. This feature is implemented using python asynchronous programming to make sure program concurrency.


## Installation
To use this system you need to install some necessary libraries that are mentioned in requirements.txt.
```python

pip install -r requirements.txt

```
For ONNX runtime installation 

**If you have NVIDIA GPU:**
```python

pip install onnxruntime-gpu

```
**Otherwise:**
```python

pip install onnxruntime

```

**You will also need to provide rtsp links for the cameras.**
In the file `camera_urls.json` you need to paste the rtsp links in a list.
```powershell

akash@akash:~$ cat camera_urls.json
["rtsp://192.168.0.100:8080/h264_pcm.sdp","rtsp://192.168.0.103:8080/h264_pcm.sdp"]

```


## Usage 
First we need to capture frames from real-time cameras. 
To capture multiple streams with OpenCV, we can use threading which can improve performance by alleviating the heavy I/O operations to a separate thread. Since accessing the webcam/IP/RTSP stream using `cv2.VideoCapture().read()` is a blocking operation, our main program is stuck until the frame is read from the camera device. If you have multiple streams, this latency will definitely be visible. To remedy this problem, we can use threading to spawn another thread to handle retrieving the frames using a queue in parallel instead of relying on a single thread to obtain the frames in sequential order. Threading allows frames to be continuously read without impacting the performance of our main program. 

**To quit the the video streaming press 'q'**

To display the raw video stream from both cameras in real-time, run the following command:
```powershell

akash@akash:~$ python raw_video_stream.py

```

Next I have implemented the object detection model on those captured frames. While working on this task I followed this [repository](https://github.com/ibaiGorordo/ONNX-YOLOv8-Object-Detection). 

On this repository readme file you will see how to export pretrained yolov8 model in .ONNX format. While exporting the the model you also need to define the image(width,height). 

**Note: You should resize the frames before inferencing on model. It might affect the accuracy of the model if the input image has a different aspect ratio compared to the input size of the model.**

To display the video stream with bounding boxes around the detected objects, run the following command:
```powershell

akash@akash:~$ python detection_video_stream.py

```

Also added feature of saving detected object images from all camera streams in providing argument directory. By cropping the objects following their bounding box values. Filename contains detected object name and current timestamp. I have implemented this feature using python asynchronous programming to make sure program the cpu unit doesn't sit idle when saving images take time.

To use this feature also display the video detection you need to run the following command:
```powershell

akash@akash:~$ python detection_video_and_crop_objects.py --save_dir "path_to_save_images/"

```

