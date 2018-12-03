# Viewing Graph

Let's first define what is a **Viewing Graph(VG)**.

***Definition**: A Viewing Graph is a graph which nodes represent images and edges represent the number of matches between two nodes.*

**Viewing Graph** is also called a **Match Graph**, which is first adopted by [2] - a milestone work of **Structure from Motion**.
As we know, **Feature Matching** is one of the main bottlenecks of 3D reconstruction. For sequential datasets, matching could performed only to its $k$
previous images. However, for unordered datasets, the exhaustive matching process takes lots of time. Thus, the structure of **Viewing Graph** should be explored to reduce the number of matching. 

## 1. Vocabulary Tree
**Vocabulary Tree** [1] is often adopted to accelarate the matching process. 

## 2. BRIAD<sup>[2]</sup>
In [2], vocabulary tree is used to measure the similarity of two images. For each image, the top scoring $k_1 + k_2$ images are identified, where the first $k_1$ images are used in an initial verification stage, and the additional $k_2$ images are used to enlarge the connected components. After the initial graph has been built, there may exist more than 1 connected components. For components of size 2 or more, the next $k_2$ images that suggested by the vocabulary tree are used to enlarge the graph. It should be noticed that, only image pairs that straddle two different connected components are considered. This step is called **components merging**. After component merging, the match graph is usually not dense enough to reliably produce a good reconstruction. Then, a **Query Expansion** step is proposed to enhance the match graph. Images $i$ and $j$ are connected if they have a certain minimum number of features in common. If image $i$ is connected to image $j$ and image $j$ is connected to image k, a detailed match is performed to check if image $i$ matches image $k$ (It should be noticed that, there is an error in the original paper). 

However, in that repeated query expansions lead to drift, where the context of the iamges found rapidly diverge from that of the initial image, especially as it starts examinging the neighbors of the neighbors, and so on. Although the geometric verification step in the matching process ensures that these pairs are not admitted into the graph as edgs, it greatly reduces the efficiency of the overall matching process because it tests more pairs unlikely to share edges - more query expansion, less efficient.
___
Although vocabulary tree help SfM pipelines find suitable image pairs to match more quickly, according to Cui [3], vocabulary tree has some disadvantages:

-  Constructing a vocabulary tree is computationally expensive (due to the computation of a tree encoding visual words and the iamge index) and can demand a large memory footprint. Specifically, creating the vocabulary tree requires an expensive clustering pass on a large set or subset of local image features for every node in the tree.

- Creating an image index requires passing each of the local features of every image in a dataset through the vocabulary tree, which is also computationally expensive.

- SfM pipelines have to load the image index in memory to find suitable image pairs to match in a large photo collection. In this scenario, substantial memory footprints are necessary since the image index is large as well.

Besides, the image representation that vocabulary tree use provides limited information about matching image pairs, as is shown in the figure below, the distribution of vocabulary trees similaarity scores for matching and non-matching image pairs overlap significantly.

<div align=center> 

![vt](img/vt.png)

</div>

## 3. Graph-based Consistency Matching

## 4. GraphMatch
Inspired by PatchMatch algorithm, GraphMatch [3] use a score computed from the distance between Fisher vectors of any two images. In their experiments, <u>Fisher vector distances are faster to compute and more indicative of a possible match than vocabulary tree similarity scores.</u> Besides, Graph Match uses a similar iterative "sample-and-propagate" algorithm to replace the query expansion step. In "sample-and-propagate" of PatchMatch, the correspondences of each patch are initialized to point to random pathces in the sampling step. Then in the propagation step, the valid correspondences found in the previous step are propagated to their neighbors. In GraphMatch, the sampling stage uses Fisher scores to search for matching image pairs more effectively. The propagation stage then uses the current graph of matched image pairs to explore the neighbors of images belonging to geometrically verified image pairs. GraphMatch is able to efficiently determine which image pairs are likely to produce a good match, avoiding unnecessary overhead in attempting to match image pairs which are unlikely to match.

In GraphMatch, two better priors are explored for edge-finding by studying metrics for global image similarity - VLAD and Fisher vectors. As the figure shows, Fisher scores give the best separation between the distributions of edges and non-edges, meaning they provide better guidance towards sampling edges - quite similar with query expansion - GraphMatch uses all the current, direct neighbors of a vertex (the set of vertices that currently share a valid edge with the vertex) as the potential neighborhood of candidates that will be tested for more matches. Then, GraphMatch identifies the candidate image pairs to test, ranks them besed on their Fisher distances, and selects a subset as the pairs to geometrically verify.


<div align=center> 

![vt](img/compare.png)

</div>

The *"propagation"* strategy in GraphMatch is that, if two vertices A and B are neighbors, the neighbors of B might also be neighbors for A and vice versa.

## References
[1] Nister D, Stewenius H. Scalable Recognition with a Vocabulary Tree[J]. Proc Cvpr, 2006, 2(10):2161-2168.

[2] Agarwal S , Snavely N , Simon I , et al. Building Rome in a day[C]// IEEE International Conference on Computer Vision. IEEE, 2009.

[3] Cui Q, Fragoso V, Sweeney C, et al. GraphMatch: Efficient Large-Scale Graph Construction for Structure from Motion[J]. 2017.