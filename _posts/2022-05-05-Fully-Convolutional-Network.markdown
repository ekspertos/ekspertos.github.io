---
layout: post
title:  "Fully Convolutional Networks"
subtitle: "Fully Convolutional Networks"
categories: projects
tags: blog github pages jekyll spacy
comments: true
---


https://deep-learning-study.tistory.com/562

end-to-end, pixels-to-pixels 학습이 되는 Convolutional network 이다.

하나도 못알아듣겠으~

1. Fast image processing with fully-convolutional networks

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

```
Fully convolutional neural networks (FCNs) have been shown to achieve the state-of-theart performance on the task of classifying time series sequences. We propose the augmentation of fully
convolutional networks with long short term memory recurrent neural network (LSTM RNN) sub-modules
for time series classification. Our proposed models significantly enhance the performance of fully convolutional networks with a nominal increase in model size and require minimal preprocessing of the data set.

In this paper, we improve the performance of FCN by
augmenting the FCN module with either a Long Short
Term Recurrent Neural Network (LSTM RNN) sub-module,
called LSTM-FCN, or a LSTM RNN with attention, called
ALSTM-FCN. In addition, the Attention LSTM can also
be used detect regions of the input sequence that contribute
to the class label through the context vector of the Attention LSTM cells. Results indicate the new proposed models,
LSTM-FCN and ALSTM-FCN, dramatically improve performance on the University of California Riverside (UCR)
Benchmark datasets




Temporal convolutions have proven to be an effective learning model for time series classification problems [11]. Fully
Convolutional Networks, comprised of temporal convolutions, are typically used as feature extractors. Global average
pooling [29] is used to reduce the number of parameters in
the model prior to classification. In the proposed models, the
fully convolutional block is augmented by an LSTM block
followed by dropout [30], as shown in Figure 1

The fully convolutional block consists of three stacked
temporal convolutional blocks with filter sizes of 128, 256,
and 128 respectively. Each convolutional block is identical to the convolution block in the CNN architecture
proposed by Wang et al. [11]. Each block consists of
a temporal convolutional layer, which is accompanied by
batch normalization [23] (momentum of 0.99, epsilon of
0.001) and followed by a ReLU activation function. Finally,
global average pooling is applied after the final convolution
block.
Simultaneously, the time series input is conveyed into a
dimension shuffle layer (explained more in Section III-B).
The transformed time series from the dimension shuffle is
then passed into the LSTM block. The LSTM block, comprising of either a general LSTM layer or an Attention LSTM
layer, is followed by a dropout. The output of the global
pooling layer and the LSTM block is concatenated and passed
onto a softmax classification layer.


```

4.

```
FCN has shown compelling quality and efﬁciency for semantic segmentation on images [14]. Each output pixel
is a classiﬁer corresponding to the receptive ﬁeld and the networks can thus be trained pixel-to-pixel
given the category-wise semantic segmentation annotation. In our problem settings, the FCN is performed
as a feature extractor. Its ﬁnal output still comes from the softmax layer. The basic block is a
convolutional layer followed by a batch normalization layer [15] and a ReLU activation layer. The
convolution operation is fulﬁlled by three 1-D kernels with the sizes {8,5,3} without striding. The basic convolution block is
```
receptive field 는 출력 레이어의 뉴런 하나에 영향을 미치는 입력 뉴런들의 공간 크기이다


이미지에서 dense layer를 사용하는 것은 무의미하다?
feature에 따라서 dense layer를 하는 것이 아니라
이미지 HxW 중에 한 축에 대해서 dense layer 연산을 하기 때문이다.


5. Amulet: Aggregating Multi-Level Convolutional Features for Salient Object Detection

```
In recent years, fully convolutional networks (FCNs),
adaptively extracting high-level semantic information from
raw images, have shown impressive results in many dense
labeling tasks, such as image segmentation [28, 31, 6],
generic object extraction [25, 13], pose estimation [48] and
contour detection [45].


```
```

Fully Convolutional Neural Network: (FCN), originally
proposed in [18], is considered a competitive architecture
yielding the second best results when evaluated on the
UCR/UEA archive (see Table I). This network is comprised
of three convolutional layers, each one performing a nonlinear transformation of
 the input time series. A global average
pooling operation is used before the final softmax classifier, thus reducing
drastically the number of parameters in
a network and allowing an architecture that is invariant to
the length of the input time series. The latter characteristic
motivated us to perform a transfer learning technique in [27],
and ensembling the resulting neural networks which is later
discussed in Section a  
```


```
originally proposed in [18], is considered a competitive architecture yielding the second best ...
This network is comprised of three convolution layers, each one performing a non linear transformeraion
of the input time series. A global average pooling operation is used before the final
softmax clasifier, thus reducing drastically the number of parameters im a network and
allowing an architecture that is invariant to the input time series

```
