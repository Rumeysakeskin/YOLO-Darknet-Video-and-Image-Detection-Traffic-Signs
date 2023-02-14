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
