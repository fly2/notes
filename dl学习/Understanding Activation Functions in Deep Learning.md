# Understanding Activation Functions in Deep Learning

OCTOBER 30, 2017 BY [ADITYA SHARMA](https://www.learnopencv.com/author/adityasharma/)

In this post, we will learn about different kinds of activation functions; we will also see which activation function is better than the other. This post assumes that you have a basic idea of Artificial Neural Networks (ANN), but in case you don’t, I recommend you first read the post on [understanding feedforward neural networks](https://www.learnopencv.com/understanding-feedforward-neural-networks/).

## 1. What is an Activation Function?

Biological neural networks inspired the development of artificial neural networks. However, ANNs are not even an approximate representation of how the brain works. It is still useful to understand the relevance of an activation function in a biological neural network before we know as to why we use it in an artificial neural network.

A typical neuron has a physical structure that consists of a cell body,  an axon that sends messages to other neurons, and dendrites that receives signals or information from other neurons.

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/biological-neural-network-300x246.jpg)](https://www.learnopencv.com/wp-content/uploads/2017/10/biological-neural-network.jpg)Biological Neural Network ([Image Credit](https://www.tutorialspoint.com/artificial_intelligence/artificial_intelligence_neural_networks.htm))

In the above picture, the red circle indicates the region where the two neurons communicate. The neuron receives signals from other neurons through the dendrites. The weight (strength) associated with a dendrite, called synaptic weights, gets multiplied by the incoming signal. The signals from the dendrites are accumulated in the cell body, and if the strength of the resulting signal is above a certain threshold, the neuron passes the message to the axon. Otherwise, the signal is killed by the neuron and is not propagated further.

The activation function takes the decision of whether or not to pass the signal. In this case, it is a simple step function with a single parameter – the threshold. Now, when we learn something new ( or unlearn something ), the threshold and the synaptic weights of some neurons change. This creates new connections among neurons making the brain learn new things.

Let us understand the same concept again but this time using an artificial neuron.

[![neuron](https://www.learnopencv.com/wp-content/uploads/2017/10/neuron-diagram.jpg)](https://www.learnopencv.com/wp-content/uploads/2017/10/neuron-diagram.jpg)

In the above figure, ![(x_1, \cdots , x_n)](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-dc0da7342ba8dc2afc5f369896f36431_l3.png) is the signal vector that gets multiplied with the weights ![\left( w_1, w_2, \cdots, w_n \right)](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-0e4fbd3474985a8ad61a56ccf99e10e0_l3.png). This is followed by accumulation ( i.e. summation + addition of bias ![b](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-173b5c22c5ff048f406b49d642c938f4_l3.png) ). Finally, an activation function ![f](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-c7d97b919a3b73617cf2fbb375fff3b1_l3.png) is applied to this sum.

Note that the weights ![\left( w_1, w_2, \cdots, w_n \right)](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-0e4fbd3474985a8ad61a56ccf99e10e0_l3.png) and the bias ![b](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-173b5c22c5ff048f406b49d642c938f4_l3.png) transform the input signal linearly. The activation, on the other hand, transforms the signal non-linearly and it is this non-linearity that allows us to learn arbitrarily complex transformations between the input and the output.

Over the years, various functions have been used, and it is still an active area of research to find a proper activation function that makes the neural network learn better and faster.

## 2. How does the network learn?

It is essential to get a basic idea of how the neural network learns. Let’s say that the desired output of the network is ![y](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-0e13c52c4764efa3f5b1e98e3c2cf98a_l3.png). The network produces an output ![y'](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-2261b069fc67b9659efe5b649051c7fc_l3.png). The difference between the predicted output and the desired output ![(y - y)](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-da3a2d7dd8eeed8f39ca81a68f1ac142_l3.png)is converted to a metric known as the loss function ( ![J](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-bb8905e363b035a97b58399e113c48e8_l3.png) ). The loss is high when the neural network makes a lot of mistakes, and it is low when it makes fewer mistakes. The goal of the training process is to find the weights and bias that minimise the loss function over the training set.

In the figure below, the loss function is shaped like a bowl. At any point in the training process, the partial derivatives of the loss function w.r.t to the weights is nothing but the slope of the bowl at that location. One can see that by moving in the direction predicted by the partial derivatives, we can reach the bottom of the bowl and therefore minimize the loss function. This idea of using the partial derivatives of a function to iteratively find its local minimum is called the **gradient descent**.

[![diagram explaining gradient descent](https://www.learnopencv.com/wp-content/uploads/2017/10/gradient-descent-2d-diagram.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/gradient-descent-2d-diagram.png)

In Artificial neural networks the weights are updated using a method called **Backpropagation**. The partial derivatives of the loss function w.r.t the weights are used to update the weights. In a sense, the error is backpropagated in the network using derivatives. This is done in an iterative manner and after many iterations, the loss reaches a minimum value, and the derivative of the loss becomes zero.

We plan to cover backpropagation in a separate blog post. The main thing to note here is the presence of derivatives in the training process.

## 3. Types of Activation Functions

- Linear Activation Function: It is a simple linear function of the form ![f(x) = x](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-5837b5b8d8f00eb7c80d172f3e04db9f_l3.png)Basically, the input passes to the output without any modification.

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/linear-activation-function-1.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/linear-activation-function-1.png)Figure : Linear Activation Function

- Non-Linear Activation Functions: These functions are used to separate the data that is not linearly separable and are the most used activation functions.A non-linear equation governs the mapping from inputs to outputs. Few examples of different types of non-linear activation functions are sigmoid, tanh, relu, lrelu, prelu, swish, etc. We will be discussing all these activation functions in detail.

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/non-linear-activation-function-300x300.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/non-linear-activation-function.png)Figure: Non-Linear Activation Function

## 4. Why do we need a non-linear activation function in an artificial neural network?

Neural networks are used to implement complex functions, and non-linear activation functions enable them to approximate arbitrarily complex functions. Without the non-linearity introduced by the activation function, multiple layers of a neural network are equivalent to a single layer neural network.

Let’s see a simple example to understand why without non-linearity it is impossible to approximate even simple functions like XOR and XNOR gate. In the figure below, we graphically show an XOR gate. There are two classes in our dataset represented by a cross and a circle. When the two features, ![X1](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-95a6237cd078bb2c77dfc30c0ec674e9_l3.png)and ![X2](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-71a291e804fecf93499e1ad302abc279_l3.png) are the same, the class label is a red cross, otherwise, it is a blue circle. The two red crosses have an output of 0 for input value (0,0) and (1,1) and the two blue rings have an output of 1 for input value (0,1) and (1,0).

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/xor.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/xor.png)Figure: Graphical Representation of XOR gate

From the above picture, we can see that the data points are not linearly separable. In other words, we can not draw a straight line to separate the blue circles and the red crosses from each other. Hence, we will need a non-linear decision boundary to separate them.

The activation function is also crucial for squashing the output of the neural network to be within certain bounds. The output of a neuron ![\sum^n_i w_i x_i + b](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-206fd95e028a0979d718a50209c266d0_l3.png) can take on very large values. This output, when fed to the next layer neuron without modification, can be transformed to even larger numbers thus making the process computationally intractable. One of the tasks of the activation function is to map the output of a neuron to something that is bounded ( e.g., between 0 and 1).

With this background, we are ready to understand different types of activation functions.

## 5. Types of Non-Linear Activation Functions

### 5.1. Sigmoid

It is also known as Logistic Activation Function. It takes a real-valued number and squashes it into a range between 0 and 1. It is also used in the output layer where our end goal is to predict probability. It converts large negative numbers to 0 and large positive numbers to 1. Mathematically it is represented as

$\sigma(x) = \frac{1}{1 + e^{-x}}$

The figure below shows the sigmoid function and its derivative graphically

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/sigmoid-activation-function.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/sigmoid-activation-function.png)Figure: Sigmoid Activation Function

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/sigmoid-derivative.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/sigmoid-derivative.png)Figure: Sigmoid Derivative

 

The three major drawbacks of sigmoid are:

1. **Vanishing gradients**: Notice, the sigmoid function is flat near 0 and 1. In other words, the gradient of the sigmoid is 0 near 0 and 1. During backpropagation through the network with sigmoid activation, the gradients in neurons whose output is near 0 or 1 are nearly 0. These neurons are called saturated neurons. Thus, the weights in these neurons do not update. Not only that, the weights of neurons connected to such neurons are also slowly updated. This problem is also known as vanishing gradient. So, imagine if there was a large network comprising of sigmoid neurons in which many of them are in a saturated regime, then the network will not be able to backpropagate.
2. **Not zero centered**: Sigmoid outputs are not zero-centered.
3. **Computationally expensive**: The exp() function is computationally expensive compared with the other non-linear activation functions.

The next non-linear activation function that I am going to discuss addresses the zero-centered problem in sigmoid.

### 5.2. Tanh

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/tanh-1.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/tanh-1.png)Figure: Tanh Activation Function

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/tanh-derivative.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/tanh-derivative.png)Figure: Tanh Derivative

