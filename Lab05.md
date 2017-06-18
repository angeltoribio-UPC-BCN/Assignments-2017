# Lab 5: Under the Hood of Cloud Advanced Analytics Services

In previous lab we suggested an "optional" task that uses the [Cloud Vision API](https://cloud.google.com/vision/) from Google as an example of Advanced Analytics as a Service in the Cloud. The platform to support this type of services require high performance computing resources as GPU accelerators and a powerful software, as TensorFlow, their advanced analytics engine. 

On this lab we will look under the hood of these types of advanced analytics services in the Cloud, either in terms of hardware and software, in order to understand how their high performance requirements can be provided. We will use a GPU cluster (thanks to the support of Barcelona Supercomputing Center) and we will review the main characteristics of [TensorFlow](https://www.tensorflow.org), the most popular framework to build Artificial Intelligent models nowadays. 

## Task 5.1: Install TensorFlow in your local laptop
* Follow the installation instructions from https://www.tensorflow.org/install/ (I recommend the virtualenv installation)
* Clone or download this repository https://github.com/jorditorresBCN/FirstContactWithTensorFlow-2nEdition
* Validate your TensorFlow installation (assuming that target directory is ~/tensorflow ):

```
source ~/tensorflow/bin/activate 
git clone https://github.com/jorditorresBCN/FirstContactWithTensorFlow-2ndEdition.git
cd FirstContactWithTensorFlow-2ndEdition/
python ValidateYourInstallation.py
```

If the system outputs the following, 
```
Hello, TensorFlow!
```

then you are ready to begin writing TensorFlow programs!


##  Tasks 5.2: TensorFlow basics
Follow course slides https://github.com/jorditorresBCN/Assignments-2017/blob/master/First_Contact_with_TF_2nEd_slides-3.pdf

* Hands-on TF.1: TensorFlow basics
* Hands-on TF.2: Linear Regression in TensorFlow
* Hands-on TF.3: Clustering in TensorFlow
* Hands-on TF.4: Single Layer Neural Network in TensorFlow 
* Hands-on TF.5: Multi-layer Neural Network in TensorFlow 

##  Tasks 5.3: TensorFlow and Parallelism 
Follow course slides https://github.com/jorditorresBCN/Assignments-2017/blob/master/First_Contact_with_TF_2nEd_slides-3.pdf

* Hands-on TF.6: Multiple GPUs and TensorFlow 


# How to Submit this Assignment:  
Submit **before the deadline** to the *RACO Practicals section* a "Lab5.txt" file including: 

1. Group number
2. Name and email of the members of this group
3. Include  all`.py` files created during TF hands-on
4. Add any comment that you consider necessary.
