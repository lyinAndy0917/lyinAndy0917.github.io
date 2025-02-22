---
title: "Path Finding"
description: "An introduction to path finding algorithms."
slug: "Path Finding"
---

# Path Finding

## A* Algorithm
-  A star is a heuristic search algorithm that finds the shortest path between the start node and the end node.
-  **What is a heuristic?** A heuristic is like a rule of thumb. It could be a guess, an estimate, or a rule that helps you find a solution.

### An Example
  - Let's say you are a mouse in a maze. The heuristic would be how strong the smell of cheese is. The stronger the smell, the closer you are to the cheese. The heuristic is the smell of the cheese.
  - So that the P would be determined by the distance and the smell of the cheese. The distance is the cost of the path, and the smell of the cheese is the heuristic.
  - So each node would have a heuristic value and between the node are the cost of the path.
  - The A* algorithm would find the path with the minimum cost.


### Code Example
First we need to define some helper functions to calculate the heuristic and the cost of the path.
1. We would start by defining the node class.  
   **What is a node in A star algorithm?** A node is a point in the grid. It has a position, a parent, and a cost.
```python
class Node(object):
    def __init__(self, pose):
        self.pose = np.array(pose)
        self.x = pose[0]
        self.y = pose[1]
        self.g_value = 0 # cost from start node to current node
        self.h_value = 0 # heuristic value
        self.f_value = 0 # f = g + h 
        self.parent = None

    def __lt__(self, other): # use to compare two nodes: less than
        return self.f_value < other.f_value

    def __eq__(self, other): # use to compare two nodes: equal
        return (self.pose == other.pose).all()
```

2. Now we could start to craft the A* class.
```python
class AStar(object):
    def __init__(self, map_path):
        self.map_path = map_path
        self.map = self.load_map(self.map_path).astype(int)
        print(self.map)
        self.resolution = 0.05
        self.y_dim = self.map.shape[0]
        self.x_dim =self.map.shape[1]
        print(f'map size ({self.x_dim}, {self.y_dim})')

    def load_map(self, path):
        return np.load(path)

    def reset_map(self):
        self.map = self.load_map(self.map_path)
```
The `load_map` function is used to load the map from the file. The `reset_map` function is used to reset the map to the original state. why do we need to reset the map? Because we need to find the path multiple times, and we need to reset the map to the original state every time we find a new path.

3. Now we would define the important parts of the euclidean distance and the cost of the path.
```python
class AStar(object):
    '''
    '''
    def euclidean_distance(self, start, end):
        return np.linalg.norm(start.pose - end.pose) # this is euclidean distance therefore it's the straight line distance

    def get_successor(self, node):
        """
        :param node: A Node data structure
        :return: a list of Nodes containing successors of current Node
        """
        successor_list = []
        x,y = node.pose  # Get x, y coordinates of the current node
        pose_list = [[x+1, y+1], [x, y+1], [x-1, y+1], [x-1, y],
                        [x-1, y-1], [x, y-1], [x+1, y-1], [x+1, y]]  # Pose list contains 8 neighbors of the current node

        for pose_ in pose_list:
            x_, y_ = pose_
            if 0 <= x_ < self.y_dim and 0 <= y_ < self.x_dim and self.map[x_, y_] == 0: # Eliminate nodes that are out of bound, and nodes that are obstacles if the value is 0 then it's not an obstacle
                self.map[x_, y_] = -1
                successor_list.append(Node(pose_))
        
        return successor_list
```
4. Now we would define the calculate the path function.
```python
class AStar(object):
    '''
    '''
    def calculate_path(self, node):
        """
        :param node: A Node data structure
        :return: a list with shape (n, 2) containing n path point
        """
        path_ind = []
        path_ind.append(node.pose.tolist())
        current = node
        while current.parent:
            current = current.parent
            path_ind.append(current.pose.tolist())
        path_ind.reverse()
        print(f'path length {len(path_ind)}') # the number of nodes in the path
        path = list(path_ind)
        return path
```
It would return the path in the form of a list of nodes.

5. We would define the plan function.
```python
class AStar(object):
    '''
    '''
   def plan(self, start_ind, goal_ind):
        """
        TODO:
        Fill in the missing lines in the plan function
        @param start_ind : [x, y] represents coordinates in webots world
        @param goal_ind : [x, y] represents coordinates in webots world
        @return path : a list with shape (n, 2) containing n path point
        """
       
        # initialize start node and goal node class
        start_node = Node(start_ind)
        goal_node = Node(goal_ind)
        """
        TODO:
        calculate h and f value of start_node
        (1) h can be computed by calling the heuristic method
        (2) f = g + h
        """
        # Calculate initial h and f values for start_node
        start_node.h_value = self.heuristic(start_node, goal_node)
        start_node.f_value = start_node.g_value + start_node.h_value

        # Reset map and initialize open and closed lists
        self.reset_map()
        open_list = []
        closed_list = []
        heappush(open_list, start_node)

        # Reset map
        self.reset_map()

        # Initially, only the start node is known.
        # This is usually implemented as a min-heap or priority queue rather than a hash-set.
        # Please refer to https://docs.python.org/3/library/heapq.html for more details about heap data structure
        open_list = []
        closed_list = np.array([])
        heappush(open_list, start_node)

        # while open_list is not empty
        while len(open_list):
            
            """
            TODO:
            get the current node and add it to the closed list
            """
            # Current is the node in open_list that has the lowest f value
            # This operation can occur in O(1) time if open_list is a min-heap or a priority queue
            
            # Get the node with the lowest f value from open_list
            current = heappop(open_list)
            closed_list = np.append(closed_list, current)

            self.map[current.x, current.y] = -1

            # if current is goal_node: calculate the path by passing through the current node
            # exit the loop by returning the path
            if current == goal_node:
                print('reach goal')
                return self.calculate_path(current)
            
            for successor in self.get_successor(current):
                """
                TODO:
                1. pass current node as parent of successor node
                2. calculate g, h, and f value of successor node
                    (1) d(current, successor) is the weight of the edge from current to successor
                    (2) g(successor) = g(current) + d(current, successor)
                    (3) h(successor) can be computed by calling the heuristic method
                    (4) f(successor) = g(successor) + h(successor)
                """
                # Set current node as the parent of the successor
                successor.parent = current

                # Calculate g, h, and f values for the successor
                distance = np.linalg.norm(successor.pose - current.pose)
                successor.g_value = current.g_value + distance
                successor.h_value = self.heuristic(successor, goal_node)
                successor.f_value = successor.g_value + successor.h_value

                heappush(open_list, successor)

        # If the loop is exited without return any path
        # Path is not found
        print('path not found')
        return None
```



