---
layout: post
title: "Build Your First Deep Learning Classifier using TensorFlow: Dog Breed Example"
date: 2018-04-26 10:00:00 +0400
categories: [data-science, deep-learning]
tags: [deep-learning, tensorflow, keras, cnn, neural-networks, machine-learning, computer-vision, transfer-learning]
original_url: "https://medium.com/data-science/build-your-first-deep-learning-classifier-using-tensorflow-dog-breed-example-964ed0689430"
canonical_url: "https://medium.com/data-science/build-your-first-deep-learning-classifier-using-tensorflow-dog-breed-example-964ed0689430"
description: "A comprehensive tutorial on building your first deep learning classifier using TensorFlow and Keras, featuring Convolutional Neural Networks, transfer learning, and practical dog breed classification with 80% accuracy."
image: "/assets/images/posts/deep-learning-classifier.jpg"
republished: true
---

> *Originally published on [Medium]({{ page.original_url }}) on {{ page.date | date: "%B %d, %Y" }}*

![Neural Network Architecture](https://unsplash.com/photos/iar-afB0QQw/download?force=true&w=800)
*Convolutional Neural Networks (like the one pictured above) are powerful tools for Image Classification*

## Introduction

In this article, I will present several techniques for you to make your first steps towards developing an algorithm that could be used for a classic image classification problem: detecting dog breed from an image.

By the end of this article, we'll have developed code that will accept any user-supplied image as input and return an estimate of the dog's breed. Also, if a human is detected, the algorithm will provide an estimate of the dog breed that is most resembling.

# 1. What are Convolutional Neural Networks?

Convolutional neural networks (also referred to as CNN or ConvNet) are a class of deep neural networks that have seen widespread adoption in a number of computer vision and visual imagery applications.

A famous case of CNN application was detailed in this [research paper](https://www.nature.com/articles/nature21056?error=cookies_not_supported&code=c8ab8524-38e7-47c0-af7a-5912873b07b6) by a Stanford research team in which they demonstrated classification of skin lesions using a single CNN. The Neural Network was trained from images using only pixels and disease labels as inputs.

Convolutional Neural Networks consist of multiple layers designed to require relatively little pre-processing compared to other image classification algorithms.

They learn by using filters and applying them to the images. The algorithm takes a small square (or 'window') and starts applying it over the image. Each filter allows the CNN to identify certain patterns in the image. The CNN looks for parts of the image where a filter matches the contents of the image.

![CNN Architecture](https://unsplash.com/photos/Q1p7bh3SHj8/download?force=true&w=800)
*An example of a CNN Layer Architecture for Image Classification*

The first few layers of the network may detect simple features like lines, circles, edges. In each layer, the network is able to combine these findings and continually learn more complex concepts as we go deeper and deeper into the layers of the Neural Network.

## 1.1 What kinds of layers are there?

The overall architecture of a CNN consists of an input layer, hidden layer(s), and an output layer. They are several types of layers, for e.g. Convolutional, Activation, Pooling, Dropout, Dense, and SoftMax layer.

![Neural Network Layers](https://unsplash.com/photos/FO7JIlwjOtU/download?force=true&w=800)
*Neural Networks consist of an input layer, hidden layers, and an output layer*

The Convolutional Layer (or Conv layer) is at the core of what makes a Convolutional Neural Network. The Conv layer consists of a set of filters. Every filter can be considered as a small square (with a fixed width and height) which extends through the full depth of the input volume.

During each pass, the filter 'convolves' across the width and height of the input volume. This process results in a 2-dimensional activation map that gives the responses of that filter at every spatial position.

To avoid over-fitting, Pooling layers are used to apply non-linear downsampling on activation maps. In other words, Pooling Layers are aggressive at discarding information but can be useful if used appropriately. A Pooling layer would often follow one or two Conv Layers in CNN architecture.

Dropout Layers are also used to reduce over-fitting, by randomly ignore certain activations functions, while Dense Layers are fully connected layers and often come at the end of the Neural Network.

## 1.2 What are Activation Functions?

The output of the layers and of the neural network are processed using an activation function, which is a node that is added to the hidden layers and to the output layer.

You'll often find that the ReLu activation function is used in hidden layers, while the final layer typically consists of a SoftMax activation function. The idea is that by stacking layers of linear and non-linear functions, we can detect a large range of patterns and accurately predict a label for a given image.

SoftMax is often found in the final layer which acts as basically a normalizer and produces a discrete probability distribution vector, which is great for us as the CNN's output we want is a probability that an image corresponds to a particular class.

When it comes to model evaluation and performance assessment, a loss function is chosen. In CNNs for image classification, the [categorical cross-entropy](https://aboveintelligent.com/deep-learning-basics-the-score-function-cross-entropy-d6cc20c9f972) is often chosen (in a nutshell: it corresponds to -log(error)). There are several methods to minimise the error using Gradient Descent — in this article, we'll rely on "[rmsprop](http://ruder.io/optimizing-gradient-descent/)", which adaptive learning rate method, as an optimizer with accuracy as a metric.

# 2. Setting up the algorithm's building blocks

To build our algorithm, we'll be using [TensorFlow](https://www.tensorflow.org/), [Keras](https://keras.io/) (neural networks API running on top of TensorFlow), and [OpenCV](https://opencv.org/) (computer vision library).

Training and testing datasets were also available on-hand when completing this project (see [GitHub repo](https://github.com/udacity/dog-project)).

## 2.1 Detecting if Image Contains a Human Face

To detect whether the image supplied is a human face, we'll use one of OpenCV's [Face Detection algorithm](https://docs.opencv.org/trunk/d7/d8b/tutorial_py_face_detection.html). Before using any of the face detectors, it is standard procedure to convert the images to grayscale. Below, the `detectMultiScale` function executes the classifier stored in `face_cascade` and takes the grayscale image as a parameter.

```python
import cv2

# Load the cascade
face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_alt.xml')

def face_detector(img_path):
    img = cv2.imread(img_path)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray)
    return len(faces) > 0
```

## 2.2 Detecting if Image Contains a Dog

To detect whether the image supplied contains a face of a dog, we'll use a pre-trained [ResNet-50](http://ethereon.github.io/netscope/#/gist/db945b393d40bfa26006) model using the [ImageNet](https://en.wikipedia.org/wiki/ImageNet) dataset which can classify an object from one of [1000 categories](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Given an image, this pre-trained [ResNet-50 model](http://ethereon.github.io/netscope/#/gist/db945b393d40bfa26006) returns a prediction for the object that is contained in the image.

When using [TensorFlow](https://www.tensorflow.org/) as backend, [Keras](https://keras.io/) CNNs require a 4D array as input. The `path_to_tensor` function below takes a string-valued file path to a color image as input, resizes it to a square image that is 224x224 pixels, and returns a 4D array (referred to as a 'tensor') suitable for supplying to a Keras CNN.

```python
from keras.preprocessing import image
from keras.applications.resnet50 import ResNet50, preprocess_input
import numpy as np

def path_to_tensor(img_path):
    # loads RGB image as PIL.Image.Image type
    img = image.load_img(img_path, target_size=(224, 224))
    # convert PIL.Image.Image type to 3D tensor with shape (224, 224, 3)
    x = image.img_to_array(img)
    # convert 3D tensor to 4D tensor with shape (1, 224, 224, 3) and return 4D tensor
    return np.expand_dims(x, axis=0)

def ResNet50_predict_labels(img_path):
    # returns prediction vector for image located at img_path
    img = preprocess_input(path_to_tensor(img_path))
    return np.argmax(ResNet50_model.predict(img))
```

Also, all pre-trained models have the additional normalization step that the mean pixel must be subtracted from every pixel in each image. This is implemented in imported function `preprocess_input`.

As shown in the code above, for the final prediction we obtain an integer corresponding to the model's predicted object class by taking the [argmax](https://docs.scipy.org/doc/numpy-1.14.0/reference/generated/numpy.argmax.html) of the predicted probability vector, which we can identify with an object category through the use of the ImageNet labels [dictionary](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a).

# 3. Build your CNN Classifier using Transfer Learning

Now that we have functions for detecting humans and dogs in images, we need a way to predict breed from images. In this section, we will create a CNN that classifies dog breeds.

To reduce training time without sacrificing accuracy, we'll be training a CNN using [Transfer Learning](https://towardsdatascience.com/transfer-learning-leveraging-insights-from-large-data-sets-d5435071ec5a) — which is a method that allows us to use Networks that have been pre-trained on a large dataset. By keeping the early layers and only training newly added layers, we are able to tap into the knowledge gained by the pre-trained algorithm and use it for our application.

[Keras](https://keras.io/applications/) includes several pre-trained deep learning models that can be used for prediction, feature extraction, and fine-tuning.

## 3.1 Model Architecture

As previously mentioned, the [ResNet-50 model](https://github.com/KaimingHe/deep-residual-networks) output is going to be our input layer — called the bottleneck features. In the code block below, we extract the bottleneck features corresponding to the train, test, and validation sets by running the following.

```python
from keras.applications.resnet50 import ResNet50

# define ResNet50 model
ResNet50_model = ResNet50(weights='imagenet', include_top=False)

def extract_Resnet50(tensor):
    return ResNet50_model.predict(preprocess_input(tensor))
```

We'll set up our model architecture such that the last convolutional output of ResNet-50 is fed as input to our model. We only add a [Global Average Pooling](https://keras.io/layers/pooling/) layer and a [Fully Connected](https://keras.io/layers/core/) layer, where the latter contains one node for each dog category and has a [Softmax](https://keras.io/activations/#softmax) activation function.

```python
from keras.layers import GlobalAveragePooling2D, Dense
from keras.models import Sequential

Resnet50_model = Sequential()
Resnet50_model.add(GlobalAveragePooling2D(input_shape=(7, 7, 2048)))
Resnet50_model.add(Dense(133, activation='softmax'))

Resnet50_model.summary()
```

As we can see in the above code's output, we end up with a Neural Network with 272,517 parameters!

## 3.2 Compile & Test the Model

Now, we can use the CNN to test how well it identifies breed within our test dataset of dog images. To fine-tune the model, we go through 20 iterations (or '[epochs](https://towardsdatascience.com/epoch-vs-iterations-vs-batch-size-4dfb9c7ce9c9)') in which the model's hyper-parameters are fine-tuned to reduce the loss function ([categorical cross-entropy](https://rdipietro.github.io/friendly-intro-to-cross-entropy-loss/)) which is optimised using RMS Prop.

```python
Resnet50_model.compile(loss='categorical_crossentropy', optimizer='rmsprop', metrics=['accuracy'])

Resnet50_model.fit(train_Resnet50, train_targets, 
          validation_data=(valid_Resnet50, valid_targets),
          epochs=20, batch_size=20, verbose=1)

# get index of predicted dog breed for each image in test set
Resnet50_predictions = [np.argmax(Resnet50_model.predict(np.expand_dims(tensor, axis=0))) for tensor in test_Resnet50]

# report test accuracy
test_accuracy = 100*np.sum(np.array(Resnet50_predictions)==np.argmax(test_targets, axis=1))/len(Resnet50_predictions)
print('Test accuracy: %.4f%%' % test_accuracy)
```

**Test accuracy: 80.0239%**

Provided with a testing set, the algorithm scored a testing accuracy of 80%. Not bad at all!

## 3.3 Predict Dog Breed with the Model

Now that we have the algorithm, let's write a function that takes an image path as input and returns the dog breed that is predicted by our model.

```python
def Resnet50_predict_breed(img_path):
    # extract bottleneck features
    bottleneck_feature = extract_Resnet50(path_to_tensor(img_path))
    # obtain predicted vector
    predicted_vector = Resnet50_model.predict(bottleneck_feature)
    # return dog breed that is predicted by the model
    return dog_names[np.argmax(predicted_vector)]
```

# 4. Testing our CNN Classifier

Now, we can write a function that takes accepts a file path to an image and first determines whether the image contains a human, dog, or neither.

If a dog is detected in the image, return the predicted breed. If a human is detected in the image, return the resembling dog breed. If neither is detected in the image, provide output that indicates an error.

```python
def run_app(img_path):
    ## handle cases for a human face, dog, and neither
    if dog_detector(img_path):
        return Resnet50_predict_breed(img_path)
    elif face_detector(img_path):
        return Resnet50_predict_breed(img_path)
    else:
        return "Error: Neither human nor dog detected in image."
```

We are ready to take the algorithm for a spin! Let's test the algorithm on a few sample images:

![Dog classification results](https://unsplash.com/photos/av3cmVU_bmM/download?force=true&w=800)
*Testing our deep learning classifier on real images*

These predictions look accurate to me!

On a final note, I noted that that the algorithm is prone to errors unless it's a clear facing shot with minimal noise on the image. Hence, we need to make the algorithm more robust to noise. Also, a method we can use to improve our classifier is [image augmentation](https://medium.com/nanonets/how-to-use-deep-learning-when-you-have-limited-data-part-2-data-augmentation-c26971dc8ced) which allows you to "augment" your data by providing variations of the images supplied in the training set.

---

*If you found this article helpful, feel free to connect with me on [LinkedIn](https://linkedin.com/in/hamzabendemra){:target="_blank" rel="noopener"} or check out my other articles on [Medium](https://medium.com/@hamzabendemra){:target="_blank" rel="noopener"}.*