It is also known as the hyperbolic tangent activation function. Similar to sigmoid, tanh also takes a real-valued number but squashes it into a range between -1 and 1. Unlike sigmoid, tanh outputs are zero-centered since the scope is between -1 and 1. You can think of a tanh function as two sigmoids put together. In practice, tanh is preferable over sigmoid. The negative inputs considered as strongly negative, zero input values mapped near zero, and the positive inputs regarded as positive. The only drawback of tanh is:

1. The tanh function also suffers from the vanishing gradient problem and therefore kills gradients when saturated.

To address the vanishing gradient problem, let us discuss another non-linear activation function known as the rectified linear unit (ReLU) which is a lot better than the previous two activation functions and is most widely used these days.

### 5.3. Rectified Linear Unit (ReLU)

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/relu-activation-function-1.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/relu-activation-function-1.png)Figure: ReLU Activation Function

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/relu-derivative.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/relu-derivative.png)Figure: ReLU Derivative

ReLU is half-rectified from the bottom as you can see from the figure above. Mathematically, it is given by this simple expression

$f(x) = \max(0,x) $

This means that when the input x < 0 the output is 0 and if x > 0 the output is x. This activation makes the network converge much faster. It does not saturate which means it is resistant to the vanishing gradient problem at least in the positive region ( when x > 0), so the neurons do not backpropagate all zeros at least in half of their regions. ReLU is computationally very efficient because it is implemented using simple thresholding. But there are few drawbacks of ReLU neuron :

