# Optimizing Delivery - Food Delivery Application

## Problem Description

A food delivery application seeks to incorporate a new feature that enables delivery agents to carry out multiple orders simultaneously, resulting in faster deliveries and reduced waiting times. The city's road network is represented as a graph comprising of connection nodes (junctions) and bidirectional roads that interconnect them. Each road, which links cities connection_from[i] and connection_to[i], has an associated connection_cost[i] that represents the time required to travel along the road.

There are `k` distinct orders to be fulfilled, each at a different location. The delivery agent begins at junction 0, completes the orders at all `k` locations in any sequence, and returns to junction 0. Determine the minimum time required to accomplish this task.

### Input

1. `connection_nodes`: Number of junctions.
2. `m`: Number of roads.
3. Three lists that describe the roads:
   - `connection_from[i]`: Starting junction of the `i-th` road.
   - `connection_to[i]`: Ending junction of the `i-th` road.
   - `connection_cost[i]`: Cost (time) of traveling along the `i-th` road.
4. `k`: The number of delivery locations.
5. `deliveries`: List of `k` locations where the orders need to be delivered.

### Output

Return the minimum time required to complete all the deliveries and return to junction 0. If it is not possible, return `-1`.

### Constraints

- `1 <= n <= 10^5`
- `1 ≤ m ≤ min(10^5, n^ * (n - 1) / 2)`
- `1 ≤ k ≤ 10`
- `1 ≤ connection_weight[i] ≤ 10^9`
- `0 ≤ connection_from[i] < n`
- `0 ≤ connection_to[i] < n`
- `0 < deliveries[i] < n`

### Example 1

**Input:**

connection_nodes = 3, m = 3 connection_from = [0, 1, 10] connection_to = [1, 2, 0] connection_cost = [10, 30, 10] deliveries = [1, 2]



**Output:**

40

Explanation:
Problem Understanding: We need to find the minimum time required for a delivery agent to deliver orders at multiple locations and return to the starting point, considering the city's road network as a graph.
Graph Representation: The problem is modeled as a graph, where nodes represent junctions, and edges represent roads with associated costs (travel time).
Solution Approach: The solution involves using Dijkstra's algorithm to calculate the shortest path from the start node to all other nodes. Then, the possible routes (permutations) are evaluated to find the one with the minimum travel time.
Code: This solution includes all the necessary code, such as graph construction, Dijkstra's algorithm, and permutation generation, within the Solution class.




