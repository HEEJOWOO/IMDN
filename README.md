# IMDN : https://arxiv.org/abs/1909.11856

#### <I studied while referring to various sites, but it is not enough. Thanks anytime for feedback.>
### <heejo5@naver.com>

Lightweight Image Super-Resolution with Information Multi-distillation Network
------------------------------------------------------------------------------
Abstract
--------
  * CNN을 이용한 최근 깊은 네트워크의 강한 표현 능력을 이용한 SR의 많은 발전
  * 과도한 Conv의 사용은 저전력 시스템에 SR 기술을 적용하는데 제한시킴
  * 이전의 SR방법들은 임의의 확대배율로 SR을 적용하는데 문제를 가지고 있음 
  * 이러한 문제를  해결하기위해 Lightweight Image Super-Resolution with Information Multi-distillation Network를 제안
  * Distillation module은 단계별로 계층 특징들을 추출함
  * Fusion module은 제안된 Contrast aware channel attention에 의하여 평가된 후보 특징들의 중요성에 따라 다시 집계가됨
  * 어떤 크기의 real img를 처리하기위해 Adaptive cropping strategy를 제안함

Introduction
------------
  * SISR은 저해상도로부터 고해상도를 얻는 과제
  * 저해상도 이미지를 고해상도로 복원을 해야 하는데, 복원해야 하는 타겟인 고해상도의 이미지가 정답이 여러 개 존재-> ill posed problem을 해결하기위해 다양한 SR Network들이 제안됨
  * 기존의 DRRN, MemNet같은 경우 입력으로 보간된 영상을 사용하고 다운 샘플링 과정을 사용하지 않아 거대한 계산량을 가지며, 학습과 테스트를 진행할 때 거대한 그래픽 메모리를 차지함
  * EDSR, RDN, RCAN, RNAN등과 같은 네트워크들은 깊은 네트워크이며 성능을 올리기위해 Conv를 다수 추가하였음 -> 많은 수의 파라미터, 거대한 메모리, 느린 학습, 테스트 속도의 문제를 가지고 있음
  * 파라미터의 수를 줄이기위한 재귀적인 방법 또는 파라미터 공유 -> 모델의 크기는 줄일 수 있지만 재귀로인한 성능저하의 손실을 막기위해 네트워크의 깊이 또는 넓이가 증가시킴, SR을 처리하는데 많은 계산 추론을 소비함
  * 경량화되고 효과적인 네트워크인 CARN같은 경우 PSNR이 다소 떨어짐
  * IDN같이 이전의 추출된 특징을 나누는 Net은 어느 정도 CARN보다 성능을 얻었지만 아직도 SR성능의 관점에서 미흡함
  * 추론속도에 영향을 미치는 또다른 요소로 네트워크 깊이가 있음 -> 테스트 단계에서 이전 계층과 다음 계층에는 종속성이 있음, 현재 레이어의 계산을 수행하려면 이전 계산이 완료 될 때까지 기다려야함, 그러나 각 레이어에서 여러 컨벌루션 연산을 병렬로 처리 할 수 있음(문제1)
  * 이전 제안된 네트워크들중 입력으로 보간된 영상을 사용하여 계산량의 문제((문제2)
  * 제안된 IMDB는 세분화된 레벨에서 특징들을 추출하고, 일부 부분적인 정보를 유지시킴 
  * concat된  다양한 정제 된 정보를 향상시키기 Contrast-aware channel attention을 이용

Contribution
------------
  * 제안하는 IMDB와 CCA 덕분에 파라미터의 수를 줄일 수 있었음 
  * 어떤 임의의 크기로 영상을 처리하도록 할 수 있은 ACS를 제안, 계산비용, 메모리량, 추론시간을 줄 일 수 있음 
  * 다양한 실험을 통해서 추론시간에 실제로 영향을 끼치는 요소를 발견했고, 네트워크의 깊이는 실행 속도에 영향을 끼치는 것을 확인 하였음, 제안하는 네트워크는 시각적 퀄리티, 추론 속도, 메모리량에 있어 좋은 밸런스를 갖음

Attention Model
---------------
  * Attention model은 더 유용한 정보를 사용하게끔 만드는 것, 계산적으로 효과적이면서 표현능력을 올림

Network
-------
![image](https://user-images.githubusercontent.com/61686244/108628213-06573c80-749d-11eb-833c-99aaf6e96094.png)

![image](https://user-images.githubusercontent.com/61686244/108628219-09eac380-749d-11eb-8f74-e6cce0b72186.png)

![image](https://user-images.githubusercontent.com/61686244/108628230-1242fe80-749d-11eb-8b28-e3cbc7e80ce2.png)


  * LR 특징 추출단계, 3x3 Conv 사용 64 출력 후 IMDBs입력, 하나의 IMDB는 각 단계별 중간 특징들(split된 특징들)을 concat하여 CCA layer를 통과한 뒤 1x1 Conv를 이용하여 융합
  * 이런 구조는 수집 된 정보의 무결성을 보장하고 매우 적은 매개 변수를 증가시켜 SR 성능을 더욱 향상시킬 수 있음 

Information multi-distillation Block
------------------------------------
  * progressive refinement module, contrast-aware channel attention(CCA), 1x1 conv로 구성,residual connection 사용 
  * IMDB의 주 아이디어는 조금씩 유용한 특징들을 뽑아내는 것 
  * PRM 구조의 hyper parameter는 Table1에 나타나 있음 

![image](https://user-images.githubusercontent.com/61686244/108628258-39013500-749d-11eb-9e2b-1bb67fe12ddd.png)

  * progressive refinement module에서 첫 번째 3x3 Conv를 이용하여 64개의 특징을 추출, 후에 channel split을 이용하여 이전의 특징을 나눔 channel split을 하게 되면 2개의 파트로 나눠지게 되는데 하나는 보존되고 다른 하나는 calculation unit으로 들어가게됨 
  * 보존된 부분은 정제된 특징으로 볼 수 있음 

![image](https://user-images.githubusercontent.com/61686244/108628266-48807e00-749d-11eb-91a5-7d9d3def8256.png)

  * 정제된 특징은 후에 concat됨 다음과 같이 표현 할 수 있음 

![image](https://user-images.githubusercontent.com/61686244/108628270-53d3a980-749d-11eb-9475-148640465aaf.png)

Contrast-aware Channel Attention Layer
--------------------------------------
  * 초기에 Channel Attention은 image classification에서 사용, High level field에서 특징 맵의 중요성은 high-value areas에 의존 
  * global average/maximum pooling은 global 정보를 사용 할 수 있음(High, mid level vision과 같은)
  * average pooling은 PSNR을 향상시킬 수 있지만, 섬세함, 엣지 같은 부분(SSIM)을 살리기에는 부족한 점들이 있음 
  * Contrast-aware channel attention layer은 low level vision에 더 적합한 방법->global average pooling을 표준 편차와 평균의 합으로 대체

![image](https://user-images.githubusercontent.com/61686244/108628294-71a10e80-749d-11eb-90a3-51dd77e6f107.png)

  * Contrast : 영상의 화소 분포가 좀 더 넒은 영역에 걸쳐서 퍼지도록 펴주는 작업
  * GAP(global average pooling) : 특징을 1차원 벡터로 만듦(기존 pooling보다 더 급격하게 줄임)

Adaptive Cropping Strategy
--------------------------
  * 기존의 IMDN의 구조를 조금 변형시킴, 2개의 downsampling layer를 앞단에 둠, 하나의 모델로 모든 확대 배율 SR을 사용가능
  * 높이와 너비를 4로 나눌수 없을 때 첫 번째로 영상을 4등분하여 IMDN_AS에 입력
  * 그림 4와 같이 ACS를 통해서 overlapped된 영상의 4개의 patch를 얻을 수 있음

![image](https://user-images.githubusercontent.com/61686244/108628307-8ed5dd00-749d-11eb-9eaf-3de2bebab048.png)

![image](https://user-images.githubusercontent.com/61686244/108628308-94332780-749d-11eb-9dd3-3eeb09c99838.png)

![image](https://user-images.githubusercontent.com/61686244/108628315-985f4500-749d-11eb-84af-06f78660c104.png)

![image](https://user-images.githubusercontent.com/61686244/108628318-9d23f900-749d-11eb-955f-c5354c079407.png)

![image](https://user-images.githubusercontent.com/61686244/108628322-a1501680-749d-11eb-8495-88b1b20a26c4.png)


Experiment
----------
![image](https://user-images.githubusercontent.com/61686244/108628329-ac0aab80-749d-11eb-977d-3734984ac3a7.png)

![image](https://user-images.githubusercontent.com/61686244/108628334-b036c900-749d-11eb-96e9-8a5f51058018.png)

![image](https://user-images.githubusercontent.com/61686244/108628336-b3ca5000-749d-11eb-8ed6-ef29bdc8a2f6.png)

![image](https://user-images.githubusercontent.com/61686244/108628340-baf15e00-749d-11eb-95e2-c078da2efe42.png)

![image](https://user-images.githubusercontent.com/61686244/108628343-bf1d7b80-749d-11eb-96e0-0bc8fb5d79ca.png)

![image](https://user-images.githubusercontent.com/61686244/108628347-c3e22f80-749d-11eb-894a-986adabc51e6.png)

![image](https://user-images.githubusercontent.com/61686244/108628352-c93f7a00-749d-11eb-800b-e9bd5519c805.png)

![image](https://user-images.githubusercontent.com/61686244/108628357-cd6b9780-749d-11eb-98ee-1cd9f13f1cb2.png)

 * Information multi-distillation network for lightweight and accurate SISR제안
 * 단계별 계층적인 특징을 추출하기위한 Progressive Refinement Module 설계
 * 제안된 Contrast Aware Channel Attention을 이용하여 SR성능 향상
 * 임의의 확대 배율에도 SR이 가능한 Adaptive Cropping Strategy 제안
 * 영상 퀄리티, 실행 속도, 메모리량 측면에서 좋은 균형을 가지고 있음 



