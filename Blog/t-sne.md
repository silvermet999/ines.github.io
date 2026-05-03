---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-22T17:53:58Z"
id: bafyreigj4cibirf3z7uq6ydaribpvymlba6pjrtjd4p7rgjq46uk6oxzli
---
# t-SNE   
## Definition   
- t-distributed Stockastic Neighbor Embedding: Viz high dim data   
- The goal is to take a set of pnts in a high dim space to find a faithful rep of those pnts in a lower dim space (typically 2D planes)   
- The algo is non-linear & adapt to the underlying data, performing diff transformations on diff regions   
   
## Param: Perplexity   
- Tunable param "**perplexity**" is a feature of t-SNE → how to balance attention btw local & global aspects of data   
- A guess about # close neighbors each pnt has => the goal is to analyze multiple plots with diff perplexities   
   
## Scenarios   
- With perplexity [5, 50], the diagrams show these clusters with very diff shapes   
- With perplexity =2, local variations dominate   
- With perplexity =100 and merged clusters, illustrate a pitfall   
- Perplexity =30, steps = 10, 20, 60 & 120, layouts with 1D & pnt-like img of the cluster   
- If plot is pinched-shaped, the process was stopped too early   
- Multiple runs from same ds give same global shape & same hyperparams produce same results   
- In case of diff std & bounding box measurements, two clusters look about the same: notion of distance to regional density variantions → expands dense clusters & contract sparse ones, evening out cluster size (in case of 2D)   
- If we add more pnts to each cluster, the perplexity has to increase to compensate → no one perplexity val that will capture distances across all clusters => distances btw well separated clusters might not mean anything   
- Random noise doesn't always look random: perplexity = 2 seem to show dramatic clusters (if you were tuning perplexity to bring out the structure, it might look good) → since the cloud of pnts was gen randomly, it has no stat interesting clusters => how perplexity often lead to this dist   
- Perplexity = 30 doesn't look like a Gaussian, only a slight density diff across diff region & pnts are seemly evenly distributed → features saying useful things about high-dim normal dist, close to uniform on a sphere: evenly dist with roughly equal spaces btw pnts => accuracy of t-SNE compared to linear projection   
- Looking for long-ish ellipsoidal cloud of pnts where the std in coordinate i is 1/i   
- For high enough perplexity vals, the elongated shapes are easy to read   
- For low perplexity, local effects and meaningless clumping, sometimes extreme shapes   
- In the best cases, there's a subtle distortion: the lines are slightly curved outwards in the t-SNE diagram → t-SNE tends to expand dense regions of data. Since the middles of the clusters have less empty spaces, the algo magnifies them   
- Topology can be read: property is containment   
- In a plot of 2 groups of 75 pnts in 50 dim space, sampled from symmetric gaussian dist, centered at the origin, one is 50 times more tightly dispersed → the small dist is in effect contained in the large one   
- Perplexity = 30 shows basic topology but t-SNE exaggerates the size of smaller group pnts   
- Perplexity = 50, the outer group becomes a circle (all pnts are about the same distance from inner group)   
- Consider a set of pnts that trace a link or a knot in 3D. Low perplexity give two loops; high ones show a global connectivity   
- The t-SNE on trefoil knot settles twice on a circle, preserving the intrinsic topology, but other runs introduce artificial breaks   
- At perplexity = 50 gives results that (up to symmetry) are visually identical    
   
   
*Credits: [https://distill.pub/2016/misread-tsne/#perplexity=10&epsilon=5&demo=3&demoParams=50,2,5](https://distill.pub/2016/misread-tsne/#perplexity=10&epsilon=5&demo=3&demoParams=50,2,5) *   
