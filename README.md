# (:rocket:UPGRADE:rocket:) Pedestrain Assistance Model For the BLIND wth YOLO V5  시각장애인의 안전한 보행을 위한 보행 보조 모델

![깃허브용](https://user-images.githubusercontent.com/83996346/181015950-37cd8bae-6b35-4888-a42a-e90c03458fad.gif)
![깃허브용2](https://user-images.githubusercontent.com/83996346/181016906-d5be5fc5-5249-4ecf-a3e1-9c216caafd22.gif)
![깃허브용3](https://user-images.githubusercontent.com/83996346/181016587-2555f1cd-a426-4271-abfd-8fa2e86c0087.gif)
![깃허브용4](https://user-images.githubusercontent.com/83996346/181016851-e2afb538-1607-4729-9316-55aa65459757.gif)
![깃허브용5](https://user-images.githubusercontent.com/83996346/181017517-7f85be0c-ec6c-40ab-b130-e82b5212e43d.gif)

시각장애인의 안전한 보행을 위한 모델입니다.   
더불어 타야하는 버스가 왔음에도 불구하고 음성안내를 못 듣고 지나치는 경우가 있어 불편함을 겪는다는 내용을 기사를 통해 파악했습니다. 그런 상황을 방지하기 위해 버스와 버스의 번호까지 Detection으로 탐지하는 기능을 추가했습니다.   
This is a model for safe walking for the blind.  
In addition, the article found out that even though the bus that the blind has to take came, they sometimes pass by without hearing the voice guidance, so they suffer from inconvenience. To prevent such a situation, I have added the ability to detect bus and bus numbers with YOLO V5.

# 주요 파라미터 (Main Parameters)
- **Epoch : 20 Epoch**  
  Epoch 당 15분 소요되었습니다. 짧은 학습으로도 mAP 0.92라는 준수한 성능이 나와 학습을 중단했습니다.  
  It takes 15m per Epoch. I stopped learning because I could get a good performance of mAP 0.92 even with short learning!
    
- **Batch Size : 16**   :new:  
  YOLO V5 L 모델을 이용하여 학습을 진행했을 때, Colab Pro 환경에선 64 Batch로 올리면 램이 감당하지 못했기 때문에 16으로 진행했습니다.  
  When I trained YOLO V5 L, I trained model with 16 Batch size Because Colab Pro's RAM could not handle 64 Batch Size... 
  
- **Image Size : 640**
  YOLO V5 모델들에 권장되는 Image Size인 640을 사용했습니다.  
  I used recommended Image Size for YOLO V5 models.  
  
# 사용한 모델 (Model)
- Model : YOLO V5 **L**:new:  
  기존엔 s를 사용했으나 m과 L을 학습시켜본 결과 s에 비해 시간이 각각 10초, 20초 정도의 차이를 보였습니다. 이에 데이터셋을 더 늘렸을 때 충분히 감당할 수 있는 L 을 선택했습니다. 

# 학습 환경 (Training Environment)
- Colab Pro

# 데이터셋 (Dataset)
1. 셀렉트 스타 - :vertical_traffic_light:교차로 및 화폐 정보 데이터셋 from..  [Here](https://open.selectstar.ai/data-set/wesee)
2. 자체 제작 데이터셋 - :bus:버스 및 노선 번호 정보 데이터셋 with.. [Roboflow](https://roboflow.com/) :new:

# 데이터셋 구축 (Building Dataset) :new:
- 이미지 (Images) : 나무위키 서울시 버스 데이터 활용 (Namu Wiki Bus Images) [Here](https://namu.wiki/w/%EC%84%9C%EC%9A%B8%20%EB%B2%84%EC%8A%A4%20101)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 405 of Original Images  
- 활용 툴 (Labeling Tool) : Roboflow
- 인력 (# Labor) : 4 workers
- 클래스 (Class) : 11 classes (0 ~ 9 , BUS)
- 라벨링 예시 (Labeling Example) 
![image](https://user-images.githubusercontent.com/83996346/181172562-a3b17fc5-6683-4a81-a434-e1c175fb45aa.png)  
  
- 전처리 (Preprocessing) : Auto-Orient, Resize to 640
- Augmentation List     
**[First Augmentation]**  
  Crop : 0% ~ 20% Zoom   
  Hue : -29 ~ +29 ## To be robust to color of BUS  
  Brightness : -20% ~ +20% ## To train for inference at night  
  Noise : Up to 3% of Pixels   
  Cutout : 3 boxes with 20% size each ## To train for the case that something is in front of BUS  
    
  **[Second Augmentation]**  
  Crop : 0% ~ 30% Zoom  
  Rotation : -10 ~ +10  
  Hue : -15 ~ + 15  
  Exposure : -10% ~ +10%  
  Bounding Box Noise : Up to 2% of Pixels  
  
Roboflow에서 무료로 제공하는 이미지 증가 수를 최대 input의 3배만 지원하기 때문에 총 2번의 Augmentation을 통해 약 400장의 데이터셋을 약 8000장으로 증가시켰습니다.  
Roboflow only supports three times the maximum number of free image increases, so I have increased the total images from 405 to approximately 8000 datasets by **Two-Stage Augmentation**


