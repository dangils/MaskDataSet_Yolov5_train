# MaskDataSet_Yolov5_train

### Yolov5 커스텀 데이터셋 object detection train
- 데이터셋 : roboflow - Mask Wearing Dataset 활용
- train 환경 : Google Colab

------- 

> Yolov5 설치 및 데이터셋 설정

```
%rm -rf yolov5
!git clone https://github.com/ultralytics/yolov5  

%cd yolov5
!pip install -r requirements.txt 
```
- yolov5 클론 및 필수 라이브러리 설치

```
%mkdir /content/yolov5_mask
%cd /content/yolov5_mask/

!curl -L "https://public.roboflow.com/ds/eL4QUdkpSR?key=0ikL5WLM1w" > roboflow.zip; unzip roboflow.zip; rm roboflow.zip
```
- yolov5_mask 폴더 생성 및 DataSet 다운로드

<br>

```
import yaml

with open('/content/yolov5_mask/data.yaml', 'r') as f:
  data = yaml.full_load(f)

print(data)
{'train': '../train/images', 'val': '../valid/images', 'nc': 2, 'names': ['mask', 'no-mask']}
```
- yaml 파일 확인
- 각각 train, val 폴더 위치, 레이블 클래스 갯수, 클래스 명  

-----------

> Yolov5 train 수행

```
%cd /content/yolov5/
!python train.py --img 416 --batch 16 --epochs 100 --data /content/yolov5_mask/data.yaml --cfg ./models/yolov5s.yaml --weights yolov5s.pt --name mask_yolov5s_results 
```
- train 설정 값 : img 416, batch 16, epochs 100

-------

> 텐서보드 train 결과 검토

```
%load_ext tensorboard
%tensorboard --logdir /content/yolov5/runs/
```

![image](https://user-images.githubusercontent.com/74512114/198859073-a0aae8e6-8c33-4cf6-9cba-4ad35d5d5129.png)
- mAP 지표로 성능 확인
- precision 및 recall 확인

![image](https://user-images.githubusercontent.com/74512114/198859291-42d315d5-30f9-44ae-9ae4-433b7f03438e.png)
- loss의 지속적인 감소로 train이 적절하게 이루어 짐


-------

> Object detection 수행

```
from IPython.display import Image
import os

val_img_path = img_list[1]

weights_path = '/content/yolov5/runs/train/mask_yolov5s_results/weights/best.pt'

!python detect.py --weights "{weights_path}" --img 416 --conf 0.5 --source "{val_img_path}"

detect_img_path = '/content/yolov5/runs/detect/exp'

Image(os.path.join(detect_img_path, os.path.basename(val_img_path)))
```
- 학습된 best.pt 파일 적용
- object detection, 이미지 파일 확인

![image](https://user-images.githubusercontent.com/74512114/198859606-a945d641-d0e7-4156-8c92-937914486cbb.png)

