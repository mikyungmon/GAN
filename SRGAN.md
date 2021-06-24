# SRGAN 논문 리뷰 # 

🔎**SRGAN**이란 ? 

 => Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network의 약자로 2017년에 나온 논문

 ✔*Super-resolution(SR)은 low resolution image(저해상도 이미지)로부터 high resolution image(고해상도 이미지)를 추정하는 분야이다. SR에 딥러닝 기법이 적용된 후로 MSE를 이용한 다양한 모델이 나왔다. CNN을 활용하여 MSE를 줄여나가는 기존의 다양한 모델들은 PSNR(super resolution을 평가하는 수치)값을 높이면서 높은 성능을 가지며 해상도를 높이는데 성공한다.*
 
 하지만 기존의 MSE를 활용한 방법들에는 한 가지 문제점이 존재한다.
 
 SR을 통해 large upscaling을 할 때 texture detail을 제대로 회복시키지 못하는 문제가 발생한다.
 
 즉, 내가 원하는 만큼의 high resolution image를 만들어 내지 못한다.
 
 **이를 해결하기 위해서 해당 논문에서는 GAN의 개념을 도입한 새로운 모델을 제시한다.**

 ## Abstract ## 

- 더 빠르고 깊은 CNN을 사용한 single image super-resolution의 획기적인 발전에도 불구하고 한가지 중요한 문제가 남아있다.

  ▶ **대규모 upscaling에서 super-resolution을 수행할 때 미세한 texture details은 어떻게 복구할 것인가?**

- 최근 연구는 MSE(mean squared reconstruction error)를 최소화하는데 주로 초점을 맞추었다.

- 그 결과 high peak signal-to-noise ratio(PSNR : super resolution을 평가하는 수치)를 가지지만 고주파 세부 정보가 부족하고 perceptually(지각적으로?) 불만족스럽다. 

  즉, super-resolution을 평가하는 수치가 높아도 실제 눈으로 확인하면 해상도가 그리 높지 않다는 것
  
**따라서 본 논문에서는 SR을 위한 GAN인 SRGAN을 제시한다. 이것은 4배 upscaling에 대해 realistic한 nature image를 추론할 수 있는 최초의 framework이다.**

- 이를 위해 adversarial loss와 content loss로 구성된 perceptual loss function을 제안한다.

  **adversarial loss(적대적 손실)** : super resolved image와 original photo realistic image를 구별하는 discriminator(판별자) network를 train
  
  **content loss(콘텐츠 손실)** : pixel space에서 similarity대신 perceptual similarity를 train

## Introduction ## 

Low Resolution(저해상도)의 이미지를 High Resolution(고해상도)으로 바꾸는 것을 super resolution(SR)이라고 한다. 

이 SR에서의 문제점은 super resolution을 통해 고화질의 이미지가 만들어졌을때, **texture의 detail이 부족하다는 점이다.** 

SR 알고리즘들의 목표는 보통 **복구된 HR 이미지와 Ground Truth 이미지 사이의 MSE를 최소화** 하는 것인데, 이는 이미지의 품질을 평가하는 PSNR의 값을 향상시키는 효과가 있다. 

하지만, 이 MSE와 PSNR은 pixel-wise 이미지 차이로 정의되어 있기 때문에 high texture detail과 같은 지각적인(perceptually) 부분과 관련있는 차이점에 대해서는 그 능력이 한정되어 있다. 

그래서 이 논문에서는 skip-connection이 있는 ResNet을 이용하고 MSE만을 최적화의 수단으로 이용하는것에서 벗어난 super-resolution generative adversarial network(SRGAN)을 제시한다.

이전 연구와는 다르게 VGG network의 high-level feature map을 이용한 독보적인 perceptual loss를 제시하고 이를 기반으로 만들어진 다음 그림과 같은 4배 upscale된 예시를 보여준다.

![image](https://user-images.githubusercontent.com/66320010/123226149-565b1400-d50e-11eb-8e71-ee4fcb45267d.png)

위 그림은 upscale들의 성능을 보여주며, SRResNet의 이미지를 보면 실제로 SRGAN과 비교했을때 디테일한 부분들에 대해서 완성도가 부족함을 볼 수 있다.

또한 가장 높은 PSNR이 perceptual적으로 더 나은 SR결과를 반드시 반영하는 것이 아님을 확인할 수 있다.

















