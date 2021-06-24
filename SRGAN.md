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

### Related Work ###

📍 Loss fucntion 

 - MSE와 같은 픽셀 단위 손실 함수는 texture와 같은 손실된 고주파 세부 정보를 복구에 어려움을 겪는다.

 - MSE를 최소화하면 일반적으로 지나치게 부드럽게(smooth) 되기 때문에 poor perceptual quality인 솔루션의 픽셀 단위 평균을 찾는 것이 좋다.

 - 다음 그림은 MSE 최소화의 문제점을 설명한다.

![image](https://user-images.githubusercontent.com/66320010/123228605-873c4880-d510-11eb-97a8-344993aa58b9.png)

 - 중간의 이미지는 MSE를 기반으로 하는 결과값이며 이는 주변에 다양한 high texture detail들이 smooth reconstruct된 평균이라 할 수 있다.

   ✔ 부드러운 재구성을 생성하기 위해 텍스처 세부 사항이 높은 여러 잠재적 솔루션을 평균화하는 것

 - 그렇기 때문에 저자는 MSE대신 perceptual similarity에 중점을 둔 loss function을 제시했다. 이는 앞서 제시된 pretrain된 **VGG 19모델에서 추출된 feature map들 사이의 euclidean distance를 계산하는 방식**이었다. 

 - 이를 통해 super-resolution과 artistic style-transfer에서 지각적으로 더 설득력있는 결과를 얻었다.

### Contribution ###

저자는 이 논문이 기여한 바를 크게 3가지로 나누었다.

✅ MSE에 최적화 된 16 블록 깊이의 SRResNet을 사용하여 PSNR 및 구조적 유사성(SSIM)으로 측정된 4배 upscaling로 이미지 SR에 대한 새로운 상태를 설정

✅ 새로운 perceptual loss에 최적화 된 GAN을 기반으로 하는 SRGAN을 제시. MSE를 기반으로 하는 content loss를 VGG 네트워크의 feature map을 기반으로 계산된 loss로 대체하였고, 이는 픽셀 공간의 변화에 더욱 불변하다 할 수 있음

✅ MOS test통해 SRGAN이 높은 upscaling요인을 가진 SR이미지를 추정하기 위한 최첨단 기술임을 확인(MOS test에서도 좋은 성능을 보임)

## Method ## 

- single image super resolution(SISR)은 𝐼^𝐿𝑅 로 부터 고화질의 super resolved된 이미지 𝐼^𝑆𝑅을 추정하는 것이 목표이다.

- C개의 채널이 있는 이미지라면 실제 tensor사이즈가 W * H * C라면 𝐼^𝐻𝑅과 𝐼^𝑆𝑅은 𝑟𝑊 * 𝑟𝐻 * 𝐶 가 된다.

- **결국 이 논문의 궁극적인 목표는 주어진 LR 입력값을 상응하는 HR로 변환하기 위한 generating 함수 G를 학습시키는 것**이며 아래의 수식을 해결하는 것이다.

![image](https://user-images.githubusercontent.com/66320010/123236178-89ee6c00-d517-11eb-9c40-7c4aac50bcaa.png)

![image](https://user-images.githubusercontent.com/66320010/123236511-d174f800-d517-11eb-8d52-f1e917ef8f02.png)

- 𝐼^𝐻𝑅 : High resolution image, 오직 training 과정에서만 사용 가능
 
  𝐼^𝑆𝑅 : Super resolved image, 𝐼^𝐿𝑅에 super resolution을 적용한 이미지
 
  𝐼^𝐿𝑅 : Low resolution image, 𝐼^𝐻𝑅에 factor r의 값만큼 가우시안 필터를 이용하여 다운 샘플링하면 𝐼^𝐿𝑅

- 이 과정에서 앞서 말한 pereptual loss인 𝑙^𝑆𝑅을 디자인하였다.

- 복구된 SR이미지의 고유한 바람직한 특성을 모델링하는 여러 손실 구성 요소의 가중치 조합으로 pereptual loss인 𝑙^𝑆𝑅을 설계한다.

### Adversarial network architecture ### 

![image](https://user-images.githubusercontent.com/66320010/123237073-4cd6a980-d518-11eb-8140-a7eb2422d37e.png)

위 식은 Goodfellow가 제안한 GAN loss를 구하기 위한 식이다. 

이 식의 목표는 super-resolved된 이미지를 실제 이미지와 구분하기 위해서 훈련된 미분 가능한 discriminator network D를 속이기 위한 generative model G를 학습하는 것이 가능하게 하는 것이다.

이러한 접근을 통해 generator는 D가 구분하기 매우 어려운 실제 이미지와 매우 유사한 solution을 생성하는 것을 학습할 수 있다.

💡 **이러한 부분이 super-resolution에서 MSE와 같은 pixel wise error를 최소화 하는 솔루션과는 대조적이다.**

![image](https://user-images.githubusercontent.com/66320010/123237702-d8e8d100-d518-11eb-8b18-4044bdbf0cfa.png)

- 위 그림은 generator와 discriminator network의 구조를 시각화 한 것이다.

- 매우 깊은 generator network G의 핵심에는 레이아웃과 동일한 B residual block이 있다. 

- 특히 이 모델은 3 * 3의 작은 크기의 커널, 64개의 feature map이 있는 2개의 컨볼루션 layer를 사용하고 활성화 함수로 batch-normalization layer와 parametricReLU를 사용한다.

- 일반적으로 convolution layer를 이용하면 그 image의 차원은 작아지거나 동일하지만 super resolution은 image의 dimension을 증가시켜야 한다.

- 여기서 pixel을 증가시키기 위해 이용된 방식이 sub-pixel convolution이다.

- 이는 Shi et al.에서 소개된 방법으로 input image의 feature map을 논문에서 제시하는 방법으로 조합하여 pixel의 수를 증가시키는 방식이다.

- D network는 VGG 네트워크에서와 같이 64에서 512 커널로 3 × 3 필터 커널 수가 2 배 증가하는 8 개의 컨벌루션 계층이 포함되어 있다. 결과로 생성되는 512개의 feature map은 두 개의 Dense layer와 최종 시그모이드 활성화 함수가 뒤따라 샘플 분류 확률을 얻는다.

### Perceptual loss function ###

![image](https://user-images.githubusercontent.com/66320010/123240984-bc01cd00-d51b-11eb-83a4-8544d7243236.png)

- 해당 논문에서 정의한 perceptual loss function 𝑙^𝑆𝑅은 generator network성능에 있어 중요하다.

- 𝑙^𝑆𝑅은 일반적으로 MSE를 기반으로 모델링되지만 이 논문에서는 지각적으로 관련된 특성과 관련하여 솔루션을 평가하는 loss function을 설계한다.

- perceptual loss를 adversarial loss와 content loss의 합으로 정의하였다. 두 loss에 대해서는 뒤에 설명하겠다.

### Content loss ###

![image](https://user-images.githubusercontent.com/66320010/123241985-8f01ea00-d51c-11eb-9045-39dd60275132.png)

- 일반적으로 사용되는 pixel-wise MSE loss는 위의 식과 같다.

- 이 식은 PSNR을 향상시키는데 좋은 효과를 보이지만 고주파 content가 부족하여 사람이 보기에는 과하게 smooth하여 만족스럽지 못한 이미지를 만들기도 한다.

- 그래서 이러한 pixel-wise loss에 의존하기 보다는 pretrained된 19개의 layer를 갖는 VGG network의 activation layer에 기반한 VGG loss를 새로 정의한다. 

- 𝑊_(𝑖,𝑗)와 𝐻_(𝑖,𝑗)는 VGG network의 각 feature map의 차원을 의미한다.

![image](https://user-images.githubusercontent.com/66320010/123242633-341cc280-d51d-11eb-9c7c-0588628826a3.png)

### Adversarial loss ###

![image](https://user-images.githubusercontent.com/66320010/123242770-5282be00-d51d-11eb-80da-1fd3e143d3f7.png)

- 지금까지 설명한 content loss외에도 GAN의 generative한 부분도 perceptual loss에 추가한다. 

- 이것은 논문이 제시하는 network가 discriminator network를 속이기 위해서 nature 이미지의 다양성을 가질 수 있도록 한다. 

- generative loss인 𝑙_𝐺𝑒𝑛^𝑆𝑅은 discriminator 𝐷_𝜃_𝐷(𝐺_𝜃_𝐺(𝐼^𝐿𝑅))의 확률을 기반으로 정의된다.

- 여기서 𝐷_𝜃_𝐷(𝐺_𝜃_𝐺(𝐼^𝐿𝑅)은 재구성된 image 𝐺_𝜃_𝐺(𝐼^𝐿𝑅)이 nature 𝐻𝑅 image일 확률이며 더 나은 gradient를 위해서 𝑙𝑜𝑔(1- 𝐷_𝜃_𝐷(𝐺_𝜃_𝐺(𝐼^𝐿𝑅)) 대신 - 𝑙𝑜𝑔𝐷_𝜃_𝐷(𝐺_𝜃_𝐺(𝐼^𝐿𝑅))을 최소화하여 사용한다.

## Experiments ## 

![image](https://user-images.githubusercontent.com/66320010/123244236-ae017b80-d51e-11eb-8f3e-72aa804b7da5.png)








