https://deep-learning-study.tistory.com/562

end-to-end, pixels-to-pixels 학습이 되는 Convolutional network 이다.

하나도 못알아듣겠으~

1. FCN 논문을 인용한 눈문들
- Fast image processing with fully-convolutional networks
```
Yan et al. use a fully-connected
network that operates on each pixel separately. The network itself has a receptive field of a single pixel. Contextual information is only provided by hand-crafted input features, instead of being collected adaptively by the network.
This places a substantial burden on manual feature design.
In contrast, our approximator is a single convolutional network that is trained end-to-end, aggregates spatial context
from the image as needed, and does not rely on extraneous
modules or preprocessing. This leads to much greater generality, higher accuracy, and faster runtimes.

```

2. Visual tracking with fully convolutional networks
```

Our feature analysis is conducted based on the 16-layer VGG network
pre-trained on the ImageNet image classification task [4],
which consists of 13 convolutional layers followed by 3 fully connected layers. We mainly focus on the conv4-3 layer
(the 10-th convolutional layer) and the conv5-3 layer (the
13-th convolutional layer), both of which generate 512 feature maps
```

3. LSTM fully convolutional networks for time series classification
