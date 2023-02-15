### Download Traffic Sign Dataset
In this project, [German Traffic Sign Detection Benchmark Dataset](https://sid.erda.dk/public/archives/ff17dc924eba88d5d01a807357d6614c/published-archive.html) was used. Dataset includes the 900 training images (1360 x 800 pixels) in PPM format, the image sections containing only the traffic signs, a file in CSV format with the ground truth, and a ReadMe.txt with more details.

Note: Do not need to the download dataset, we will download it and execute all following process in the `traffic_sign_detection_YOLOv3_Darknet.ipynb` :partying_face:	 

### Installing Darknet
Darknet is an open-source neural network framework. We will use it to train the pre-trained weights.
```python
# Clone the darknet
!git clone https://github.com/AlexeyAB/darknet
%cd darknet
# Download weights darknet model 53
!wget https://pjreddie.com/media/files/darknet53.conv.74
```

### Converting Traffic Sign Dataset in YOLO Format
Open `prepare_dataset_for_YOLO_format.ipynb` in Colab and run the code.
After images of Traffic Signs were downloaded,

- Convert annotations
- Prepare the following files needed for training in Darknet framework
```python
  ├── train.txt
  ├── test.txt
  ├── ts_data.data
  ├── classes.names
```
These files' formats are as following:

`train.txt and test.txt`
```python
./FullIJCNN2013/00539.jpg
./FullIJCNN2013/00207.jpg
./FullIJCNN2013/00075.jpg
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
train = ./FullIJCNN2013/train.txt
valid = ./FullIJCNN2013/test.txt
names = ./FullIJCNN2013/classes.names
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
After preparation of all files we will the the custom dataset with following command:
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

- Put `traffic-sign-to-test.mp4` under the `darknet/data/`, and `yolov3_train_1000.weights` to `darknet/weights/` location.
```python
!./darknet detector demo cfg/ts_data.data cfg/yolov3_ts_test.cfg weights/yolov3_train_1000.weights data/traffic-sign-to-test.mp4 -out_filename traffic-sign-to-test.avi -dont_show
```
