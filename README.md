## Introduction

This was the final project of my Data Science bootcamp at Jedha. I had chance to work with two highly motivated tech-women on this topic.
The main problem was to find a way to identify precise patterns in human protein cell images. From a data scientist point of view, this was a multi-label classification model (18 labels) with Image Segmentation.



## Table of contents
* [Tech Stack](#tech-stack)
* [The Problem](#the-problem)
* [The Dataset](#the-dataset)



## Tech Stack

#Python #Tensorflow #Deep Learning #Computer Vision #Transfert Learning 

> A thorough description of the problem is available on Kaggle's website : https://www.kaggle.com/c/hpa-single-cell-image-classification/overview/description 




## The Problem
Given human cells images with different regions of interest (called [organelles](https://en.wikipedia.org/wiki/Organelle)),
predict the protein organelle localization labels for **each cell** in the image. In other terms, we have to predict the **type** and **localization** of these [organelles](https://en.wikipedia.org/wiki/Organelle) given a random image consisting of many (often different) human cells. 


![RGB](https://github.com/Proxima-centaury-b/Kaggle_Cell_Classification/blob/main/Images/RGB.png)///////////////////////////////////////////![mask](https://github.com/Proxima-centaury-b/Kaggle_Cell_Classification/blob/main/Images/mask.png)

                
The left image is the combination of three "channel" images (Red Green Blue) into a single colorfull image where we can see different cells. The right image is a **multilabel mask**
 obtained by image segmentation, which allows to localize each cells in the image. However, the problem is to localize each protein of interest inside each cells in the image.


At first sight, this problem seemed to be quite standard to handle, I mean using techniques of image classification model. But there were several complex considerations we have to deal with : 
- Volume = the size of dataset : huge for computations (running-time problems), but not enough for acceptable training accuracy (need Data Augmentation and other tricks)
- Variety of images: comprises 17 different cell types of highly different morphology, which affect the protein patterns of the different organelles.
- Velocity : recurrent problems of huge running-times due to size of dataset and training with Deep Neural Networks (200+ layers)
- Highly multi-labelled : there are in total 19 different labels present in the dataset (18 labels for specific locations, and label 18 for negative and unspecific signal)


Indeed, there are in total 19 different labels present in the dataset (18 labels for specific locations, and label 18 for negative and unspecific signal). The dataset is acquired in a highly standardized way using one imaging modality (confocal microscopy). However, the dataset comprises 17 different cell types of highly different morphology, which affect the protein patterns of the different organelles. All image samples are represented by four filters (stored as individual files), the protein of interest (green) plus three cellular landmarks: nucleus (blue), microtubules (red), endoplasmic reticulum (yellow). The green filter should hence be used to predict the label, and the other filters are used as references. The labels are represented as integers that map to the following:

0. Nucleoplasm
1. Nuclear membrane
2. Nucleoli
3. Nucleoli fibrillar center
4. Nuclear speckles
5. Nuclear bodies
6. Endoplasmic reticulum
7. Golgi apparatus
8. Intermediate filaments
9. Actin filaments
10. Microtubules
11. Mitotic spindle
12. Centrosome
13. Plasma membrane
14. Mitochondria
15. Aggresome
16. Cytosol
17. Vesicles and punctate cytosolic patterns
18. Negative



Moreover, the labels we have to predict are **weak** image-level labels which means that the labels for training are **image level labels** while the task is to predict **cell level** labels which is totally different, and much more complicated. That is to say, each training image contains a number of cells that have collectively been labeled and the prediction task is to look at images of the same type and predict the labels of each individual cell within those images.

As the training labels are a collective label for all the cells in an image, it means that each labeled pattern can be seen in the image but not necessarily that each cell within the image expresses the pattern. This imprecise labeling is what is refer to as **weak**.


Our task is then to 
- **segment the cells in the images**
- **and predict the labels of those segmented cells**



## The Dataset

Train and test sets were provided by The Human Protein Atlas research group  which is an initiative based in Sweden that is aimed at mapping proteins in all human cells, tissues, and organs regrouping leading researchers all around the world. These dataset was quite huge and consisted of :

- 21.186 images of high resolution (a mix of 1728x1728, 2048x2048 and 3072x3072 PNG files) splitted in a train + test set
- A CSV file containing image level labels (each image is assign one or more of the 19 labels: for example [2,8,11,15] )
- a total of 160GB storage

So this was a real Big Data problem, which required many tricks to avoid endless computation, as we had to put our images (by batches) in a very deep Neural Network consisting 200+ layers and 4+ millions training parameters (derived from the so-called EfficientNetB0). For this task, we used GPU hardware provided by Kaggle to reach acceptable running-times (up to 12-15 hours)


