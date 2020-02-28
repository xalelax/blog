---
layout: post
title:  "Transfer learning made easy: let's build a dog breed classifier!"
labels: [deep learning, machine learning]
categories: [deep learning, machine learning]
image: assets/images/encdec.png
---

# Introduction

In this post, I want to showcase how simple it has become to perform transfer learning
with the help of modern libraries. I will use TensorFlow, but the situation looks pretty
similar with other frameworks. In particular, I will load a Dataset from the TensorFlow
Datasets library and download a pretrained model for object classification. 
We will then attach a custom layer on top of pretrained
model, and train it for the 


Transfer learning is based on the idea that the feature a network learns for a problem
can be reused for a variety of other tasks; %aggiungere refs
this should sound a very natural concept: as humans, when learning how to perform
a new task we never start from scratch, but we carry over all that we have learnt in
our lifetime (sometimes this allows us to quickly learn new stuff,
often from a single training example, but
other times it actually hinders our development... but let's not digress!).

As an example for how this can be done, let's consider image classification problems!

The first few layers of a convolutional neural network learn to perform pretty simple
tasks, such as recognizing edges at different angles, corners, etc... But the deeper we 
go towards the network output, the more abstract learned features become (from patterns 
and shapes all the way to human faces). This intuitively suggests that if we already have a 
network which can recognize, say, furniture (chairs, desks, and so on) from pictures, 
in order to solve a different problem in computer vision, such as recognizing the model
of a car from a picture, for sure we will need to learn in the final layer 
the quite "abstract" features which correspond to a Ferrari Testarossa, but on the other 
hand we don't need to teach the network "how to see" from scratch. 
The features built in the first few layers of the furniture detection model can be 
"recycled" for car model detection; we just need to cut away a few of the final layers 
where the network learned features which are useful for furniture detection but not for 
car detection.

Transfer learning has many advantages, among which:
  1. Since we are carrying over information from another problem, in principle we do not
     need tons of data for training a network. This is especially useful in the (very frequent)
	 situation where generating and annotating a large data set has a prohibitive cost. 
  2. We do not need to train a whole new network! In fact, very often we freeze (i.e., 
     set as nontrainable) the weights in the initial layers of the network, rendering the 
	 remaining free parameters a small minority with respect to the total number of parameters
	 of the network. As a result, training time is much smaller!
  3. Buh	 

# Dog breed classifier

In order to show how to perform transfer learning, let's train a neural network which recognizes
the breed of a dog from a single picture. For training, we will use the St...
dataset, which contains around 20,000 pictures of dogs together with labels of their breed.
Although 20,000 images might sound like a lot, in the world of computer 
vision this is a quite small dataset: the data used for training state-of-the-art networks
for object classification, [Imagenet](http://www.image-net.org/)
contains more than 14 million images!

Let's start by downloading Stanford's Dog Dataset from the TensorFlow Datasets
library:
```python
import tensorflow_datasets as tfds

dataset, info = tfds.load(name="stanford_dogs", with_info=True)
```

We can visualize a few traininig examples with the following:
```python
# function to convert label indices to breed names 
get_name = info.features['label'].int2str

for doggo in dataset['train'].take(10):
    plt.figure()
    plt.imshow(doggo['image'])
    plt.title(get_name(doggo['label']))
```

% inserire pics

...

Notice how pictures in this dataset have different resolutions, and often contain 
a lot of other stuff aside from a dog. The best thing to do would be to apply 
the dog breed classifier network only to a the subset of each image where the dog is,
but for the sake of simplicity we will just resize every image to the same size and
feed them in their entirety to our network

```python
IMG_LEN = 224
IMG_SHAPE = (IMG_LEN,IMG_LEN,3)
N_BREEDS = 120

training_data = dataset['train']
test_data = dataset['test']

def preprocess(ds_row):
  
    # Image conversion int->float + resizing
    image = tf.image.convert_image_dtype(ds_row['image'], dtype=tf.float32)
    image = tf.image.resize(image, (IMG_LEN, IMG_LEN), method='nearest')
  
    # Onehot encoding labels
    label = tf.one_hot(ds_row['label'],N_BREEDS)

    return image, label

def prepare(dataset, batch_size=None):
    ds = dataset.map(preprocess, num_parallel_calls=4)
    ds = ds.shuffle(buffer_size=1000)
    if batch_size:
      ds = ds.batch(batch_size)
    ds = ds.prefetch(buffer_size=tf.data.experimental.AUTOTUNE)
    return ds
```


Now we can define a model; as I am experimenting lately with TensorFlow Lite, and plan to deploy
this on my smartphone for tesing purposes, I decided to start with a pretrained MobileNetV2 % link
network, as it is very performant on mobile devices. This network will serve as a base, and we can 
download it in the following way:

```python
base_model = tf.keras.applications.MobileNetV2(input_shape=IMG_SHAPE,
                                               include_top=False,
                                               weights='imagenet')
```

By specifying `include_top=False`
we cut away from MobileNetV2 the very final layer, a dense layer of 1000 %check
neurons which give the output predictions for the imagenet classes (...) % dare esempi

On top of this model, we will add just two layers: a Global...2d % finire
to transform the .... tensor which is the output from the final layer of MobileNetV2
to a vector, and then a single dense layer with as many neurons as the output classes 
we want to predict (i.e., the number of dog breeds).
```python
base_model.trainable = False

model = tf.keras.Sequential([
  base_model,
  tf.keras.layers.GlobalAveragePooling2D(),
  tf.keras.layers.Dense(N_BREEDS, activation='softmax')
])
```






# Conclusions

In this tutorial, I hope I was able to give a clear small introduction to transfer learning,
together 
