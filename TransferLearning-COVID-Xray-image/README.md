# A framework of deep learning classifier to diagnose COVID19 pneumonia using X-ray images
### The course project in Image and Video processing, Spring 2020.  

___
This is a deep learning-based framework that automatically detects pneumonia from X-rays images, and distinguishes pneumonia caused by COVID19, influenza, and bacteria to facilitate rapid proper intervention for patients. This deep learning framework is a transfer learning model using InceptionV3 architectures. The dataset used is a collection of 5651 X-ray images that contain 241 COVID19, 2577 bacterial pneumonia, 1356 other pneumonia, and 1377 normal conditions. 

___
### Four Datasets sources:
1.https://github.com/ieee8023/covid-chestxray-dataset COVID19: 190, bacterial: 39, SARS: 11  
2.https://www.kaggle.com/nabeelsajid917/covid-19-x-ray-10000-images COVID19: 72, normal: 28  
3.https://www.kaggle.com/paultimothymooney/chest-xray-pneumonia Bacterial: 2538, influzenza:1345, normal:1349  
4.https://www.kaggle.com/andrewmvd/convid19-x-rays COVID: 79  

### Image registration
To reduce the technical noises such as the exposure and translational variance, histogram equalization and image registration were used to preprocess the images. Different image registration methods are explored. Finally, insight Toolkit (ITK) was used for image registration.

### Transfer learning

Inception V3 was used for the transferred model. Three methods for transfer learning were used. First, only the architecture of the inception V3 is used, called no pre-trained. Second, the inception V3 with its pertained parameters as the initial parameters was used, and in the backpropagation, the gradient of parameters was learned. The last one, the pre-trained inception V3 was used, but all parameters in the model except the final output layer were frozen. It meant that in the backpropagation the parameters in final layer were learned, and the parameters in other layers will not be changed.

<p align="center"><img src="https://github.com/yuhanH/Deep-learning-application/tree/main/TransferLearning-COVID-Xray-image/model_performance.png" height="512" /></p>

From the performance of these three transfer learning models, The no pre-trained model converged very slowly which corresponded to the results from in which the models usually converged after 80 epochs (not in the figure). The pre-trained model converged the fastest among these three models. For the pre-trained frozen model, it was converged quickly, but the loss was still big. It might result from the small number of learnable parameters in this model, only the final lay parameters. 
