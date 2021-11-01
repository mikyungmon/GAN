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

