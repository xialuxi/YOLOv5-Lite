### Update on 2021-09-16 [YOLOv5 add Repvgg Block]

![repyolov5](https://user-images.githubusercontent.com/82716366/133568390-918822c8-ff7c-43bd-bd54-c999d3e57f01.png)

# YOLOv5-Lite：lighter, faster and easier to deploy   ![](https://zenodo.org/badge/DOI/10.5281/zenodo.5241425.svg)

![0111](https://user-images.githubusercontent.com/82716366/129756605-a0cba66c-b296-43f1-b83e-39f5f10cd1c2.jpg)


Perform a series of ablation experiments on yolov5 to make it lighter (smaller Flops, lower memory, and fewer parameters) and faster (add shuffle channel, yolov5 head for channel reduce. It can infer at least 10+ FPS On the Raspberry Pi 4B when input the frame with 320×320) and is easier to deploy (removing the Focus layer and four slice operations, reducing the model quantization accuracy to an acceptable range).

## Comparison of ablation experiment results

  ID|Model | Input_size|Flops| Params | Size（M） |Map@0.5|Map@.5:0.95
 :-----:|:-----:|:-----:|:----------:|:----:|:----:|:----:|:----:|
001| yolo-fastest| 320×320|0.25G|0.35M|1.4| 24.4| -
002| nanodet-m| 320×320| 0.72G|0.95M|1.8|- |20.6
003| yolo-fastest-xl| 320×320|0.72G|0.92M|3.5| 34.3| -
004| yolov5-lite| 320×320|1.43G |1.62M|3.3| 36.2|20.8| 
005| yolov3-tiny| 416×416| 6.96G|6.06M|23.0| 33.1|16.6
006| yolov4-tiny| 416×416| 5.62G|8.86M| 33.7|40.2|21.7
007| nanodet-m| 416×416| 1.2G	|0.95M|1.8|- |23.5
008| yolov5-lite| 416×416|2.42G |1.62M|3.3| 41.3|24.4| 
009| yolov5-lite| 640×640|4.12G |1.62M|3.3| 45.7|27.1| 
010| yolov5s| 640×640| 17.0G|7.3M|14.2| 55.9|36.2

## Comparison on different platforms

Equipment|Computing backend|System|Framework|Input|Speed{our}|Speed{yolov5s}
:---:|:---:|:---:|:---:|:---:|:---:|:---:
Inter|@i5-10210U|window(x86)|640×640|torch-cpu|112ms|179ms
Nvidia|@RTX 2080Ti|Linux(x86)|640×640|torch-gpu|11ms|13ms
Redmi K30|@Snapdragon 730G|Android(arm64)|320×320|ncnn|36ms|-
Raspberrypi 4B|@ARM Cortex-A72|Linux(arm64)|320×320|ncnn|97ms|371ms

* The above is a 4-thread test benchmark
* Raspberrypi 4B enable bf16s optimization，[Raspberrypi 64 Bit OS](http://downloads.raspberrypi.org/raspios_arm64/images/raspios_arm64-2020-08-24/)

## <div>How to use</div>

<details open>
<summary>Install</summary>

[**Python>=3.6.0**](https://www.python.org/) is required with all
[requirements.txt](https://github.com/ppogg/YOLOv5-Lite/master/requirements.txt) installed including
[**PyTorch>=1.7**](https://pytorch.org/get-started/locally/):
<!-- $ sudo apt update && apt install -y libgl1-mesa-glx libsm6 libxext6 libxrender-dev -->

```bash
$ git clone https://github.com/ppogg/YOLOv5-Lite
$ cd YOLOv5-Lite
$ pip install -r requirements.txt
```

</details>

<details>
<summary>Inference with detect.py</summary>

`detect.py` runs inference on a variety of sources, downloading models automatically from
the [latest YOLOv5 release](https://github.com/ppogg/YOLOv5-Lite/releases) and saving results to `runs/detect`.

```bash
$ python detect.py --source 0  # webcam
                            file.jpg  # image 
                            file.mp4  # video
                            path/  # directory
                            path/*.jpg  # glob
                            'https://youtu.be/NUsoVlDFqZg'  # YouTube
                            'rtsp://example.com/media.mp4'  # RTSP, RTMP, HTTP stream
```

</details>

<details>
<summary>Training</summary>

```bash
$ python train.py --data coco.yaml --cfg yolov5s.yaml --weights '' --batch-size 64
                                         yolov5m                                40
                                         yolov5l                                24
                                         yolov5x                                16
```

<img width="800" src="https://user-images.githubusercontent.com/26833433/90222759-949d8800-ddc1-11ea-9fa1-1c97eed2b963.png">

 Maybe you should use multi-gpu. It's faster several times~
  
 ```bash
$ python -m torch.distributed.launch --nproc_per_node 4 train.py
```
  
</details>  

## Detection effect 

<img src="https://user-images.githubusercontent.com/82716366/133584299-32c19883-2eb2-48ef-a22c-34e244d0ffbe.jpg" width="1200" /><br/>

## Android_demo 

This is a Redmi phone, the processor is Snapdragon 730G, and yolov5-lite is used for detection. The performance is as follows:

<img src="https://user-images.githubusercontent.com/82716366/130357030-c4131b64-55e4-40c9-9f66-c17b42d2409b.jpg" width="300"/><br/>

### More detailed explanation ----------------------

Detailed model link: https://zhuanlan.zhihu.com/p/400545131

![image](https://user-images.githubusercontent.com/82716366/129891972-31f230e3-6e30-4392-820e-6aef08a51ab1.png)

## Reference

https://github.com/ultralytics/yolov5

https://github.com/megvii-model/ShuffleNet-Series

https://github.com/Tencent/ncnn
