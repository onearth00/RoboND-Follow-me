## Deep Learning Project <!-- omit in toc -->
In this project, I trained a deep neural network to identify and track a target in simulation. THe so-called “follow me” applications like this are key to many fields of robotics and the very same techniques may be extended to scenarios like advanced cruise control in autonomous vehicles or human-robot collaboration in industry.

The model used in the semantic segmentation task is a 7-layer fully convolutional network (FCN), built in Tensorflow and Keras. The model training was accelerated with a p2.xlarge AWS EC2 instance.

I will walk through architecture of the constructed model, followed by training, results and thoughts on further improvement.

## Neural network architecture <!-- omit in toc -->
A fully convolutional network (FCN) is an DNN model for end-to-end, pixels-to-pixels semantic segmentation. 

- encoder
    ```python
    l1 = encoder_block(inputs, 32, 2)
    l2 = encoder_block(l1,64,2)
    # Remember that with each encoder layer, the depth of your model (the number of filters) increases.

  
    # TODO: Add the same number of Decoder Blocks as the number of Encoder Blocks
    l4 = decoder_block(l3,l1,64)
    x = decoder_block(l4,inputs,32)
    ```
- 1 by 1 convolution layer
    ```python
     
    l3 = conv2d_batchnorm(l2, 128, kernel_size=1, strides=1) # TODO Add 1x1 Convolution layer using conv2d_batchnorm().
    ```
- decoder
    ```python
    l4 = decoder_block(l3,l1,64)
    x = decoder_block(l4,inputs,32)
    ```

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

## Thoughts on further improvement <!-- omit in toc -->
