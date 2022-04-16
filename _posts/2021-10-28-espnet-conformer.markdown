---
layout: post
title:  "ESPnet Conformer"
subtitle: "Conformer"
categories: projects
tags: blog github pages jekyll spacy transformer
comments: true
---


> espnet에서 conformer가 어떻게 implement 되어 있는지 알아본다
>
> https://github.com/espnet/espnet/blob/master/espnet2/asr/encoder/conformer_encoder.py
>
> https://github.com/espnet/espnet/blob/master/egs2/ksponspeech/asr1/conf/tuning/train_asr_conformer8_n_fft512_hop_length256.yaml
>
> https://www.youtube.com/watch?v=DwaBQbqh5aE

```
encoder: conformer
encoder_conf:
    output_size: 512
    attention_heads: 8
    linear_units: 2048
    num_blocks: 12
    dropout_rate: 0.1
    positional_dropout_rate: 0.1
    attention_dropout_rate: 0.1
    input_layer: conv2d
    normalize_before: true
    macaron_style: true
    pos_enc_layer_type: "rel_pos"
    selfattention_layer_type: "rel_selfattn"
    activation_type: "swish"
    use_cnn_module:  true
    cnn_module_kernel: 31
```

__ESPnetASRModel__ https://github.com/espnet/espnet/blob/5c21f63e45e0961a5d817017c282b0cafd68a3aa/espnet2/asr/espnet_model.py#L142

__PositionalEmbedding__ https://github.com/espnet/espnet/blob/5c21f63e45e0961a5d817017c282b0cafd68a3aa/espnet/nets/pytorch_backend/transformer/embedding.py#L173


__subsampling + positionembedding + linearlayer__ https://github.com/espnet/espnet/blob/5c21f63e45e0961a5d817017c282b0cafd68a3aa/espnet/nets/pytorch_backend/transformer/subsampling.py#L44

__conformer model__ https://github.com/espnet/espnet/blob/master/espnet2/asr/encoder/conformer_encoder.py

__conformer block__ https://github.com/espnet/espnet/blob/5c21f63e45e0961a5d817017c282b0cafd68a3aa/espnet/nets/pytorch_backend/conformer/encoder_layer.py#L17


1. pos_enc_layer_type = "rel_pos"
> https://arxiv.org/pdf/2005.09940.pdf 에 소개된 positional encoding 방식이다

positional embedding을 transformer에서 해주지 않을 경우 "Queen And King" 과 "King And Queen"의 결과가 동일하게 나올 것이다

absolute한 position이 아닌 relative position을 고려해야할 경우를 알아본다

![이미지](https://ekspertos.github.io/assets/img/review/2021-10-27/2021-10-27-relative-position.PNG)

distance between token is important

token 마다 positional embedding이 한개만 존재하는 것이 아닌 다른 token 들 모두에 relationship을 고려한다

5개의 토큰이 있다면 결과가 5개가 나온다

w_0 w_1 w_2 w_3 w_4 w_5
...
w_-3 w_-2 w_-1, w_0, w_1, w_2
...
w_-5 w_-4 w_-3 w_-2 w_-1 w_0

5개의 positional embedding 결과가 나온다

clipping 이 제시될 경우 k = -2  2,-2 를 벗어날 경우 -2와 2의 결과를 준다

모든 토큰을 다 더해야 할까 어떻게 해야할까

self-attention 을 이용한다?

weighted sum over tokens


## Conv2dSubsampling
> https://github.com/espnet/espnet/blob/5c21f63e45e0961a5d817017c282b0cafd68a3aa/espnet/nets/pytorch_backend/transformer/subsampling.py#L44



## Subsampling Layer
```
[논문의 음성 feature extraction 방식]
80-channel filterbanks features computed from 25ms window with a stride of 10ms
```
10ms 단위의 음성 데이터를 40ms 단위로 subsampling한다
```python
self.conv = torch.nn.Sequential(
    torch.nn.Conv2d(1, odim, 3, 2),
    torch.nn.ReLU(),
    torch.nn.Conv2d(odim, odim, 3, 2),
    torch.nn.ReLU(),
)
self.out = torch.nn.Sequential(
    torch.nn.Linear(odim * (((idim - 1) // 2 - 1) // 2), odim),
    pos_enc if pos_enc is not None else PositionalEncoding(odim, dropout_rate),
)
```

마지막 과정에서 relative positional embedding 을 한

## Conv2dSubsampling

여기에서 conformer block 이전 과정들이랑 pos_enc까지 모두 해주는거넹?

## Conformer.EncoderLayer(nn.Module)
https://github.com/espnet/espnet/blob/5c21f63e45e0961a5d817017c282b0cafd68a3aa/espnet/nets/pytorch_backend/conformer/encoder_layer.py#L17

x, pos_emb = x_input[0], x_input[1]

positional embedding 된 결과

if positionwise_layer_type == "linear":
            positionwise_layer = PositionwiseFeedForward
            positionwise_layer_args = (
                output_size,
                linear_units,
                dropout_rate,
                activation,
            )

x = residual + stoch_layer_coeff * self.ff_scale * self.dropout(
                self.feed_forward_macaron(x)
            )

## 내일 가서 할 것

  Encoder Layers , conformer 개수
  Encoder Dim :
  Attention Heads
  Conv Kernel size
  Decoder Layers
  Decoder Dim
