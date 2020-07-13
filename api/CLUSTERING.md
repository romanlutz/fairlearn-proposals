# Fair Clustering

Fair clustering is an important unsupervised learning task related to ML
fairness that Fairlearn does not cover so far.
This proposal aims to show how it could be integrated into the package and
the educational materials.

## Definitions

### Metric space

### Clustering

`k`-clustering problems try to assign `n` points to `k` disjoint clusters.

[`k`-means clustering](https://en.wikipedia.org/wiki/K-means_clustering)
is about assigning each point to the closest cluster in terms of the mean of
its members (cluster centroid). The problem is NP-hard, but efficient
heuristics exist. The cluster centroid need not be one of the input points.

[`k`-medoid clustering](https://en.wikipedia.org/wiki/K-medoids) is similar
to `k`-means clustering, except that the centroid needs to be one of the input
points.

[`k`-median clustering](https://en.wikipedia.org/wiki/K-medians_clustering)
is about assigning each point to the closest cluster in terms of the median
of its members (cluster centroid). The problem is NP-hard.

[`k`-center clustering](http://personal.psu.edu/jol2/course/stat597e/notes2/kcenter.pdf)
is about assigning each point to a cluster so that the maximum distance
between points within the cluster is minimized. The centroid may not be the
mean or median of the cluster points. The problem is NP-hard.

**Balance** of a cluster w.r.t. colors is defined as the `min |A|/|B|` for any
two colors A, B. These colors map directly into sensitive features in the
fairness literature. For example, the coloring may be done based on genders,
age groups, races, etc.
If we have a cluster with 100 people, of which 40 are male, 50 are female, and
10 are non-binary, then the balance of that cluster is `10 / 50 = 0.2`.
This definition is taken and generalized from [1] to work for more than two
colors/groups. The balance is always between `0` and `1` with `1` being a
perfect balance and `0` representing the absence of a color.

`(t, k)`-fair [means/mediod/median/center] clustering is defined as
`k`-[means/mediod/median/center] clustering where the minimum balance for each
cluster needs to be at least `t`. [1] calls all balanced clusters fairlets,
and the overall clustering solution a `(t, k)`-fairlet decomposition of `X`.

[14] similarly defines **minority protection** as a minimum fraction for each
group that needs to be present in each cluster. Additionally, they define
**restricted dominance** through a maximum fraction for each group on all
clusters. [14] does not require disjoint groups.

In contrast, [3] makes users specify how many of the `k` centers should be
from each group. In other words, they define fairness through
**representation in the set of centers**, whereas the balance-based approach
from above considers representation within the set of points in a cluster.

[8] approaches fair clustering differently by challenging the assumption of
balanced clusters as a desirable outcome. Instead, they propose
**minimizing the maximum average cost of any group**. Cost can be defined as

- absolute representation error (distance of a point to nearest centroid)
  which would be similar to demographic parity, i.e., the cluster centroids
  have to work similarly well for all groups
- relative representation error looks at the distances of points to the
  nearest centroid compared to the optimal value for a group; this assumes
  different distributions between the groups that would not work well with
  absolute representation error; inspired by equality of opportunity

[5] follows the same approach with a focus on `k`-means on Lloyd's algorithm.

## Applications

1. **Preprocessing in supervised learning tasks**

2. **Data summarization**
   The goal in data summarization is to return a small but representative
   subset of the data that matches a particular keyword. [3]
   If we return the members of a cluster as search results for certain terms
   then balance within the clusters matters for representation. For example,
   we might want a search for the term "CEO" to return a minimum percentage
   of people from all genders, or equal representation between races to avoid
   harms of under- and overrepresentation. The point is to show that a
   particular gender or race are not definitional to the role of a CEO.
   According to [3] this form of data summarization is common in text
   summarization and robotics.

3. **Replacing data points by cluster centroid**

4. **Predictive policing**
   This is mentioned by [5] but there's plenty of research into this topic
   suggesting that it's at the very least a questionable use case of AI.
   We should definitely examine this more in detail and provide a
   summary to users as to why we're staying away from this application.
   Some more background from [NYU Law Review](https://www.nyulawreview.org/online-features/dirty-data-bad-predictions-how-civil-rights-violations-impact-police-data-predictive-policing-systems-and-justice/),
   [Upturn](https://www.upturn.org/reports/2016/stuck-in-a-pattern/)

5. **Market segmentation**
   The goal in market segmentation is to partition customers into clusters
   where the services or advertisements provided to them are tailored to that
   specific cluster.
   

### Datasets

1. https://archive.ics.uci.edu/ml/datasets/diabetes with gender as the
   sensitive feature; the goal is to create clusters that are balanced w.r.t.
   gender; used by [1]
2. https://archive.ics.uci.edu/ml/datasets/Bank+Marketing with married/not
   married as the sensitive feature; the goal is to create clusters that are
   balanced w.r.t. married/not married; used by [1], [8], [14]
3. https://archive.ics.uci.edu/ml/datasets/adult with gender as the sensitive
   feature; the goal is to create clusters that are balanced w.r.t. gender;
   used by [1], [8], [3], [5], [14];
   [3], [5] and [14] also used race as a sensitive feature
4. https://archive.ics.uci.edu/ml/datasets/iris with setosa/versicolor labels
   as sensitive feature; used by [8]
5. https://www.kaggle.com/jessicali9530/lfw-dataset
   The labeled faces in the wild dataset is provided by UMass Amherst with
   lots of [disclaimers](http://vis-www.cs.umass.edu/lfw/) worth considering.
   [5] uses this with male/female as the sensitive feature.
6. http://archive.ics.uci.edu/ml/datasets/default+of+credit+card+clients
   with eduction binned into higher/lower educated as sensitive feature
   used by [5]; [14] doesn't bin education (leaving it separate) and
   additionally groups by marriage status
7. https://archive.ics.uci.edu/ml/datasets/US+Census+Data+%281990%29 with
   age and sex as sensitive features; used by [14]

## API

Actual Python code is available for a few of the papers in the References
section.

The fair `k`-center code is available on [GitHub](https://github.com/matthklein/fair_k_center_clustering).
It relies on the number of centers per group as well as a set of prespecified
centers to include regardless (which may be an empty set).

## References

1. Fairlets https://paperswithcode.com/paper/fair-clustering-through-fairlets
   Introduces the concept of fairlets and the reduction from fair clustering
   to vanilla clustering.
2. Matroids, matching, and fairness https://storage.googleapis.com/pub-tools-public-publication-data/pdf/9ff7e51fc04da84086786eb1a6dc672604260e96.pdf
3. Fair K-center clustering https://paperswithcode.com/paper/fair-k-center-clustering-for-data
4. Fair Spectral clustering https://paperswithcode.com/paper/guarantees-for-spectral-clustering-with
5. Fair K-means clustering https://paperswithcode.com/paper/fair-k-means-clustering
6. Variational fair clustering https://paperswithcode.com/paper/clustering-with-fairness-constraints-a
7. Probabilistic fair clustering https://arxiv.org/pdf/2006.10916.pdf
8. Group-representative clustering https://arxiv.org/pdf/2006.11009.pdf
9. Individually fair clustering https://arxiv.org/pdf/2006.04960.pdf
10. Hierarchical clustering https://arxiv.org/pdf/2006.10221.pdf
11. Fair correlation clustering https://paperswithcode.com/paper/fair-correlation-clustering
12. Coresets for clustering with fairness constraints https://paperswithcode.com/paper/coresets-for-clustering-with-fairness-1
13. distributional individual fairness in clustering https://paperswithcode.com/paper/distributional-individual-fairness-in
14. Fair algorithms for clustering https://arxiv.org/pdf/1901.02393.pdf