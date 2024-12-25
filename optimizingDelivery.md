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

connection_nodes = 3, m = 3 connection_from = [0, 1, 0] connection_to = [1, 2, 2] connection_cost = [10, 20, 50] deliveries = [1, 2]

makefile
Copy code

**Output:**

60

markdown
Copy code

**Explanation:**

There are three junctions and three roads with associated travel times of 10, 20, and 50. Two orders need to be delivered at locations 1 and 2. The agent can travel along this path:  
`0 → 1 → 2 → 10`. This gives the minimum total travel time of `10 + 20 + 20 + 10 = 60`.

### Example 2

**Input:**

connection_nodes = 5, m = 6 connection_from = [0, 0, 4, 4, 1, 1] connection_to = [1, 4, 1, 3, 2, 3] connection_weight = [10, 3, 5, 4, 2, 4] deliveries = [1, 3]

makefile
Copy code

**Output:**

19

java
Copy code

**Explanation:**

Two orders need to be delivered at locations 1 and 3. The agent can travel along this path:  
`0 → 4 → 3 → 1 → 4 → 0`. This gives the minimum total travel time of `3 + 4 + 4 + 5 + 3 = 19`.

## Solution Code

```java
import java.io.*;
import java.math.*;
import java.security.*;
import java.text.*;
import java.util.*;
import java.util.concurrent.*;
import java.util.function.*;
import java.util.regex.*;
import static java.util.stream.Collectors.joining;
import static java.util.stream.Collectors.toList;

class Result {

    /* Complete the 'getMinimumTime' function below. */
    public static long getMinimumTime(int connectionNodes, List<Integer> connectionFrom, List<Integer> connectionTo, List<Integer> connectionWeight, List<Integer> deliveries) {
        // Create a graph
        Map<Integer, Map<Integer, Integer>> graph = new HashMap<>();
        
        // Construct the graph with edges and weights
        for (int i = 0; i < connectionFrom.size(); i++) {
            graph.putIfAbsent(connectionFrom.get(i), new HashMap<>());
            graph.putIfAbsent(connectionTo.get(i), new HashMap<>());
            graph.get(connectionFrom.get(i)).put(connectionTo.get(i), connectionWeight.get(i));
            graph.get(connectionTo.get(i)).put(connectionFrom.get(i), connectionWeight.get(i));
        }

        // Perform a shortest path algorithm like Dijkstra's from node 0 to all delivery locations
        Map<Integer, Integer> distFromStart = dijkstra(graph, 0, connectionNodes);
        List<Integer> allNodes = new ArrayList<>(deliveries);
        allNodes.add(0); // Include start node (0) for final return

        // Compute minimum time using permutations of deliveries
        long minTime = Long.MAX_VALUE;
        List<List<Integer>> permutations = generatePermutations(allNodes);
        for (List<Integer> perm : permutations) {
            long time = 0;
            // Calculate the travel time for the current permutation of deliveries
            for (int i = 0; i < perm.size() - 1; i++) {
                time += distFromStart.get(perm.get(i));
            }
            minTime = Math.min(minTime, time);
        }

        return minTime == Long.MAX_VALUE ? -1 : minTime;
    }

    // Helper function for Dijkstra's algorithm
    public static Map<Integer, Integer> dijkstra(Map<Integer, Map<Integer, Integer>> graph, int start, int n) {
        Map<Integer, Integer> dist = new HashMap<>();
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));
        pq.offer(new int[]{start, 0});
        dist.put(start, 0);

        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            int node = current[0], distance = current[1];
            
            if (distance > dist.getOrDefault(node, Integer.MAX_VALUE)) continue;

            for (Map.Entry<Integer, Integer> neighbor : graph.getOrDefault(node, Collections.emptyMap()).entrySet()) {
                int neighborNode = neighbor.getKey();
                int edgeWeight = neighbor.getValue();
                int newDist = distance + edgeWeight;

                if (newDist < dist.getOrDefault(neighborNode, Integer.MAX_VALUE)) {
                    dist.put(neighborNode, newDist);
                    pq.offer(new int[]{neighborNode, newDist});
                }
            }
        }
        return dist;
    }

    // Helper function to generate permutations
    public static List<List<Integer>> generatePermutations(List<Integer> list) {
        List<List<Integer>> result = new ArrayList<>();
        permuteHelper(list, 0, result);
        return result;
    }

    public static void permuteHelper(List<Integer> list, int index, List<List<Integer>> result) {
        if (index == list.size()) {
            result.add(new ArrayList<>(list));
            return;
        }
        for (int i = index; i < list.size(); i++) {
            Collections.swap(list, i, index);
            permuteHelper(list, index + 1, result);
            Collections.swap(list, i, index); // backtrack
        }
    }
}

public class Solution {

    public static void main(String[] args) throws IOException {
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(System.getenv("OUTPUT_PATH")));
        
        String[] connectionNodesEdges = bufferedReader.readLine().replaceAll("\\s+$", "").split(" ");
        int connectionNodes = Integer.parseInt(connectionNodesEdges[0]);
        int connectionEdges = Integer.parseInt(connectionNodesEdges[1]);
        
        List<Integer> connectionFrom = new ArrayList<>();
        List<Integer> connectionTo = new ArrayList<>();
        List<Integer> connectionWeight = new ArrayList<>();
        
        IntStream.range(0, connectionEdges).forEach(i -> {
            try {
                String[] connectionFromToWeight = bufferedReader.readLine().replaceAll("\\s+$", "").split(" ");
                connectionFrom.add(Integer.parseInt(connectionFromToWeight[0]));
                connectionTo.add(Integer.parseInt(connectionFromToWeight[1]));
                connectionWeight.add(Integer.parseInt(connectionFromToWeight[2]));
            } catch (IOException ex) {
                throw new RuntimeException(ex);
            }
        });
        
        int deliveriesCount = Integer.parseInt(bufferedReader.readLine().trim());
        List<Integer> deliveries = IntStream.range(0, deliveriesCount)
            .mapToObj(i -> {
                try {
                    return bufferedReader.readLine().replaceAll("\\s+$", "");
                } catch (IOException ex) {
                    throw new RuntimeException(ex);
                }
            })
            .map(String::trim)
            .map(Integer::parseInt)
            .collect(toList());
        
        long result = Result.getMinimumTime(connectionNodes, connectionFrom, connectionTo, connectionWeight, deliveries);
        bufferedWriter.write(String.valueOf(result));
        bufferedWriter.newLine();
        
        bufferedReader.close();
        bufferedWriter.close();
    }
}
Explanation:
Problem Understanding: We need to find the minimum time required for a delivery agent to deliver orders at multiple locations and return to the starting point, considering the city's road network as a graph.
Graph Representation: The problem is modeled as a graph, where nodes represent junctions, and edges represent roads with associated costs (travel time).
Solution Approach: The solution involves using Dijkstra's algorithm to calculate the shortest path from the start node to all other nodes. Then, the possible routes (permutations) are evaluated to find the one with the minimum travel time.
Code: This solution includes all the necessary code, such as graph construction, Dijkstra's algorithm, and permutation generation, within the Solution class.
