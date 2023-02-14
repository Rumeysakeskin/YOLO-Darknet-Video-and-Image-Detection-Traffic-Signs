### Download Traffic Sign Dataset
In this project, [German Traffic Sign Detection Benchmark Dataset](https://sid.erda.dk/public/archives/ff17dc924eba88d5d01a807357d6614c/published-archive.html) was used. Dataset includes the 900 training images (1360 x 800 pixels) in PPM format, the image sections containing only the traffic signs, a file in CSV format with the ground truth, and a ReadMe.txt with more details.

Note: Do not need to the download dataset, we will download it in `prepare_dataset_for_YOLO_format.ipynb` :partying_face:	 

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
- Prepare `yolov3_train.cfg` and `yolov3_test.cfg` files according to your custom dataset following above instructions.


