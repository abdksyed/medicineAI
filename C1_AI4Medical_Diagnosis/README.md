# AI for Medical Diagnosis

## Module 01

### Pre Requisites
* Basics of Deep Learning  - Supervised Learning, Conv. Neural Networks and Loss functions.
* Python Programming
* Some Knowledge of Probability.

### Applications
* Chest X-Ray Classification
* Cancer detection in Dermatology [Source](https://www.nature.com/articles/nature21056)
* Diabetic Retinopathy in Ophthalmology [Source](https://www.nature.com/articles/s41433-018-0269-y)
* Cancer Spread to Lymph Nodes through Histopathology [Soruce](https://pubmed.ncbi.nlm.nih.gov/30312179/)

### Training Challenges
* Class Imbalance
    * Weigted Loss
    * Resampling (Undersample and Oversample)
* Multi-Task (More than one class in an Image)
    * Multi Label Loss - Sum of Binary Loss for each prediction
* Small Datasize
    * Pretraining on Natural Images and Fine-Tuning on Medical Dataset
        * Two Ways to do so - Update all layers (or) Update only later layers
    * Generating More Data
        * Data Augmentation like Color Contrast, Rotate, Translate, Zoom, Brightness or combinations.

### Testing

Splitting the Data into three subsets
* Training - For Training of Model
* Validation - For Hyper parameter tuning/optimization
* Testing - For reporting metrics.

### Testing Challenges
* Patient Overlap
    * Splitting Data by Patient
* Set Sampling
    * There maybe no examples of a label in test set due to high imbalance
        * Hence, we would want to have X% (50%) of data with minorty class.
* Ground Truth
    * Inter-Observer Disagreement
        * Consensus Voting - Majority Vote or Make a group to discuss and decide
        * Additional Medical Testing. For Ex: CT Scan to see Mass in Lungs and assign Mass label to XRay.
     
## Module 2

### Evaluation Metrics
* Accuracy = Correctly Classified / Total No. of Examples
    * $Accuracy = P(correct) = P(correct \cap disease) + P(correct \cap normal)$
    * Using $P(A \cap B) = P(A|B)P(B)$
    * $Accuracy = P(correct|disease)P(disease) + P(correct|normal)P(normal)$
    * $Accuracy = P(+|disease)P(disease) + P(-|normal)P(normal)$

* Sensitivity (True Positive Rate) (Recall for Positive)
    * If the patient has the disease, what is the probability that the model predicts positive(disease)?

* Specificity (True Negative Rate) (Recall for Negative)
    * If the patient is normal, what is the probability that the model predicts negative(normal)?
 
* $Accuracy = Sensitivity * P(disease) + Specificity * P(normal)$
* $Accuracy = Sensitivity * prevelance + Specificity * (1-prevelance)$

* Positive Predictive Value (PPV)
    * $P(disease|+)$ - If a model prediction is positive, what is the probability that a patient has the disease.

* Negative Predictive Value (NPV)
  * $P(normal|-)$ - If a model prediction is negative, what is the probability that a patient is normal .
 
* Confusion Matrix
    * The matrix is defined with first row as Ground Truth Positive (Disease) and 2nd row as Ground Truth Negative (Normal)
    * The first column in the matrix is model predicted as positive, and the second row is model predicted negative.
    * $Sensitivity = \frac{TP}{TP+FN}$(TP+FN = sum of first row)
    * $Specificity = \frac{TN}{FP+TN}$(FP+TN = sum of second row)
    * $PPV = \frac{TP}{TP+FP}$(TP+FP = sum of first column)
    * $NPV = \frac{TN}{TP+FP}$(FN+TN = sum of second column)
 
#### PPV in terms of Sensitivity, Specificity and Prevelance
$PPV = P(pos|pøs)$
(pos is "actually positive" and pøs is "predicted positive").

By Bayes Rule, this is 
$$
    PPV = \frac{P(pøs | pos) * P(pos)}{P(pøs)}
$$

Numerator:
* $P(pøs|pos)$ is Sensitivity
* $P(pos)$ is Prevelance

Denominator:
* $P(pøs)=TruePos. + FalsePos.$
* $TruePos=P(pøs∣pos)×P(pos)$
* $True Pos. = Senstivity * Prevalence$
* $FalsePos=P(pøs∣neg)×P(neg)$
* $1−specificity=P(pøs∣neg)$
* $1−prevalence=P(neg)$

$$
PPV = \frac{sensitivity * prevalence}{sensitivity * prevalence + (1-specificity)*(1-prevalence)}
$$

### Threshold and Eval Metrics

#### Threshold
The value (betweeon 0 and 1), at which we predict the output as *postive* if the probability is above this value, else we predict *negative* if the probability is below the threshold

At **t = 0**, we classify all the data points as positive, hence the sensitivity is 1 and the specificity is 0.  
At **t = 1**, we the sensitivity becomes 0 and the specificity is 1.

#### ROC

$FPR = \frac{FP}{FP+TN}$  
$TPR = \frac{TP}{TP + FN}$  
$\text{precision} = \frac{TP}{TP + FP} = PPV$


### Confidence Intervals

In repeated sampling, the method produces intervals that include the population accuracy in about 95% of the samples.  
The larger the sample size, the close the interval will be in the confidence interval.


## Module 03

### Image Segmentation
In MRI Scans, the MRI sequence is a 3D volume made up of multiple sequences, and thus will consist of multiple 3D volumes. The key idea that we will use to combine the information from different sequences is to treat them as different channels. One challenge with combining these sequences is that they may not be aligned with each other. The basic idea with image registration is to transform the images so that they're aligned or registered to each other.

* 2D Approach
    * Break up the 3D MRI volume into many 2D slices.
    * Each slice is passed into a segmentation model which outputs the segmentation for that slice.
    * 2D slices combined to form the 3D output volume of the segmentation.
    * Drawback with 2D approach is that we might lose important 3D context when using this approach.
* 3D Approach
    * Ideally, we'd want to pass in the whole MRI volume into the segmentation model.
    * Size of the MRI volume makes it impossible to pass it in all at once into the model.
    * Break up the 3D MRI volume into many 3D subvolumes.
    * Feed the subvolumes into the model and then aggregate them at the end to form a segmentation map for the whole volume.
    * Disadvantage with this 3D approach is that we might still lose important spatial context.
 
### U Net
* U-Net was first designed for biomedical image segmentation and demonstrated great results on the task of cell tracking
* Achieve relatively good results, even with hundreds of examples.
* U-Net consists of two paths: a contracting path, and an expanding path.
* Contracting path is a typical convolutional network like used in image classification.
* Expanding path is the opposite of the contracting path. aking our small feature maps through a series of up-sampling and up-convolution steps to get back to the original size of the image.
* Concatenates the up-sample representations at each step with the corresponding feature maps at the contraction pathway.
* The architecture outputs the probability of tumor for every pixel in the image.

### 3D U-Net
* Similar to 2D, can feed any 3D subvolume into a segmentation architecture.
* 2D convolutions become 3D convolutions, and the 2D pooling layers become 3D pooling layers.

### Data Augmentation
* When doing Data Aug on the image, have to carry the same Data Aug on the GT segmentation mask.
* Transformations have to applied to the entire 3D volume.

### Loss Function
* Soft Dice Loss
$$
L(P,G)  = 1 - \frac{2\sum_i^np_ig_i}{\sum_i^np_i^2 + \sum_i^ng_i^2}
$$
