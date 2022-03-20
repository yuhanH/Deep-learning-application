# Sequential functional states analysis of scCUT&TAG-Pro data using convolution neural network

In this work, I built a multi-task deep convolutional neural network to predict cell type-specific regulatory functional activity of DNA sequences from single-cell genomics data. I used the single-cell cut&tag-pro data profiling blood immune cells from the blood. Cut&tag-pro data measures various histone modifications and surface protein for individual cells. Based on the patterns of histone modifications, we can determine the DNA sequence functional roles in different cell types. My goal is to find the relationship between DNA sequences and their cell-type-specific functional roles in genomics.

___  

## Data preprocess
The single-cell cut&tag-pro data is from [Zhang & Srivastava et al. (2021)](https://www.biorxiv.org/content/10.1101/2021.09.13.460120v1). Cells are annotated by the surface protein markers and the whole genome is chunked to 200bp DNA fragments. The cells from the same cell type are aggregated to enhance the signal. To simplify the model, I just select four cell types as the input data: CD8 T, CD4 T, B cells, and monocytes and only consider if the DNA fragment has the role of promoter. Thus, in the aggregated data, each 200bp DNA fragment has four outputs, probability of promoters in CD8 T, CD4 T, B cells, and monocytes. Due to the imbalance of the data, I sampled an equal number of negative samples (non-promoter) DNA fragments to the positive samples also controlling for the GC content. 
After preprocessing, the input $ X $ has 263837 200bp DNA fragments (samples) and four base-pairs as features (ATCG) and the output $Y$ has four probabilities for each sample. 

<img src="https://render.githubusercontent.com/render/math?math={  X \in R^{n_{DNAfrag} \times 4} }}">
<img src="https://render.githubusercontent.com/render/math?math={  Y \in R^{n_{DNAfrag} \times 4} }}">


___  

## Model archtecture
This is a multi-task deep convolutional neural network. The basic model architecture is from [Kelly et al. (2016)](https://genome.cshlp.org/content/26/7/990.long) and [Almeida et al. (2021)](https://www.biorxiv.org/content/10.1101/2021.10.05.463203v1). It starts with several convolution filters to capture local DNA patterns. Then it goes to the fully connected layers to model the connection between local DNA patterns to its functional role. In the end, it has a task-specific layer accounting for the cell-type-specific functional role. Poisson loss is used here to give more penalties on false-negative results prediction.

<p align="center"><img src="https://github.com/yuhanH/Deep-learning-application/blob/main/CNN-GenomicPromoter/framework.png" height="200" /></p>

$$ Y^{t}= W^{t}f(X,W), t \in [0,3]  $$
$$ Y^t \in R^{n_{DNAfrag} \times 1} $$

The architecture overview (this plot is modified from https://github.com/bernardo-de-almeida/DeepSTARR/)

___  

## Importance score
The model generally works well. The performance also varies across different cell types. The result clearly shows that Poisson loss can effectively avoid false-negative predictions.
<p align="center"><img src="https://github.com/yuhanH/Deep-learning-application/blob/main/CNN-GenomicPromoter/performance.png" height="400" /></p>
After training the model, the model enables exploring the importance of each base-pair in the 200bp DNA fragment for the prediction. I calculated the importance score for each base-pair of each DNA fragment. The importance score was introduced by [Shrikumar er al. 2018](https://arxiv.org/abs/1811.00416). The general idea is to mask one base-pair and put the masked DNA into the model, and the difference of prediction probability is considered as the importance score. 

$$ P(X) - P(X_{d(i)}) $$

Here is one example of a conserved DNA promoter region for these four cell types. It shows that what base-pairs have positive or negative influence on the role of promoter regions. 
<p align="center"><img src="https://github.com/yuhanH/Deep-learning-application/blob/main/CNN-GenomicPromoter/conserved promoter.png" height="512" /></p>