1. Not zero-centered: The outputs are not zero centered similar to the sigmoid activation function.
2. The other issue with ReLU is that if x < 0 during the forward pass, the neuron remains inactive and it kills the gradient during the backward pass. Thus weights do not get updated, and the network does not learn. When x = 0 the slope is undefined at that point, but this problem is taken care of during implementation by picking either the left or the right gradient.

To address the vanishing gradient issue in ReLU activation function when x < 0 we have something called Leaky ReLU which was an attempt to fix the dead ReLU problem. Let’s understand leaky ReLU in detail.

### 5.4. Leaky ReLU

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/leaky-relu-activation.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/leaky-relu-activation.png)Figure : Leaky ReLU activation function

This was an attempt to mitigate the dying ReLU problem. The function computes

  ![\[ f(x) = max(0.1x, x) \]](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-25fb00e1be31bb8dba5e319c5ee315e4_l3.png)

The concept of leaky ReLU is when x < 0, it will have a small positive slope of 0.1. This function somewhat eliminates the dying ReLU problem, but the results achieved with it are not consistent. Though it has all the characteristics of a ReLU activation function, i.e., computationally efficient, converges much faster, does not saturate in positive region.

The idea of leaky ReLU can be extended even further. Instead of multiplying x with a constant term we can multiply it with a hyperparameter which seems to work better the leaky ReLU. This extension to leaky ReLU is known as Parametric ReLU.

### 5.5. Parametric ReLU

The PReLU function is given by

$f(x) = \max(\alpha x, x)$

Where ![\alpha](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-0210468bf4cb3a50550e30ce7951201b_l3.png) is a hyperparameter. The idea here was to introduce an arbitrary hyperparameter ![\alpha](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-0210468bf4cb3a50550e30ce7951201b_l3.png), and this ![\alpha](https://www.learnopencv.com/wp-content/ql-cache/quicklatex.com-0210468bf4cb3a50550e30ce7951201b_l3.png) can be learned since you can backpropagate into it. This gives the neurons the ability to choose what slope is best in the negative region, and with this ability, they can become a ReLU or a leaky ReLU.

**In summary, it is better to use ReLU, but you can experiment with Leaky ReLU or Parametric ReLU to see if they give better results for your problem**

### 5.6. SWISH

[![img](https://www.learnopencv.com/wp-content/uploads/2017/10/swish.png)](https://www.learnopencv.com/wp-content/uploads/2017/10/swish.png)Figure: SWISH Activation Function

Also known as a self-gated activation function, has recently been released by researchers at Google. Mathematically it is represented as

$\sigma(x) = \frac{x}{1 + e^{-x}} $

According to the [paper](https://arxiv.org/abs/1710.05941v1), the SWISH activation function performs better than ReLU

From the above figure, we can observe that in the negative region of the x-axis the shape of the tail is different from the ReLU activation function and because of this the output from the Swish activation function may decrease even when the input value increases. Most activation functions are monotonic, i.e., their value never decreases as the input increases. Swish has one-sided boundedness property at zero, it is smooth and is non-monotonic. It will be interesting to see how well it performs by changing just one line of code.

## Subscribe & Download Code

If you liked this article and would like to download code (C++ and Python) and example images used in other posts of this blog, please [subscribe](https://bigvisionllc.leadpages.net/leadbox/143948b73f72a2%3A173c9390c346dc/5649050225344512/) to our newsletter. You will also receive a free [Computer Vision Resource](https://bigvisionllc.leadpages.net/leadbox/143948b73f72a2%3A173c9390c346dc/5649050225344512/) Guide. In our newsletter, we share OpenCV tutorials and examples written in C++/Python, and Computer Vision and Machine Learning algorithms and news.