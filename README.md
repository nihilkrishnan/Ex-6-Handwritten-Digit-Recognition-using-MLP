# Skill Assisessment-Handwritten Digit Recognition using MLP

## Aim :

To Recognize the Handwritten Digits using Multilayer perceptron.

##  EQUIPMENTS REQUIRED :

Hardware – PCs
Anaconda – Python 3.7 Installation / Google Colab /Jupiter Notebook

## Theory :

Handwriting recognition is the ability of a computer to receive and interpret intelligible handwritten input from sources such as paper documents, photographs, touch-screens, and other devices. There are many techniques to that have been developed to recognize the handwriting. One of them is Handwritten Digit Recognition. In this project, we would be using Machine Learning classifier Multi-layer Perceptron Neural Network.

An MLP is a supervised machine learning (ML) algorithm that belongs in the class of feedforward artificial neural networks. The algorithm essentially is trained on the data in order to learn a function. Given a set of features and a target variable (e.g. labels) it learns a non-linear function for either classification or regression.

MLP classifier is a very powerful neural network model that enables the learning of non-linear functions for complex data. The method uses forward propagation to build the weights and then it computes the loss. Next, back propagation is used to update the weights so that the loss is reduced. This is done in an iterative way and the number of iterations is an input hyperparameter. Other important hyperparameters are the number of neurons in each hidden layer and the number of hidden layers in total. These need to be fine-tuned.

It can be seen that the machine learning model can recognize the hand written digits. Though the accuracy is about 83% but still it can be increased by using Convolution Neural Network or Support Vector Machine classifier of machine learning with proper tuning.


## Algorithm :

### Step 1 :

Import the necessary libraries of python.

### Step 2 :

In the end_to_end function, first calculate the similarity between the inputs and the peaks. Then, to find w used the equation Aw = Y in matrix form. Each row of A (shape: (4, 2)) consists of index [0]:

### Step 3 :

Similarity of point with peak 1 index [1]: similarity of point with peak 2 index[2]: Bias input (1) Y: Output associated with the input (shape: (4, )) W is calculated using the same equation we use to solve linear regression using a closed solution (normal equation).

### Step 4
:
This part is the same as using a neural network architecture of 2-2-1, 2 node input (x1, x2) (input layer) 2 node (each for one peak) (hidden layer) 1 node output (output layer).

### Step 5 :

To find the weights for the edges to the 1-output unit. Weights associated would be: edge joining 1st node (peak1 output) to the output node edge joining 2nd node (peak2 output) to the output node bias edge.

### Step 6 :

End the program.

## Program :

### DEVELOPED BY : NIHIL K K
### REG NO : 212221223003

### Program to Recognize the Handwritten Digits using Multilayer perceptron (MLP).

