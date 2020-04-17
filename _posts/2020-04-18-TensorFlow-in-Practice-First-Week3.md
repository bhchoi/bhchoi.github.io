---
title: "Tensorflow in Practice 첫번째 강의 Week3"
date: 2020-04-18 00:38:00 +0800
categories: [Tensorflow, Tensorflow in Practice]
tags: [Tensorflow in Practice]
toc: true
---

> Coursera Tensorflow in Practice 강의 내용 정리 노트 입니다.

Introduction to TensorFlow for Artificial Intelligence, Machine Learning, and Deep Learning 3주차 내용 

## Convolution layer를 이용한 학습
```python
model = tf.keras.models.Sequential([
  tf.keras.layers.Conv2D(64, (3,3), activation='relu', input_shape=(28, 28, 1)),
  tf.keras.layers.MaxPooling2D(2, 2),
  tf.keras.layers.Conv2D(64, (3,3), activation='relu'),
  tf.keras.layers.MaxPooling2D(2,2),
  tf.keras.layers.Flatten(),
  tf.keras.layers.Dense(128, activation='relu'),
  tf.keras.layers.Dense(10, activation='softmax')
])
```
