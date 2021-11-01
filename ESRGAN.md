# ESRGAN # 

- **SRGAN**은 single image super-resolution에서 현실적인 질감(realistic texture)을 만들어낼 수 있긴 하지만 디테일적인 면에서 종종 이상한 인공적인 것을 만들어낸다.

- 시각적인 품질을 더욱 향상시키기 위해 더욱 성능이 향상된 SRGAN 즉, **Enhanced SRGAN(ESRGAN)** 을 제안하게 되었다.

- 기존 SRGAN에 있던 Batch Normalization을 없앤 Residual-in-Residual Dense Block(RRDB)를 도입하였고 relativistic GAN으로부터 아이디어를 얻어 Discriminator가 절대값 대신 relative realness를 예측하도록 했다.

- 또한 활성화(activation)전에 feature들을 사용함으로써 perceptural loss를 개선하였고 이를 통해 밝기 일관성과 질감 복원에 대해 강력한 supervision을 제공할 수 있다

### ◾ Network Architecture ###

  SRGAN에 의해 복원된 이미지의 품질을 더욱 향상시키기 위해 Generator의 구조에서 2가지를 수정했다.

  1. 모든 Batch Normalization layer들을 제거

  2. 기존의 basic block을 multi-level residual network와 dense connection을 결합한 Residual-in-Residual Dense Block(RRDB)로 대체

  ![image](https://user-images.githubusercontent.com/66320010/139660485-6a61dec9-47b3-4068-b7be-8defc8a5183c.png)

  ✅ BN(Batch Normalization) layer 제거
  
  - BN layer들은 **학습**하는 동안 한 batch에서 평균과 분산을 사용하여 특징을 정규화시키고 **테스트**하는 동안 전체 학습 dataset으로부터 추정된 평균과 분산을 사용한다.

  - 학습 datasets의 통계치와 테스트 datasets의 통계치가 서로 많이 다르면 BN layer들은 이상한 인공물(artifacts)을 만들어내는 경향이 있고 일반화 능력을 제한하는데, 더 깊고 GAN framework에서 학습된 네트워크 일수록 이러한 인공물들을 더 만들어내는 것처럼 보인다.

  - 따라서 안정적인 학습과 일관된 성능을 위해 BN layer를 제거하였고 이로 인해 일반화 능력이 생기고 계산 복잡성과 메모리 사용량이 감소된다.

  ✅ Residual-in-Residual Dense Block (RRDB)
  
  - SRGAN 구조를 유지하며 새로운 RRDB를 사용한다. RRDB는 기존 SRGAN의 residual block보다 더 깊고 복잡한 구조이며 메인 경로에서 dense block을 사용하는데 이로인해 네트워크 용량이 더욱 커지게 된다.

  - 향상된 구조 외에도 매우 깊은 네트워크를 학습시키는 것을 용이하게 하기위해 몇몇 기술들을 이용한다.

    1. Residual scaling
    
        불안정성을 방지하기 위해 메인 경로에 추가하기 전에 0과 1사이의 상수를 곱하여 residual 스케일을 축소한다.
        
    2. Smaller initialization

        residual 구조는 초기 파라미터 분산이 작을수록 trian하기 더 쉽다.
        
### ◾ Relativistic Discriminator ### 

Residual-in-Residual Dense Block (RRDB) 를 통해 Generator를 발전시켰다면 Discriminator는 Relativistic GAN을 기반으로 발전한다.

SRGAN의 Discriminator와의 차이는 SRGAN의 판별자는 이미지가 real인지 fake인지에 대해 판별을 한다면 ESRGAN에서는 Real image Xr이 Fake imgage Xf보다 realistic할(현실적일) 확률을 예측한다.

![image](https://user-images.githubusercontent.com/66320010/139663033-db15bf8d-5aab-4558-9ac5-16d7f4e74eec.png)

- Standard Discriminator in SRGAN

  ![image](https://user-images.githubusercontent.com/66320010/139663512-65c2413d-b240-4ae7-9148-afd234e1db06.png)
  
- Relativistic Discriminator

  ![image](https://user-images.githubusercontent.com/66320010/139663606-0cd6b541-5088-410f-8819-c935886ef0df.png)
  
  E : 기대값 (미니배치에서 모든 fake에 대한 average operation)

  σ : Sigmoid function
  
- Relativistic average Discriminator loss

  ![image](https://user-images.githubusercontent.com/66320010/139663845-f9ba110e-aa69-4056-b4e4-c572d4fd3354.png)

- Adversarial loss for Generator

  ![image](https://user-images.githubusercontent.com/66320010/139663962-03931ff5-f11e-4863-a03d-a4e17c3724c2.png)
  
  Xf : Generator에 Low Resolution Image를 input으로 넣어서 나온 output
  
  SRGAN에서는 Generator 부분에서만 영향이 있는 반면, ESRGAN에서는 Adversarial loss를 통해서 generated data, real data 둘 다 train이 가능해졌다.
  
### ◾ Perceptual Loss ### 
  
SRGAN에서는 **activation 이후 feature 학습**을 했다면 ESRGAN에서는 **activation  이전**에 feature들을 constraining함에 따라 perceptual loss를 더 효과적으로 발전시켰고 기존의 2가지 문제점을 해결했다.
  
1. 매우 깊은 네트워크 후에 활성화된 feature들은 매우 sparse함으로 질 낮은 성능으로 이어짐
  
2. 활성화 후 feature들을 사용하는 것은 ground-truth 이미지와 비교했을 때 일관성 없는 복원된 밝기를 야기
  
![image](https://user-images.githubusercontent.com/66320010/139664615-25c26730-ece6-4a8d-a3e0-1de8e9d53692.png)  
  
**활성화 후** 대부분의 feature들은 소극적(inactive)으로 된 반면, **활성화 전** feature들은 더 많은 정보를 가지고 있다.
  
- Generator total loss

  ![image](https://user-images.githubusercontent.com/66320010/139664875-b8598226-6dff-48e7-ac5e-01fd7cd89949.png)
  
### ◾ Network Interpolation ###   

GAN기반 방법의 이상한 noise를 제거함과 동시에 양호한 지각 품질을 유지하기 위해 유연하고 효과적인 전략으로 **network interpolation**을 제안했다.

구체적으로 먼저 PSNR oriented network(Gpanr)을 학습한 후 미세한 조정(fine-tuning)을 통해 GAN-based network(Ggan)를 얻었다. 

보간된 모델(Ginterp)을 도출하기 위해 두 네트워크의 모들 해당 매개변수들을 보간했다.

![image](https://user-images.githubusercontent.com/66320010/139667422-9d05073b-0813-4a87-a284-29a82946c9fa.png)

- alpha value는 0 ~ 1 사이의 interpolation parameter
  
해당 network interpolation에는 두 가지 장점이 있다.
  
  1. Interpolated model은 소개된 artifact(인공물)없이도 실현 가능한 α에 대해 의미 있는 결과를 만들어낼 수 있다.
  
  2. model을 retrain 하지 않고서도, perceptual quality(지각 품질)와 fidelity(정확도?)의 균형을 계속 유지할 수 있다.

추가적으로 PSNR-oriented method와 GAN-based method를 효과적으로 균형을 이룰 method를 연구하고 있는데, 예로 네트워크 파라미터를 보간하는거 대신에 output이미지를 보간이 있다.

하지만 이것의 단점은 noise와 blur사이의 적절한 trade off가 이루어지지 않는다고 한다. interpolated image가 너무 blur하거나 noise가 발생할 수 있기 때문이라고 한다.

다른 method로는 content loss와 adversarial loss의 weights를 튜닝하는 것인데 이는 tuning loss weights와 fine tuning한 네트워크가 필요해서 이미지 스타일을 계속 다루기에는 cost가 너무 많이 든다는 단점이 있다.
  
### ◾ Experiment Results ###   
  
![image](https://user-images.githubusercontent.com/66320010/139668385-42114763-9fc5-462c-8cf1-84d0a521d405.png)  
  
SRCNN과 EDSR, RCAN 등 여러 모델과 quality를 비교했을 때의 결과이다.

Peceptual quality에 있어서 표준 평가 지표는 없지만 시각적으로 ESRGAN이 좋은 것을 확인할 수 있는데, sharpness, detail에 있어 성능이 두드러진다.  
  
### ◾ Conclusion ###  

- 이전의 SR 방법들 보다 일관되게 지각 품질이 더 좋은 ESRGAN 모델을 제안했고, 이 방법은 PIRM-SR Challenge에서 1등을 차지했다.

- 배치 정규화가 없는 RDDB block을 모델 구조에 넣었고, residual scaling, smaller initialization을 통해서 deep model 훈련이 잘 이루어지게 되었다.

- Discriminator에서는 relativistic Gan을 사용하여, 이미지가 realistic한지 안 한지 판별할 수 있게 함으로써, 이를 통하여 Generator는 더 세부적인 texture의 복원이 가능했다.

- feature들을 activation 이전에 사용함으로써 perceptual loss 부분에서 향상시킬 수 있었다. 결과적으로 정확한 밝기와 사실적인 texture를 복원할 수 있었다.
