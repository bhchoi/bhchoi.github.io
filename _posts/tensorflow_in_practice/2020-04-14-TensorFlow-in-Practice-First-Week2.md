---
title: "Tensorflow in Practice 첫번째 강의 Week2"
date: 2020-04-13 00:34:00 +0800
categories: [Tensorflow, Tensorflow in Practice]
tags: [Tensorflow in Practice]
toc: true
---

> Coursera Tensorflow in Practice 강의 내용 정리 노트 입니다.

Introduction to TensorFlow for Artificial Intelligence, Machine Learning, and Deep Learning 2주차 내용 

## Fashion MNIST dataset을 이용한 학습
Fashion MNIST dataset load
```python
mnist = tf.keras.datasets.fashion_mnist
(training_images, training_labels), (test_images, test_labels) = mnist.load_data()
```

image는 0 ~ 255 사이의 값을 가지고 있지만, 0 ~ 1사이의 값으로 바꾸어 주어야 training하기 쉽다.  
이를 nomalize라고 한다.
```python
training_images  = training_images / 255.0
test_images = test_images / 255.0
```

Sequential을 이용하여 layer 정의한다.  
image는 2차원 데이터이고, 이를 1차원으로 바꾸기 위해 Flatten()을 추가한다.  
10개의 class이기 때문에, 마지막 Dense layer의 units은 10개로 하고, activation은 softmax로 정의한다.
```python
model = tf.keras.models.Sequential([tf.keras.layers.Flatten(), 
                                    tf.keras.layers.Dense(128, activation=tf.nn.relu), 
                                    tf.keras.layers.Dense(10, activation=tf.nn.softmax)])
```

학습 시작
```python
model.compile(optimizer='adam',
                  loss='sparse_categorical_crossentropy',
                  metrics=['accuracy'])
history = model.fit(x_train, y_train, epochs=10, validation_data=(x_test, y_test))
```

model 평가
```python
model.evaluate(test_images, test_labels)
```

## early stopping을 위한 callback 작성

tf.keras.callbacks.Callback을 상속받아 class를 작성한다.  
epoch이 끝날때마다 on_epoch_end() 메소드가 호출된다.  
여기서는 accuracy가 0.6 보다 크면 종료하는 조건을 추가한다.  
accuracy는 model.compile()의 parameter인 metrics에 미리 정의되어 있어야 한다.  
myCallback을 생성하여 model.fit()의 callbacks에 넣어준다.
```python
class myCallback(tf.keras.callbacks.Callback):
  def on_epoch_end(self, epoch, logs={}):
    if(logs.get('accuracy')>0.6):
      print("\nReached 60% accuracy so cancelling training!")
      self.model.stop_training = True

callbacks = myCallback()

model.compile(optimizer=tf.optimizers.Adam(),
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

model.fit(x_train, y_train, epochs=10, callbacks=[callbacks])
```
callback의 다양한 활용방법  
[https://www.tensorflow.org/api_docs/python/tf/keras/callbacks/Callback](https://www.tensorflow.org/api_docs/python/tf/keras/callbacks/Callback){:target="_blank"}