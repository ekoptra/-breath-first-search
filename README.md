# Maze Problem - Breath First Search

## Import Library


```python
import queue
```

## Maze Class


```python
class Maze:
    def __init__(self, maze):
        self.maze = maze
        self.ysize = len(maze) - 1
        self.xsize = len(maze[0]) - 1
    
    #  Methon untuk mengembalikan langkah yang mungkin dilakukan dari suatu posisi x dan y
    def getMove(self, x, y):
        move = []
        if x < 0 or x > self.xsize or y < 0 or y > self.ysize:
            return move
        
        if x - 1 >= 0 and self.maze[x-1][y] != '#':
            move.append('U')
        
        if x + 1 <= self.xsize and self.maze[x+1][y] != '#':
            move.append('D')
        
        if y - 1 >= 0 and self.maze[x][y-1] != '#':
            move.append('L')
        
        if y + 1 <= self.ysize and self.maze[x][y+1] != '#':
            move.append('R')
        return move
```

## Problem Class


```python
class Problem(object):
    def __init__(self, initial, goal):
        self.initial = initial
        self.goal = goal
    
    def goal_test(self, state):
        return state == self.goal
```

## Maze Problem Class


```python
class MazeProblem(Problem):
    def __init__(self, initial, goal, maze):
        Problem.__init__(self, initial, goal)
        self.maze = maze

    def path_cost(self, cost_so_far):
        return cost_so_far + 1
    
    def printMaze(self, solution=None):
        maze = self.maze.maze
        if solution:
            for step in solution:
                maze[step[0]][step[1]] = '+'                
                
        maze[self.initial[0]][self.initial[1]] = 'O'
        maze[self.goal[0]][self.goal[1]] = 'X'

        for row in maze:
            line = ""
            for col in row:
                line = line + col
            print(line)
```

## Node Class


```python
class Node:
    def __init__(self, state, parent=None, path_cost=0):
        self.state = state
        self.parent = parent
        self.path_cost = path_cost
        self.depth = 0
        if parent:
            self.depth = parent.depth + 1
            
    def __repr__(self):
        return "<Node ({}, {})>".format(self.state[0], self.state[1])
    
    def expand(self, problem):
        return [Node(self.move(next_state), self, self.path_cost + 1) 
                for next_state in problem.maze.getMove(self.state[0], self.state[1])]
    
    def move(self, movement):
        if movement == 'D':
            return [self.state[0] + 1, self.state[1]]
        if movement == 'U':
            return [self.state[0] - 1, self.state[1]]
        if movement == 'L':
            return [self.state[0], self.state[1] - 1]
        if movement == 'R':
            return [self.state[0], self.state[1] + 1]
    
    def path(self):
        node, path_back = self, []
        while node:
            path_back.append(node)
            node = node.parent
        return list(reversed(path_back))
    
    def solution(self):        
        return [node.state for node in self.path()]
```

## Breath First Search Function


```python
def breath_first_search(problem, verbose=False):
    q = queue.Queue()
    for node in Node(problem.initial).expand(problem):
        q.put(node)
    visited = [problem.initial]
    while not q.empty():
        next_node = q.get()
        if next_node.state not in visited:
            visited.append(next_node.state)
            if verbose:
                print(next_node)
            if problem.goal_test(next_node.state):
                return {'solution' : next_node.solution(), 'cost' : next_node.path_cost}
            for node in next_node.expand(problem):
                q.put(node)
    return 'Not Found'
```

## Buat Maze


```python
maze = []
maze.append(["#","#", "#", "#", "#", " ", "#", "#", "#"])
maze.append(["#"," ", " ", " ", " ", " ", " ", " ", "#"])
maze.append(["#"," ", "#", "#", "#", "#", "#", " ", "#"])
maze.append(["#"," ", " ", " ", "#", " ", "#", " ", "#"])
maze.append(["#","#", "#", " ", "#", " ", "#", " ", "#"])
maze.append(["#"," ", " ", " ", "#", " ", "#", "#", "#"])
maze.append(["#"," ", "#", "#", "#", " ", " ", " ", "#"])
maze.append(["#"," ", " ", " ", " ", " ", "#", " ", "#"])
maze.append(["#","#", "#", "#", "#", "#", "#", "X", "#"])
```

## Menyelesaikan Maze 


```python
maze = Maze(maze)
problem = MazeProblem([0, 5], [8, 7], maze)
problem.printMaze()
```

    #####O###
    #       #
    # ##### #
    #   # # #
    ### # # #
    #   # ###
    # ###   #
    #     # #
    #######X#
    


```python
result = breath_first_search(problem, verbose=True)
```

    <Node (1, 5)>
    <Node (1, 4)>
    <Node (1, 6)>
    <Node (1, 3)>
    <Node (1, 7)>
    <Node (1, 2)>
    <Node (2, 7)>
    <Node (1, 1)>
    <Node (3, 7)>
    <Node (2, 1)>
    <Node (4, 7)>
    <Node (3, 1)>
    <Node (3, 2)>
    <Node (3, 3)>
    <Node (4, 3)>
    <Node (5, 3)>
    <Node (5, 2)>
    <Node (5, 1)>
    <Node (6, 1)>
    <Node (7, 1)>
    <Node (7, 2)>
    <Node (7, 3)>
    <Node (7, 4)>
    <Node (7, 5)>
    <Node (6, 5)>
    <Node (5, 5)>
    <Node (6, 6)>
    <Node (4, 5)>
    <Node (6, 7)>
    <Node (3, 5)>
    <Node (7, 7)>
    <Node (8, 7)>
    


```python
result['cost']
```




    24




```python
problem.printMaze(result['solution'])
```

    #####O###
    #+++++  #
    #+##### #
    #+++# # #
    ###+# # #
    #+++# ###
    #+###+++#
    #+++++#+#
    #######X#
    