```
import numpy as np
import pandas as pd
from matplotlib import pyplot as plt

data = pd.read_csv('train.csv')
data = np.array(data)
m, n = data.shape
np.random.shuffle(data)

data_dev = data[0:1000].T
Y_dev = data_dev[0]
X_dev = data_dev[1:n]
X_dev = X_dev / 255.

data_train = data[1000:m].T
Y_train = data_train[0]
X_train = data_train[1:n]
X_train = X_train / 255.
_,m_train = X_train.shape
Y_train

def init_params():
    W1 = np.random.rand(10, 784) - 0.5
    b1 = np.random.rand(10, 1) - 0.5
    W2 = np.random.rand(10, 10) - 0.5
    b2 = np.random.rand(10, 1) - 0.5
    return W1, b1, W2, b2
    
def ReLU(Z):
    return np.maximum(Z, 0)
    
def softmax(Z):
    A = np.exp(Z) / sum(np.exp(Z))
    return A
    
def forward_prop(W1, b1, W2, b2, X):
    Z1 = W1.dot(X) + b1
    A1 = ReLU(Z1)
    Z2 = W2.dot(A1) + b2
    A2 = softmax(Z2)
    return Z1, A1, Z2, A2
    
def ReLU_deriv(Z):
    return Z > 0

def one_hot(Y):
    one_hot_Y = np.zeros((Y.size, Y.max() + 1))
    one_hot_Y[np.arange(Y.size), Y] = 1
    one_hot_Y = one_hot_Y.T
    return one_hot_Y
    
def backward_prop(Z1, A1, Z2, A2, W1, W2, X, Y):
    one_hot_Y = one_hot(Y)
    dZ2 = A2 - one_hot_Y
    dW2 = 1 / m * dZ2.dot(A1.T)
    db2 = 1 / m * np.sum(dZ2)
    dZ1 = W2.T.dot(dZ2) * ReLU_deriv(Z1)
    dW1 = 1 / m * dZ1.dot(X.T)
    db1 = 1 / m * np.sum(dZ1)
    return dW1, db1, dW2, db2

def update_params(W1, b1, W2, b2, dW1, db1, dW2, db2, alpha):
    W1 = W1 - alpha * dW1
    b1 = b1 - alpha * db1    
    W2 = W2 - alpha * dW2  
    b2 = b2 - alpha * db2    
    return W1, b1, W2, b2
    
def get_predictions(A2):
    return np.argmax(A2, 0)    
    
def get_accuracy(predictions, Y):
    print(predictions, Y)
    return np.sum(predictions == Y) / Y.size
    
def gradient_descent(X, Y, alpha, iterations):
    W1, b1, W2, b2 = init_params()
    for i in range(iterations):
        Z1, A1, Z2, A2 = forward_prop(W1, b1, W2, b2, X)
        dW1, db1, dW2, db2 = backward_prop(Z1, A1, Z2, A2, W1, W2, X, Y)
        W1, b1, W2, b2 = update_params(W1, b1, W2, b2, dW1, db1, dW2, db2, alpha)
        if i % 10 == 0:
            print("Iteration: ", i)
            predictions = get_predictions(A2)
            print(get_accuracy(predictions, Y))
    return W1, b1, W2, b2
    W1, b1, W2, b2 = gradient_descent(X_train, Y_train, 0.10, 500)
    
def make_predictions(X, W1, b1, W2, b2):
   _, _, _, A2 = forward_prop(W1, b1, W2, b2, X)
   predictions = get_predictions(A2)
   return predictions
   
def test_prediction(index, W1, b1, W2, b2):
   current_image = X_train[:, index, None]
   prediction = make_predictions(X_train[:, index, None], W1, b1, W2, b2)
   label = Y_train[index]
   print("Prediction: ", prediction)
   print("Label: ", label)
   current_image = current_image.reshape((28, 28)) * 255
   plt.gray()
   plt.imshow(current_image, interpolation='nearest')
   plt.show()
   test_prediction(0, W1, b1, W2, b2)
   test_prediction(1, W1, b1, W2, b2)
   test_prediction(2, W1, b1, W2, b2)
   test_prediction(3, W1, b1, W2, b2)
   dev_predictions = make_predictions(X_dev, W1, b1, W2, b2)
   get_accuracy(dev_predictions, Y_dev)   
   ```


## Output :

### Y_TRAIN :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/6a59cb8b-893d-4dbd-b4fa-c849d73f77b1)

### ITERATIONS FROM 1-100 :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/ced44727-5d7a-4afb-8c84-a27fae3d1528)

### ITERATIONS FROM 110-230 :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/7c40947f-bcdf-4d28-9e69-7e774211b33d)

### ITERATIONS FROM 240-340 :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/b6115fb9-d0d6-47e9-8886-f387b92db7e1)

### ITERATIONS FROM 350-450 :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/017df91a-563a-4dd2-92cb-a016d6bf376e)

### ITERATIONS FROM 460-490 :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/e24edda0-381a-4866-81e4-1c1a749fdfcf)

### PREDICTING 5 :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/b6ddb0b1-a75f-4930-b07e-4331a3da4260)

### PREDICTING 2 :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/c156f953-69b8-4c85-8bf9-eb6f8b04502d)


### DEV_PREDICTIONS :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/58b08302-a620-435a-ae11-09e34a365f2f)


![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/da8d9653-2655-4ad9-9810-bd9d7db230cb)


### ACCURACY :

![image](https://github.com/Abrinnisha6/Ex-6-Handwritten-Digit-Recognition-using-MLP/assets/118889454/7c838c8b-8a5a-492a-b7c1-b3cbf6bbe11c)


## Result :

Thus, the program to recognize the handwritten digits using the multi-layer perceptron (MLP) is developed and executted successfully.
