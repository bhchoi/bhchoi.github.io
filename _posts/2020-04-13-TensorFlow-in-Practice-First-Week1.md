---
title: "Tensorflow in Practice 첫번째 강의 Week1"
date: 2020-04-13 00:34:00 +0800
categories: [Tensorflow, Tensorflow in Practice]
tags: [Tensorflow in Practice]
toc: true
---

> Coursera Tensorflow in Practice 강의 내용 정리 노트 입니다.

Tensorflow 2.0 keras를 이용하여 간단한 Neural Network를 작성해보자.

## Import
```python
import tensorflow as tf
print(tf.__version__)
```

## Data
```python
xs = np.array([-1.0,  0.0, 1.0, 2.0, 3.0, 4.0], dtype=float)
ys = np.array([-3.0, -1.0, 1.0, 3.0, 5.0, 7.0], dtype=float)
```

## Model Define
```python
model = tf.keras.Sequential([keras.layers.Dense(units=1, input_shape=[1])])
```

## Model Compile
```python
model.compile(optimizer='sgd', loss='mean_squared_error')
```

## Train
```python
model.fit(xs, ys, epochs=500)
```

## Predict
```python
print(model.predict([10.0]))
```