Med2Vec
=========================================

Med2Vec is a multi-layer representation learning tool for learning code representations and visit representations from EHR datasets

####Relevant Publications

Med2Vec implements an algorithm introduced in the following:
    Multi-layer Representation Learning for Medical Concepts
	Edward Choi, Mohammad Taha Bahadori, Elizabeth Searles, Catherine Coffey, Jimeng Sun
	arXiv preprint arXiv:1602.05568

####Running Med2Vec

**STEP 1: Installation**  

1. Install [python](https://www.python.org/), [Theano](http://deeplearning.net/software/theano/index.html). We use Python 2.7, Theano 0.7. Theano can be easily installed in Ubuntu as suggested [here](http://deeplearning.net/software/theano/install_ubuntu.html#install-ubuntu)

2. If you plan to use GPU computation, install [CUDA](https://developer.nvidia.com/cuda-downloads)

3. Download/clone the Med2Vec code  

**STEP 2: Preparing training data**  

1. Med2Vec training data need to be a Python Pickled list of list of medical codes (e.g. diagnosis codes, medication codes, or procedure codes). 
First, medical codes need to be converted to an integer. Then a single visit can be converted as a list of integers. 
For example, [5,8,15] means the patient was assigned with code 5, 8, and 15 at a certain visit. 
If a patient made two visits [1,2,3] and [4,5,6,7], it can be converted to a list of list [[1,2,3], [4,5,6,7]]. 
If there are multiple patients, each patient must be delimited by a list [-1]. 
For example, [[1,2,3], [4,5,6,7], [-1], [2,4], [8,3,1], [3]] means there are two patients where the first patient made two visits and the second patient made three visits. 
This list of list needs to be pickled using cPickle. We will refer to this file as the "visit file".

2. The total number of unique medical codes is required to run Med2Vec. 
For example, if the dataset is using 14,000 diagnosis codes and 11,000 procedure codes, the total number is 25,000. 

3. For a faster training, you can provide an additional dataset, which is simply the same dataset in step 1, but with grouped medical codes. 
For example, ICD9 diagnosis codes can be grouped into 283 categories by using [CCS](https://www.hcup-us.ahrq.gov/toolssoftware/ccs/ccs.jsp) groupers. 
You will still be able to learn the code representations for the original un-grouped codes. 
The grouped dataset is used only for speeding up the training speed. (Refer to section 4.4 of the paper) 
The grouped dataset should be prepared in the same way as the dataset in step 1. We will refer to this grouped dataset as the "label file".

4. Same as step 2, you will need to remember the total number of unique grouped codes if you plan to use this grouped dataset.

5. If you wish to use patient demographic information (e.g. age, weight, gender) you need to create a demographics vector for each visit the patient made. 
For example, if you are using age (real-valued) and ethnicity(categorical, assume 6 categories), you can create a vector such as [45.0, 0, 0, 0, 0, 1, 0]. 
Similar to the [-1] vector in step 1, each patient is delimited with an all-zero vector. 
Therefore the demographic information will be a pickled matrix where column size is the size of the demographics vector and row size is the number of total visits of all patients plus the delimiters. 
We will refer to this file as the "demo file".

6. Similar to step 2, you will need to remeber the size of the demographics vector if you plan to use the demo file. 
In the example of step 5, the size of the demographics vector is 7.

**STEP 3: Running Med2Vec**  

1. The minimum input you need to run Med2Vec is the visit file, the number of unique medical codes and the output path
`python med2vec <path/to/visit_file> <the number of unique medical codes> <path/to/output>`  

2. Specifying `--verbose` option will print training process after each 10 mini-batches.

3. Additional options can be specified such as the size of the code representation, the size of the visit representation and the number of epochs. Detailed information can be accessed by `python med2vec --help`

**STEP 4: Looking at your results**  

Med2Vec produces a model file after each epoch. The model file is generated by [numpy.savez_compressed](http://docs.scipy.org/doc/numpy-1.10.1/reference/generated/numpy.savez_compressed.html).
