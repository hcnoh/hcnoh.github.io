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
- linux
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
dataset_path = "/hd/dataset/VCTK/VCTK-p280-short"
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
```

`_generate_batch`가 `audio` 및 `script`를 반환할 때 각각 최대 길이 125000, 130으로 패딩을 하였기 때문에 `from_generator` 메서드의 `output_shapes`을 위와 같이 잡아주었다.

또한 `.batch`, `.shuffle`, `.repeat` 등을 이용하여 배치 사이즈 및 셔플링 등의 `config` 설정들을 해주었다. 위의 예제에서 설정된 `config`는 다음과 같다.

- 배치 사이즈: 4
- 셔플을 위한 버퍼 사이즈: 10
- 최대 10번 반복하여 반환

이제 `generated_audio`, `generated_script`을 세션을 이용하여 실행시키면 실제 데이터셋의 데이터를 반환하게 된다.

```python
sess = tf.Session()
sess.run(tf.global_variables_initializer())
audio_target, script_target = sess.run([generated_audio, generated_script])
print(np.shape(audio_target))
print(np.shape(script_target))

>>>
(4, 125000)
(4, 130)
```

배치 사이즈를 4로 설정해 주었기 때문에 위와 같은 결과가 나오는 것을 확인할 수 있다. 이제 이러한 방식으로 `tf.placeholder`를 `Dataset`으로 대체할 수 있게 되었다. 실제 모델의 추론에 활용하기 위해서는 다음과 같이 사용하면 된다.

```python
model = NeuralNetworkModel(input=generated_audio)
loss = get_loss(pred=model, target=generated_script)

print(sess.run([model, loss]))
```

## TFRecord
`TFRecord`는 `TensorFlow`에서 지원하는 파일 형식이다. 공식 홈페이지에는 `TFRecord`에 대해서 `The TFRecord file format is a simple record-oriented binary format that many TensorFlow applications use for training data.`라고 표현하고 있다. 간단히 말해서 바이너리 형식으로 저장하기 위한 용도의 파일 형식이라는 의미이다. 즉, `TFRecord`는 데이터를 자체적인 바이너리 형식으로 저장하는 기능을 제공한다.

다음은 `VCTK` 데이터셋을 `TFRecord`로 변환하여 저장해주는 함수를 구현한 예제이다. 데이터셋의 형식은 위의 `from_generator` 예제의 형식과 동일하게 구현하였다.

```python
def create_tfrecord(dataset_list):
    print("Start converting...")
    options = tf.python_io.\
        TFRecordOptions(compression_type=tf.python_io.TFRecordCompressionType.GZIP)
    writer = tf.python_op.TFRecordWriter(path="tfrecord/tfrecord_practice.tfrecords",
                                         options=options)
    for dataset in dataset_list:
        audio_file_path = dataset["audio_file_path"]
        script_file_path = dataset["script_file_path"]
        
        # 데이터셋을 미리 전처리하여 TFRecord로 저장
        audio = audio_process.get_audio(audio_file_path)
        
        script = get_script(script_file_path)
        script = script_pad(script)
        script = np.asarray(list(script)) # tostring 메서드를 사용하기 위해서 numpy array로 변환해주는 작업이 추가
        
        example = tf.train.Example(
            features=tf.train.Features(
                feature={"audio": _bytes_feature(audio.tostring()),
                         "script": _bytes_feature(script.tostring())
                         }))
        writer.write(exampleSerializeToString())
    
    writer.close()
    print("Done...")
```

위의 작업의 핵심은 데이터셋을 `string`으로 변환하여 `byte` 형식의 피쳐로 바꿔주는 작업이다. 위의 작업을 수행하는 `_bytes_feature` 함수는 `TensorFlow` 공식 홈페이지에서 제공하는 다음의 코드를 사용하면 된다.

```python
def _bytes_feature(value):
    return tf.train.Feature(bytes_list=tf.train.BytesList(value=[value]))
```

마찬가지로 `float` 및 `int` 형식도 `byte` 형식의 피쳐로 변환할 수 있지만 `np.ndarray.tostring` 메서드를 이용하여 `string`으로 변환한 후에 위에서 정의된 `_bytes_feature` 함수를 사용하는 것이 간편할 것이다. (이 부분은 아직 확실히는 모르겠다. 더 공부해보고 정리하도록 하겠다.)

공식 홈페이지에서는 `tf.data`를 이용하여 메모리에 전부 들어가지 않는 데이터셋도 알맞게 처리할 수 있다고 한다. 특히 `TFRecord`는 기존의 데이터셋을 바이너리 형식으로 처리해서 저장해주며 결과적으로 용량도 크게 줄일 수 있다. 기존의 데이터셋의 용량을 확인하기 위해 커맨드라인 상에서 다음의 명령을 수행해본다.

```bash
>>> cd /hd/dataset/VCTK
>>> du -hs VCTK-p280-short
150M    VCTK-p280-short
```

위의 결과에서 확인할 수 있듯이 기존 데이터셋의 용량은 150MB이다. 그러면 새로 생성된 `TFRecord` 파일의 용량은 몇일까? `TFRecord` 파일이 저장된 위치에 가서 확인해본다.

```bash
>>> cd ~/hcnoh/practice/tfrecord
>>> du -hs tfrecord_practice.tfrecords
92M     tfrecord_practice.tfrecords
```

용량이 150MB에서 92MB로 대폭 줄어든 것을 확인할 수 있다.

`TFRecord`를 사용하였을 때의 또 다른 장점으로는 전처리를 미리 수행할 수 있다는 점이다. 기존의 `from_generator` 메서드를 이용한 방식은 `_generate_batch` 제너레이터 내부에서 데이터를 반환할 때마다 매 번 전처리를 수행해야 한다는 단점이 있다. (물론 다른 방법을 이용하여 전처리를 우회할 수는 있을 것 같다.) 그렇게되면 속도적인 측면에서도 매우 성능이 저하될 것이다. 하지만 기존 데이터셋을 모두 전처리하여 미리 `TFRecord` 형식으로 저장해놓고 필요할 때마다 불러서 쓸 수 있다면 속도적으로 매우 향상된 성능을 기대할 수 있을 것이다.

## TFRecordDataset 모듈
위에서 정의한 `create_tfrecord` 함수를 이용하여 생성된 `TFRecord` 파일을 불러와서 모델 학습 및 추론에 사용하기 위해서는 방금 설명했던 `tf.data.Dataset` 모듈과 유사한 기능을 가지는 `tf.data.TFRecordDataset` 모듈을 이용하면 된다.

다음은 위에서 정의된 방식으로 저장된 `TFRecord` 파일을 불러와서 다시 기존 형식에 맞춰서 변환하는 작업을 수행하는 예제이다. 사용법은 기존 `Dataset` 모듈과 거의 일치한다.

```python
dataset = tf.data.TFRecordDataset(finenames="tfrecord/tfrecord_practice.tfrecords",
                                  compression_type="GZIP").map(from_tfrecord)
generated_audio, generated_script = \
    dataset.\
    batch(4).\
    shuffle(10).\
    repeat(10).\
    make_one_shot_iterator().\
    get_next()
```

마찬가지로 `generated_audio`, `generated_script`를 세션을 이용하여 실행시키면 실제 데이터셋의 데이터를 반환한다.

```python
sess = tf.Session()
sess.run(tf.global_variables_initializer())
audio_target, script_target = sess.run([generated_audio, generated_script])
print(np.shape(audio_target))
print(np.shape(script_target))

>>>
(4, 125000)
(4, 130)
```
