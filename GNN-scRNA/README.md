# Graph laplacian and graph convolutional neural network for single-cell data
### The course project in [Mathematical Tools for Data Science](https://cds.nyu.edu/math-tools/), Spring 2021.  

___
In this work, I first explored the graph Laplacians in a scRNA-seq from [3K blood cells](https://satijalab.org/seurat/articles/pbmc3k_tutorial.html). I constructed the KNN
graph and explore the eigenvector of its Laplacian matrix. I demonstrated each eigenvector represents a partition of different cell types. The eigenvector with a small eigenvalue represents a low “frequency” cell-type information. Next, to further analyze scRNA data from [a COVID-19 project](https://www.cell.com/cell/fulltext/S0092-8674(20)31444-6?_returnURL=https%3A%2F%2Flinkinghub.elsevier.com%2Fretrieve%2Fpii%2FS0092867420314446%3Fshowall%3Dtrue), I chose to implement the node classification task of the GCN work introduced by [Kipf & Illing (2017)](https://arxiv.org/abs/1609.02907) in the Deep Graph Library. This GCN model both utilities
the cell type information in the adjacency graph and learned the pathological related variation by node classification. Lastly, I used the embeddings of hidden layers in GCN to identify and validate heterogeneous cell states in blood and explored how the cells in the human blood respond to SARS-CoV-2 infection differently.

___

### Graph laplacian of the KNN from single-cell RNA sequencing data

In the visualization of 3K blood in the UMAP calculated from PCA embeddings, we can observe seven major cell types in the blood which are annotated by their marker genes (Figure 1A). In their KNN graph, an undirected adjacency matrix, different cell types form blocks due to the shared neighbors, and within similar cell types (CD8Naive, CD4 Naive, CD4 Memory), the block boundaries are blurred with each other (Figure 1B). I performed graph Laplacian operator on this KNN graph and the derived eigenvectors clearly show their strong associations with cell types. For example, the second eigenvector is positive in Monocytes and Dendritic cells while T cell, NK cell, and B cells are negative along the second eigenvector (Figure 1C). The fifth eigenvector is distinguishable in the sub-population of monocytes and the 49th eigenvector shows the difference among very small populations. It is interesting that with the increase of eigenvalue, the eigenvector defines the smaller and smaller cell clusters. The result demonstrates that the eigenvalue of the Laplacian matrix is analogous to the frequency in the Fourier transform.
<p align="center"><img src="https://github.com/yuhanH/Deep-learning-application/blob/main/GNN-scRNA/pbmc3k graph laplacian.png" height="512" /></p>


### Graph convolutional neural network covid classification

For the application of the GCN model, I use the scRNA-seq data from [Su et al. (2020)](https://www.cell.com/cell/fulltext/S0092-8674(20)31444-6?_returnURL=https%3A%2F%2Flinkinghub.elsevier.com%2Fretrieve%2Fpii%2FS0092867420314446%3Fshowall%3Dtrue). I down-sampled to 20K
cells in total, a half from COVID-19 and the other half from healthy donors. I first performed the PCA analysis and visualized cells from the 50 dimensions of PCA embeddings in the 2d Uniform Manifold Approximation and Projection (UMAP). It showed that cells from healthy and COVID-19 are generally mixed in most cell types (Figure 2A). It indicates that the majority variance in the dataset is the variance among different cell types rather than the cellular responses in the COVID-19 pathological process. 

Next, in this work, I built a two-layer GCN for node classification (healthy or COVID-19) on an undirectly KNN graph. I first performed PCA analysis and constructed the KNN graph ($A$) for cells from both healthy and COVID-19 patients. Then, I normalized KNN graph $A$ as 
$$\tilde A = D^ {-\frac{1}{2}} A D^{ -\frac{1}{2}} $$ 
The forward model and the cross-entropy are defined in the same model from [Kipf & Illing (2017)](https://arxiv.org/abs/1609.02907).

\begin{equation}
Z = f(X,A) =  \textrm{softmax}(\tilde A  \textrm{ ReLU}(\tilde AXW^{(0)})W^{(1)})
\end{equation}
In this work, the input layer uses 5000 features and the first hidden layer has 50 dimensions. 
$$ W^{(0)} \in \mathbb{R}^{2000 \times 50} $$
$$ W^{(1)} \in \mathbb{R}^{50 \times 2} $$

Using the above model, I trained the GCN model with 1000 epochs, and Table 1 showed training, validation, and test accuracy. The training accuracy was slightly higher than the validation and test groups. When I stratified the accuracy into different cell types, I found that monocytes had the highest accuracy and it matched with previously reported results that monocytes had the strongest response in the COVID19 patients.

<p align="center"><img src="https://github.com/yuhanH/Deep-learning-application/blob/main/GNN-scRNA/GCN performance.png" height="200" /></p>

Lastly, I further explore the GCN model I trained. I construct a UMAP visualization using the 50-dimensional latent space from the first hidden layer. Compared with UMAP from PCA space, healthy and COVID19 cells have a better separation in the UMAP and cell-type information are retained as well.

<p align="center"><img src="https://github.com/yuhanH/Deep-learning-application/blob/main/GNN-scRNA/UMAP covid.png" height="512" /></p>
Figure 2: Visulization of 20K blood cells from COVID-19 and healthy cells (A, B) UMAP visulization of cells in the PCA space and GCN hidden layer space. Cells are annotated by marker genes.The COVID-19 and normal status are true labels from the dataset.
