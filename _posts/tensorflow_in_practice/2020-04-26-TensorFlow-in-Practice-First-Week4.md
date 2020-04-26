---
title: "Tensorflow in Practice 첫번째 강의 Week4"
date: 2020-04-26 00:38:00 +0800
categories: [Tensorflow, Tensorflow in Practice]
tags: [Tensorflow in Practice]
toc: true
---

> Coursera Tensorflow in Practice 강의 내용 정리 노트 입니다.

Introduction to TensorFlow for Artificial Intelligence, Machine Learning, and Deep Learning 4주차 내용 

## Imagedata Generator
image data path를 지정하면 자동으로 rescale, size 등을 조정하여 load한다.  
tutorial : [https://www.tensorflow.org/tutorials/images/classification](https://www.tensorflow.org/tutorials/images/classification){:target="_blank"}  
api : [https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing/image/ImageDataGenerator](https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing/image/ImageDataGenerator){:target="_blank"}
```python
train_datagen = ImageDataGenerator(rescale=1/255)

train_generator = train_datagen.flow_from_directory(
    '/image_dir_path/',
    target_size=(100,100),
    batch_size=32,
    class_mode='binary'
)

history = model.fit_generator(
    train_generator,
    steps_per_epoch=32,
    epochs=20,
    verbose=1,
    callbacks=[callbacks]
)
```