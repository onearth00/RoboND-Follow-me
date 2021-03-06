## Deep Learning Project <!-- omit in toc -->
In this project, I trained a deep neural network to identify and track a target in simulation. The so-called “follow me” applications like this are key to many fields of robotics and the very same techniques may be extended to scenarios like advanced cruise control in autonomous vehicles or human-robot collaboration in industry.

The model used in the semantic segmentation task is a 7-layer fully convolutional network (FCN), built in Tensorflow and Keras. The model training was accelerated with a p2.xlarge AWS EC2 instance.

I will walk through architecture of the constructed model, followed by training, results and thoughts on further improvement.

## Neural network architecture <!-- omit in toc -->
A fully convolutional network (FCN) is an DNN model for end-to-end, pixels-to-pixels semantic segmentation. Unlikely Convolutional Neural Network (CNN), FCN is composed of convolutional layers without fully-connected layers found at the end of the network. By changing the "connected" to "convolutional", one perserve the spatial information through the entire network. A typical FCN is consisted of encoder, 1 by 1 convolution layer, decoder, and some skip connections. 

#### Encoder.
Each encoder is a convolutional layer for progressive understanding of the characeristics of the image. 

There are two layers of encoders in the current model. The first layer uses a filter size of 32 and a stride of 2, while the second convolution uses a filter size of 64 and a stride of 2. Both convolutions used same padding. The padding in conjunction with a stride of 2 half image size in each layer, while setting the depth to the filter size. The python code is shown below:
 
```python
l1 = encoder_block(inputs, 32, 2)
l2 = encoder_block(l1,64,2)
# Remember that with each encoder layer, the depth of your model (the number of filters) increases.
```
#### 1x1 convolution layer.
The 1x1 convolution layer is a regular convolution, with a stride of 1. This layer flattens the data for classification while retaining their spatial information. In the pythong below, we use depth of 128 for this layer:

```python
l3 = conv2d_batchnorm(l2, 128, kernel_size=1, strides=1) #Add 1x1 Convolution layer using conv2d_batchnorm().
```
#### Decoder.
The decoder of the model can either be composed of transposed convolution layers or bilinear upsampling layers. The decoder block mimics the use of skip connections by having the larger decoder block input layer act as the skip connection. It calculates the separable convolution layer of the concatenated bilinear upsample of the smaller input layer with the larger input layer.

Two decoders are used in the current model, with the the first taking 1x1 convolution as the small input layer and the encoder 1 as the large input layer, and a filter size of 64 is used for this layer; the second decoder block layer uses the output from the first decoder block as the small input layer, and the original image as the large input layer, and a filter size of 32 is used for this layer. Below shows the python code:

```python
l4 = decoder_block(l3,l1,64)
x = decoder_block(l4,inputs,32) # Add the same number of Decoder Blocks as the number of Encoder Blocks
```

Finally, the output convolution layer applies a softmax activation function to the output of the second decoder block. A full schematic of the FCN architecture can be found below:

<img src="./FCN_architecture.png" alt="Schematic of the FCN architecture" width="75%">


## Training parameters <!-- omit in toc -->

The below parameters were attempted to train the fully-connected network, yielding satisfactory results:

- learning_rate = 0.001
- batch_size = 128
- num_epochs = 40
- steps_per_epoch = 200
- validation_steps = 50
- workers = 2

A more adaptive approach may be adopted to dynamically tune learning_rate as a function of validation loss. 

Training took place on an Amazon EC2 ([p2.xlarge](https://aws.amazon.com/ec2/instance-types/p2/)) instance. Each epoch takes about 320 seconds to train, and the total used time amounted to 3.5 hours.

## Results <!-- omit in toc -->

The IoU is 0.55, while the final weighted score is 0.407.

## Thoughts on the model extensibility <!-- omit in toc -->

This model was trained on following a person, but in principle it should be readily extensible to apply to other objects of interest. The key is to provide sufficient amount of target objects for training, of multiple angles of observations and with varying distraction features. 

Real-life scenes are filled with obstacles and constraints. For example, the FCN should also be trained to recognize objects such as trees or telephone poles, and learn to pivot away from the barriers. 
