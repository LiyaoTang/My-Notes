# Data Structure

## Spatial Data

### $K$-D Tree<

- Motivation

  - spatial data storage \& fast access
    - NN-search
    - range queries: radius neighborhood
    - fast look-up

- Definition

  - a binary search tree, where the search key used at each level corresponds to a different dimension

    (in a circulating fashion) 

- Construction

  - if there is only one point, form a leaf
  - otherwise, divide the points in half,  using a chosen axis
  - recursively divide into 2 sets of points, until all points are leaves

- Division Strategy

  - divide points perpendicular to the axis with widest spread
  - use a round-robin fashion

- Complexity

  - Depth: guaranteed to $\le \log n$ , where $n$ the number of $k$-dim point
  - Construction
    - $\mathcal O(n)$ to find the mid-value of an axis (same algorithm as $k$-th biggest num)
    - divide points into $2$ sets $\Rightarrow$ at most $\log n$ times division
    - $\Rightarrow \mathcal O(n\log n)$ time with $\mathcal O(n)$ space (select axis by round-robin); $\mathcal O(dn)$ - select the widest axis in current set
  - Storage
    - each node may contain: axis, criteria (division number), left-\&-right subtree, point (if being a leaf)
    - same as binary tree: $\mathcal O(n + \frac 12 n + ... + \frac 1{2^{\log_2n}})=\mathcal O(n)$ 
  - 

- NN-Search

  - Procedure

    - start from the nearest point inside the cell of given center location

    - recursively step back to root $\Rightarrow$ check if need to search the neighbor sub-tree (at each level)

      i.e. check if current radius $r$ can cross the division number (on its axis)

    - if yes, go into the subtree \& search the qualified cells

  - Pro

    - avoid distance evaluation by comparing with the division / split number (criteria)

      (pruning by heuristic)

  - Complexity

    - worst case $\mathcal O(n)$; average $\mathcal O(\log n)$