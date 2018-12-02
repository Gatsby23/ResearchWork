# Viewing Graph

Let's first define what is a **Viewing Graph(VG)**.

*Definition: A Viewing Graph is a graph which nodes represent images and edges represent the number of matches between two nodes.*

**Viewing Graph** is also called a **Match Graph**, which is first adopted by [2] - a milestone work of **Structure from Motion**.
As we know, **Feature Matching** is one of the main bottlenecks of 3D reconstruction. For sequential datasets, matching could performed only to its $k$
previous images. However, for unordered datasets, the exhaustive matching process takes lots of time. Thus, the structure of **Viewing Graph** should be explored
to reduce the number of matching. 

**Vocabulary Tree** [1] is often adopted to accelarate the matching process. In [2], vocabulary tree is used to measure the similarity of two images. For each image, the top scoring $k_1 + k_2$ images are identified, where the first $k_1$ images are used in an initial verification stage, and the additional $k_2$ images are used to enlarge the connected components. After the initial graph has been built, there may exist more than 1 connected components. For components of size 2 or more, the next $k_2$ images that suggested by the vocabulary tree are used to enlarge the graph. It should be noticed that, only image pairs that straddle two different connected components are considered. This step is called **components merging**. After component merging, the match graph is usually not dense enough to reliably produce a good reconstruction.

## References
[1] Nister D, Stewenius H. Scalable Recognition with a Vocabulary Tree[J]. Proc Cvpr, 2006, 2(10):2161-2168.

[2] Agarwal S , Snavely N , Simon I , et al. Building Rome in a day[C]// IEEE International Conference on Computer Vision. IEEE, 2009.