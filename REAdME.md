## Deep Learning Project <!-- omit in toc -->
This project involved the building of a fully convolutional network (FCN) to which was trained to identify a target individual from a simulated drone camera feed.

The model is built within Tensorflow and Keras, and was trained using a p2.xlarge AWS EC2 instance.

The original Udacity project repo is here.

## Neural network architecture <!-- omit in toc -->

a picture on the layers

## Training parameters <!-- omit in toc -->

The below parameters were attempted to train the fully-connected network, yielding satisfactory results:

- learning_rate = 0.001
- batch_size = 128
- num_epochs = 40

- steps_per_epoch = 200
- validation_steps = 50
- workers = 2

A more adaptive approach may be adopted to dynamically tune learning_rate as a function of validation loss. 

Training took place on an Amazon EC2 (p2.xlarge) instance. Each epoch takes about 320 seconds to train, and the total spared time amounted to 3 1/2 hours.

## Results <!-- omit in toc -->

The IoU is 0.55, while the final weighted score is 0.407.

## Thoughts on further improvement <!-- omit in toc -->
