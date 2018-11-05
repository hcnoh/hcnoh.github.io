---
layout: post
title: "TensorFlow의 tf.data 모듈 기본 사용법 정리"
date: 2018-11-05 10:52:42
tagline: "TensorFlow에서 학습 데이터를 불러올 수 있게 해주는 모듈인 tf.data 모듈 및 TFRecord에 대한 기본적인 사용법 정리"
categories:
- TensorFlow
tags:
- tensorflow
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-11-05-tensorflow-data-module
---

이번 포스팅은 `TensorFlow` 공식 홈페이지에 나온 내용을 참고하여 작성하였다.

## tf.placeholder란?
`TensorFlow`를 처음 접하는 경우에 만나게 되는 객체 및 메서드로 `tf.placeholder` 및 `sess.run(feed_dict={})`를 들 수 있다. 이 둘의 기능은 우리가 구현한 모델에 학습용 또는 추론용 데이터를 입력(보통 `feeding`이라고 표현)시키기 위한 도구라고 볼 수 있다. `tf.placeholder`는 `feeding`할 데이터의 형식 및 모양을 정의하는 어떤 틀이라고 볼 수 있다. 이렇게 정의된 `tf.placeholder`는 이미 정의된 `tf.Session`을 실행시키는 메서드인 `sess.run`과 함께 사용되어 모델에 데이터를 `feeding`시킬 수 있다.

다음은 가장 기초적인 `tf.placeholder` 사용에 대한 예제이다.

```python
import numpy as np
import tensorflow as tf


# 그래프 생성
x = tf.placeholder(dtype=tf.float32, shape=[3])
y = x ** 2
y = y + 4

# Session 생성 및 변수 초기화
sess = tf.Session()
sess.run(tf.global_variables_initializer())

# Session 실행
print(sess.run(y, feed_dict={x: np.array([1, 2, 3])}))

>>>
[ 5.  8.  13.]
```

간단하게 예제를 설명하면 다음과 같다.

먼저 `tf.placeholder`를 이용하여 그래프를 생성한다. 그래프는 `x`에 입력되는 값에 모든 요소에 제곱을 한 후에 `4`를 더해주는 그래프가 된다.

그 다음으로 `tf.Session`을 통해 세션을 생성하고 그래프에 할당된 변수들을 초기화해준다.

마지막으로 `sess.run`을 이용하여 세션을 실행한다. 이 때 `feed_dict`를 이용하여 `x`라는 `placeholder`에 `np.array([1, 2, 3])`을 `feeding`해주게 된다.

이러한 방식으로 생성된 모델에 학습용 데이터를 `feeding`해줄 수 있는 것이다. 

하지만, `TensorFlow` 공식 홈페이지에는 무려 이런 경고 메시지가 있다.

![](/assets/img/2018-11-05-tensorflow-data-module/01.png)

즉, 이런 방식의 `feeding`은 가장 비효율적인 방식의 데이터 입력 방식이라는 것이다. 그렇다면 대규모의 학습용 데이터셋을 입력시키기 위해서는 어떤 방식을 사용해야 할까? `TensorFlow`에서는 이런 작업을 위해서 `tf.data` 모듈을 제공한다.

## tf.data
`tf.data`는 `TensorFlow`에서 제공하는 대규모 데이터 `feeding`용 모듈이다. 원래는 `contributor`들이 `contribute`한 모듈이었지만 최근 버전부터는 정식으로 제공하게 되었다. `tf.data` 모듈은 가장 기본적인 배치 사이즈 설정 및 `shuffle` 기능까지 제공하며 또한 자체 데이터 형식인 `TFRecord`를 이용한 전처리 기능까지 제공한다.

`tf.data`는 다음의 하위 모듈들을 제공하지만 여기서는 가장 핵심 모듈인 `tf.data.Dataset` 및 `tf.data.TFRecordDataset` 모듈에 관하여 설명할 것이다.

- `Dataset`
- `FixedLengthRecordDataset`
- `Iterator`
- `TFRecordDataset`
- `TextLineDataset`

## Dataset 모듈
`Dataset` 모듈은 여러 방식으로 데이터를 불러올 수 있지만 기본적으로는 `generator`로 부터 데이터를 불러오는 `from_generator`라는 메서드를 이용하게 된다. `from_generator` 메서드를 이용하기 위해서는 먼저 `generator` 함수를 정의해야 한다. 다음의 예제는 음성 데이터인 `VCTK` 데이터셋을 불러와서 반환하는 `generator` 예제이다.

```python
dataset_path = "/hd/dataset/VCTK"
dataset_list = get_dataset_list(dataset_path)

def _generate_batch():
    while True:
        random_dataset_list = dataset_list[:]
        random.shuffle(random_dataset_list)
        for dataset in random_dataset_list:
            audio_file_path = dataset["audio_file_path"]
            script_file_path = dataset["script_file_path"]
            
            audio = audio_process.get_audio(audio_file_path)
            script = get_script(script_file_path)
            script = script_pad(script)
            script = list(script)
            
            yield audio, script
```

`get_dataset_list` 함수는 `dataset_path`를 파라미터로 받아서 딕셔너리의 리스트 형식으로 된 `dataset_list`를 반환하게 된다. `dataset_list`는 각 요소가 `audio_file_path`, `script_file_path`를 키 값으로 가진 딕셔너리이다.

참고로 `from_generator` 메서드의 경우 `generator`가 모든 값을 반환하면 종료가 되게 된다. 따라서 `generator`가 계속 반환을 시켜주기 위해서는 `generator` 함수 내부에 `while True:` 부분을 추가해주는 것이 좋다. 또한 자체적인 `shuffle` 기능이 있더라도 리스트에서 `yield` 반환 시에 리스트를 `shuffle` 하여 넘기는 것이 기능적으로 좋다.

이제 `Dataset` 클래스를 사용하여 데이터를 불러와보자. 다음은 위에서 정의된 `_generate_batch` 함수를 이용하여 `Dataset` 클래스를 생성하는 예제이다.

```python
dataset = tf.data.Dataset.from_generator(generator=_generate_batch,
                                         output_types=(tf.float32, tf.string),
                                         output_shapes=(tf.TensorShape([125000]),
                                                        tf.TensorShape([130])))
generated_audio, generated_script = \
    dataset.\
    batch(4).\
    shuffle(10).\
    repeat(10).\
    make_one_shot_iterator().\
    get_next()

sess = tf.Session()
sess.run(tf.global_variables_initializer())
```



