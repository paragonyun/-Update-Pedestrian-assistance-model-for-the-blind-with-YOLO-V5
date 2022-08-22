# (:rocket:UPGRADE:rocket:) Pedestrain Assistance Model For the BLIND wth YOLO V5  시각장애인의 안전한 보행을 위한 보행 보조 모델

![깃허브용](https://user-images.githubusercontent.com/83996346/181015950-37cd8bae-6b35-4888-a42a-e90c03458fad.gif)
![깃허브용2](https://user-images.githubusercontent.com/83996346/181016906-d5be5fc5-5249-4ecf-a3e1-9c216caafd22.gif)
![깃허브용3](https://user-images.githubusercontent.com/83996346/181016587-2555f1cd-a426-4271-abfd-8fa2e86c0087.gif)
![깃허브용4](https://user-images.githubusercontent.com/83996346/181016851-e2afb538-1607-4729-9316-55aa65459757.gif)
![깃허브용5](https://user-images.githubusercontent.com/83996346/181017517-7f85be0c-ec6c-40ab-b130-e82b5212e43d.gif)
  
[이전 버전(Previous Version)](https://github.com/paragonyun/Pedestrian-assistance-model-for-the-blind-with-YOLO-V5)  
  
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

# 불균형 조정 (Balancing Classes)
Bus dataset : 8,000 Images  
Cross dataset : 36,000 Images  
  
두 데이터를 단순하게 합치면 당연히 Cross 쪽의 데이터가 더 많으므로 버스를 상대적으로 인식하지 못했습니다. 때문에 Undersampling을 진행하여 8,000 : 8,000으로 데이터셋의 비율을 맞추는 것으로 데이터셋 간 불균형을 해소했습니다.   
When the two data are simply combined, of course, there is more data on the Cross side, so the bus was relatively unrecognized. Therefore, we solved the imbalance between datasets by matching the data set ratio to 8,000 : 8,000 by performing Undersampling.  
  
다만 각 데이터셋 '내부'의 클래스 불균형은 해소하지 않고 그대로 진행했습니다. 각 class는 BUS 혹은 Zebra-Cross와 항상 함께 나타나는 특징이 있었기 때문입니다.  
(Ex : 버스는 무조건 노선 번호가 있고 횡단보도는 '엔간하면' 보행자 신호등이 있음)   
However, the class imbalance of each dataset 'inside' was not resolved and proceeded as it was. This is because each class has a feature that always appears with BUS or Zebra-Cross.  
(Ex : Buses must have route numbers and crosswalks have pedestrian traffic lights when 'engaged')  
  
물론 이미지 합성을 통해 데이터셋 내부의 불균형을 해소하는 방안도 떠올랐지만 기술 부족으로 시현해내지 못했습니다.     
Of course, there was also a way to resolve the imbalance within the dataset through image photoshop, but it was not realized due to lack of technology.
  
# 학습 결과 (Result of Train)  
Wandb로 시각화한 결과입니다.  
Visualizations with Wandb!!   
![image](https://user-images.githubusercontent.com/83996346/181914732-6b9003b4-8ad4-4a45-afe2-aed11e0f09f4.png)
![image](https://user-images.githubusercontent.com/83996346/181914766-b0dd4f15-8f31-4f3a-a848-0a27c29e0b69.png)
![image](https://user-images.githubusercontent.com/83996346/181914925-c93d399b-d784-420c-8db6-2728654cd571.png)

### Train and Valid Batch
![image](https://user-images.githubusercontent.com/83996346/181914965-c8bbb98e-a3ac-4457-ad01-f6858d64a948.png)
![image](https://user-images.githubusercontent.com/83996346/181914983-009e863f-4b4d-4a10-9561-437bb75ee13b.png)
![image](https://user-images.githubusercontent.com/83996346/181914993-fdc2b6c0-f50a-4771-aa85-5f6c1c254b29.png)

# 발표 PPT 및 영상
[YouTube Link](https://youtu.be/m_1cNQ-Va90) or Click Image Below!
[![Click Image!!](https://user-images.githubusercontent.com/83996346/185837856-ac110b22-89c7-43ba-9e16-95d81535a6ce.png)](https://youtu.be/m_1cNQ-Va90)
![image](https://user-images.githubusercontent.com/83996346/185837869-e18eba2e-c607-4ee2-8cc0-915cf6280e51.png)
![image](https://user-images.githubusercontent.com/83996346/185837887-5e2f1a9f-7f94-41d8-bc55-8ab163b12397.png)
![image](https://user-images.githubusercontent.com/83996346/185837897-961b427e-f416-4e2c-80f5-57065cf0f429.png)
![image](https://user-images.githubusercontent.com/83996346/185837904-e4c4d313-8977-4832-907d-e1cb53e53f7d.png)
![image](https://user-images.githubusercontent.com/83996346/185837911-9244677e-8ead-4cca-98cc-eb8cb1043df1.png)
![image](https://user-images.githubusercontent.com/83996346/185837921-ddd8d5c3-74a3-4dae-b7c4-a6226125dd0b.png)
![image](https://user-images.githubusercontent.com/83996346/185837939-93c1c1ec-3dab-4f78-8d95-cfd07a682922.png)
![image](https://user-images.githubusercontent.com/83996346/185838042-a9f4ca3d-5b0d-4ac6-9aae-b45366b3289c.png)
![image](https://user-images.githubusercontent.com/83996346/185838050-f64aaaa8-ba36-4299-be4c-81515b8e7396.png)
![image](https://user-images.githubusercontent.com/83996346/185838059-8699d819-903d-4eee-96c1-b7bc4c322245.png)
![image](https://user-images.githubusercontent.com/83996346/185838068-c08e97ae-bc3c-4272-86e0-3d7226b0f665.png)
![image](https://user-images.githubusercontent.com/83996346/185838084-2e85ab5b-232a-49f9-ad74-e1da2936f298.png)
![image](https://user-images.githubusercontent.com/83996346/185838093-a32ac99c-86f6-4ecd-ad63-ab1d48622ff2.png)
![image](https://user-images.githubusercontent.com/83996346/185838101-85367037-4065-4503-bd25-fb9642350e2f.png)
![image](https://user-images.githubusercontent.com/83996346/185838114-1b87e03b-de36-47f8-a402-1afb3fb05521.png)
![image](https://user-images.githubusercontent.com/83996346/185838131-dc64c409-76c1-4018-8c2c-9ae6c298768d.png)
![image](https://user-images.githubusercontent.com/83996346/185838142-17abeda3-ea56-4dc6-8291-9e52460b6d6a.png)
![image](https://user-images.githubusercontent.com/83996346/185838152-2cacdf29-f42c-40a1-b675-2edab1fc6dad.png)
![image](https://user-images.githubusercontent.com/83996346/185838163-99da8e78-e1ce-4992-bd2a-e166bff38c98.png)
![image](https://user-images.githubusercontent.com/83996346/185838183-dac450b2-7047-4a3b-b09d-7f6307eecacb.png)
![image](https://user-images.githubusercontent.com/83996346/185838187-9a88600f-1356-4db8-acd4-4e6e545892ca.png)
![image](https://user-images.githubusercontent.com/83996346/185838199-4eee4ef9-a4d0-4d3f-a3c3-6c9a82339ed0.png)
![image](https://user-images.githubusercontent.com/83996346/185838207-0c2f8198-ee14-460c-a5e0-babb7777c8e2.png)
![image](https://user-images.githubusercontent.com/83996346/185838216-d73bc6bf-87d3-4035-a096-78a49eb23497.png)
![image](https://user-images.githubusercontent.com/83996346/185838226-275e2b24-8b29-43de-966b-89528b146c70.png)
![image](https://user-images.githubusercontent.com/83996346/185838241-c6d631c3-66e6-4b31-9de5-781d37b6a352.png)
![image](https://user-images.githubusercontent.com/83996346/185838247-ab72b274-5d92-47e9-8e18-8df5693adf68.png)
![image](https://user-images.githubusercontent.com/83996346/185838264-c3c6d6c9-02f0-4339-8966-7d317c453f21.png)
![image](https://user-images.githubusercontent.com/83996346/185838273-1b6826c0-ad64-4315-a121-96081148a9da.png)
![image](https://user-images.githubusercontent.com/83996346/185838280-419018da-a8f4-441f-bda2-8a9619641a3b.png)
![image](https://user-images.githubusercontent.com/83996346/185838301-b78e60cc-cbac-47be-95d4-eaed3e25a822.png)
![image](https://user-images.githubusercontent.com/83996346/185838314-ce22d8ff-8bed-4810-a374-972ec1f72fe5.png)
