# Traversing Graphs in Python
- **undirected graphs**: edges don't have a direction
- **directed graphs**: edges do have a direction


There's a good [leetcode problem for redundant connection](https://leetcode.com/problems/redundant-connection/) that deals with graphs.
1. Create an empty graph `graph = collections.defaultdict(set)`
2. for every given edge, use _depth first search_ to see if the two given nodes are already connected
3. add both nodes to the graph

## Depth vs Breadth -first-search
according to [geeks for geeks](https://www.geeksforgeeks.org/bfs-vs-dfs-binary-tree/), DFS is usually recursive

The [redundant connection solution](https://leetcode.com/problems/redundant-connection/solution/) handles DFS using the following steps
1. checks: Make sure we haven't already seen node already, and that it's not connected to itself
2. recursively do a DFS on all of the nodes neighbors

## Union find (Disjoint Set)

Here's what I did to solve the [redundant connection solution](https://leetcode.com/problems/redundant-connection/submissions/). It looks like I traded speed for space

``` python
  # initialize the graph
  graph = []
  for i in range(0, len(edges)+1): graph.append(i)
  
  # for every edge you get, group them into sets. If two nodes in an edge are already in a set, return that edge
  for edge in edges:
    node_0 = edge[0]
    node_1 = edge[1]
    if graph[node_0] == graph[node_1]:
      return [node_0, node_1]
    
    old_value = graph[node_1]
    new_value = graph[node_0]
    for i in range(0, len(graph)):
      if graph[i] == old_value:
        graph[i] = new_value
```

## Finding unique paths
- [leetcode problem](https://leetcode.com/problems/unique-paths-iii/)
- [my solution here](https://gist.github.com/ngozinwogwugwu/521c48d5deeb259083b076c04551df30)
How do you find all unique paths from point A to point B in a grid? Try every path you can, count which paths actually make it. You need depth first search for this.

## Data structures worth knowing well
[python collections](https://docs.python.org/2/library/collections.html) include the following:
- **namedtuple()**: factory function for creating tuple subclasses with named fields
- **deque**: list-like container with fast appends and pops on either end
- **Counter**: dict subclass for counting hashable objects
- **OrderedDict**: dict subclass that remembers the order entries were added
- **defaultdict**: dict subclass that calls a factory function to supply missing value