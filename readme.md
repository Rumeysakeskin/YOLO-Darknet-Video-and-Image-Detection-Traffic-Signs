## YOLO Darknet: Traffic sign detection on image and video

![result_traffic_sign](https://user-images.githubusercontent.com/61134521/219385677-b7214329-4aea-46c0-864a-3cee3847d100.gif)

### Download Traffic Sign Dataset
In this project, [German Traffic Sign Detection Benchmark Dataset](https://sid.erda.dk/public/archives/ff17dc924eba88d5d01a807357d6614c/published-archive.html) was used. Dataset includes the 900 training images (1360 x 800 pixels) in PPM format, the image sections containing only the traffic signs, a file in CSV format with the ground truth, and a ReadMe.txt with more details.

Note: Do not need to the download dataset, we will download it in `training_YOLOv3_Darknet.ipynbb` :partying_face:	 

### Converting Traffic Sign Dataset in YOLO Format
Open `training_YOLOv3_Darknet.ipynb` in Colab and run the code.

- In the YOLO format, every image in the dataset has a single text file. If an image has no objects there is no text file for that image. 
- Inside the text file, each row contains the following information: The first element of each row is a class id, then bounding box properties (x, y, width, height). Bounding box properties must be normalized (0–1). (x, y) should be the mid-points of a box.
```python
images
→ 0001.jpg
labels
→ 0001.txt
 ├── class_id, x_centre, y_centre, width, height
```


- Also we need to create the following files before training:
```python
  ├── train.txt
  ├── test.txt
  ├── ts_data.data
  ├── classes.names
```
These files' formats are as following:

`train.txt and test.txt`
```python
/content/FullIJCNN2013/00539.jpg
/content/FullIJCNN2013/00207.jpg
/content/FullIJCNN2013/00075.jpg
.
.
```

`classes.names`
```python
prohibitory
danger
mandatory
other
```

`ts_data.data`
```python
classes = 4
train = /content/FullIJCNN2013/train.txt
valid = /content/FullIJCNN2013/test.txt
names = /content/FullIJCNN2013/classes.names
backup = backup
```

### Setting up Configuration Files

[yolov3.cfg](https://github.com/pjreddie/darknet/blob/master/cfg/yolov3.cfg) includes parameters for training, structure of CNN layers, last three YOLO layers.

`filters = (classes + coordinates + 1) * masks`

`max_batches = classes * 2000 (not less than 4000)`
         
`steps = 80% and 90% of max batches (max batches represent total number of iterations for training)`

For traffic sign dataset:
```python
filters = (4 + 5) * 3 = 27
max_batches = 4 * 2000 = 8000
steps = 6400, 7200
classes = 4
batch = 32 (training), 1 (testing)
subdivisions = 16 (training), 1 (testing) - (represent number of minibatches in one batch)
```
- Prepare `yolov3_train.cfg` and `yolov3_test.cfg` files according to your custom dataset following above instructions and put under the `darknet/cfg/` location.

### Training YOLOv3 in Darknet Framework
After preparation of all files we will train the the custom dataset with following command:
```python
!./darknet detector train cfg/ts_data.data cfg/yolov3_train.cfg darknet53.conv.74 -dont_show
```


### How to define the best weights after training?

Weights will be saved every 100 iterations in the following path. Now, we will define the best one to use for detection making sure that there is no overfitting.
```python
├── darknet/
    ├── backup/
        ├── yolov3_train_1000.weights
        ├──   ...
        ├── yolov3_train_last.weights
        ├── yolov3_train_final.weights
```

There is special command in Darknet framework that calculates mAP.
```python
!./darknet detector map cfg/ts_data.data cfg/yolov3_ts_train.cfg backup/yolov3_train_1000.weights
```
Continue checking for all weights in order to define the weights with biggest mAP.

### Testing

- Put `traffic-sign-test.mp4` under the `darknet/data/`, and `yolov3_train_1000.weights` to `darknet/weights/` location.
```python
!./darknet detector demo cfg/ts_data.data cfg/yolov3_ts_test.cfg weights/yolov3_train_1000.weights data/traffic-sign-test.mp4 -out_filename traffic-sign-to-test.avi -dont_show
```

### Inference on Image
Open the `traffic_sign_image_detection.ipynb` notebook and run the script for your test image. 

<img src="result.jpg" width="500" height="270">

### Inference on Video
Open the `traffic_sign_detection_on_video.ipynb` notebook and run the script for your test video.

![result_traffic_sign](https://user-images.githubusercontent.com/61134521/219385677-b7214329-4aea-46c0-864a-3cee3847d100.gif)






