# Co-authorship Network Analysis

A Co-authorship network contains authors as nodes and collaboration on the same paper as edges. The analysis of these networks is often used to explore the development of a field and guide future collaboration. My project focuses on how authors specializing in different topics within a field are distributed. Do authors with the same interests collaborate the most? Is there polarization in the distribution of these authors?

To capture what topics each author focuses on I have used a deep learning Word2Vec model I trained on all abstracts from 1980 to 2022 in the Web of Science CORE dataset. During training this model creates word vectors based on the context each word appears in. These word vectors can be combined to capture more complex meaning and the cosine similairty between two word vectors gives a value from -1 to 1 depending on how similar these word vectors are. 

I can combine word vectors for all the text in the title and keywords for each paper by an author. This gives an author vector that represents all the work an author has contributed towards within a specifc field. When we measure the cosine similarity between the author vectors and the average of all author vectors we get the following network for the field of foldamers.

![foldamer_transparent_3](https://github.com/LarsSchimmelpfennig/CoauthorshipNetworkAnalysis/assets/91089724/a9de15e1-467f-4af3-b1b4-f44549a7b407)

This gives approximately a left-skewed normal distribution with the mean cosine similarity near .925. This is to be expected as few authors will be significantly different from the average author vector. This distribution was created from the Foldamer Co-authorship network. Other fields of research will have varying means and standard deviations depending on how varied the topics researched are.

<div align="center">
  <img src="https://github.com/LarsSchimmelpfennig/CoauthershipNetworkAnalysis/assets/91089724/ee188d0f-1de4-42d1-b8ce-3b8e2540a289" alt="Image" width="60%" />
</div>

For this analysis of these networks I will focus on the following metrics: Modularity is the proportion of edges from nodes in a cluster that connect to other nodes in the same cluster. Here clusters are assigned to maximize modularity. An increase in modularity represents an increase in edge density within a cluster. Next we will use the average distance between neighboring author vectors. This is computed using the value described in the histogram and will help tell us if authors with similar interests are working together. Lastly I have included homophily which measures the proportion of neighboring nodes with a score within some threshold. This is a metric for network polarization where high homophily values represent a higher degree of polarization. I have chosen to use the standard deviation of scores/2 as a threshold.

<div align="center">
  <img src="https://github.com/LarsSchimmelpfennig/CoauthershipNetworkAnalysis/assets/91089724/7d562964-7b32-47cd-94c9-6efe17c76035" alt="Image" width="60%" />
</div>

To measure the distance between these two curves they are first normalized so all values fall from 0-1. Next, Dynamic Time Warping (DTW) and Euclidean distances are recorded for each pair of curves. A permutation test with 10k iterations is used to quantify how likley the observed distance between curves is after combining and shuffling all values. From this I was able to measure a significance level between each curve. 

## Euclidean Distance Permutation Test P-values

| Literature Field                  | Modularity, Avg. Neighbors Distance | Modularity, Homophily | Avg. Neighbors Distance, Homophily |
|:---------------------------------:|:-----------------------------------:|:---------------------:|:----------------------------------:|
| Spliceosome                       | .135                                | .969                  | .001                               |
| Foldamer                          | .848                                | .1266                 | .001                               |
| Amelanotic Melanoma               | .001                                | .001                  | .028                               |
| Co-authorship Network Analysis    | .555                                | .124                  | .905                               |

With alpha=.05 there are some significant differences between these two sets of p-values. DTW seems to be more likely to incorrectly reject the null hypothesis that the observed distance between the curves is expected from randomness.

## DTW Distance Permutation Test P-values

| Literature Field                  | Modularity, Avg. Neighbors Distance | Modularity, Homophily | Avg. Neighbors Distance, Homophily |
|:---------------------------------:|:-----------------------------------:|:---------------------:|:----------------------------------:|
| Spliceosome                       | .001                                | .965                  | .001                               |
| Foldamer                          | .003                                | .001                  | .001                               |
| Amelanotic Melanoma               | .001                                | .001                  | .075                               |
| Co-authorship Network Analysis    | .019                                | .296                  | .992                               |

# Conclusion

It is very interesting that these different curves would share such similar features for some fields over time and not for others. For the second column, modularity and avg distance, as clusters of authors become more interconnected the distance between their author vectors increases. A significant p-value seems to suggest that dense clusters of authors are formed that are diversified. Collaborators may bring unique contributions without large overlap.


# Future Work

<li>Instead of generating these author vectors from the text in their publications I can instead train a new Word2Vec model with the authors of each paper directly. Authors that appear in the same papers will then have a higher cosine similarity. To attempt this, I would need to address Author Name Disambiguation (AND) which is a complex problem as many authors dont publish under a consistent spelling and many share the same name.</li>

<li>I can also use the text of all papers written by the authors in a network (instead of those just within a specifc field) when creating these author vectors, however, this would also require work on AND.</li> 
