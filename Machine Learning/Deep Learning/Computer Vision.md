
*Computer Vision* or CV is one of the biggest areas for Deep Learning, along with [NLP](NLP.md). Computer vision deals with the realm of images and videos, both their understand and other transformations. Computer vision tasks are generally implemented with [Convolutional Neural Networks](Convolutional%20Neural%20Net.md) and more increasingly, Vision [Transformers](Transformers.md) are becoming the state-of-the art.

## Image Augmentation

Large datasets are crucial in deep learning. *Image Augmentation* generates similar but distinct training examples after a series of random changes to the training images, thereby expanding the size of the training set. Alternatively, image augmentation can be motivated by the fact that random tweaks of training examples allow models to less rely on certain attributes, thereby improving their [generalization](../Bias%20vs.%20Variance.md) ability.

For example, we can crop an image in different ways to make the object of interest appear in different positions, reducing the dependence of a model on the position of the object. We can also adjust factors such as brightness and color to reduce a model's sensitivity to color. Image augmentation is an indispensable part of computer vision [Preprocessing](../MLOps/Preprocessing.md).


## Computer Vision Applications

![](../../Attachments/Pasted%20image%2020230301013518.png)

#### Image Classification

The classic CV task is Image [Classification](../Classification.md). In these tasks, the model tries to categorize an image into a class based on the training data. This is the task done on the famous ImageNet contest. This can be combined with localization to find the exact location of the subject in the image. This can then be shown with a bounding box to let the user see where the subject is detected

#### Object Detection

In image classification, we assume that there is only *one* major object in the image and we only focus on how to recognize its category. However, there are often *multiple* objects in the image. We not only want to know their categories, but also their specific positions in the image. In CV, we refer to such tasks as *Object Detection* (or object recognition).

#### Semantic Segmentation

In object detection tasks, rectangular bounding boxes are used to label and predict objects in images. *Semantic Segmentation* divides an image into regions belonging to different semantic classes. You can also only highlight the region of the object in question, in a task called instance segmentation.