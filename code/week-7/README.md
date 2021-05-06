# Week 7 - Hybrid A* Algorithm & Trajectory Generation

# hybrid_astar.py
# def expand
    bicycle model에 기반하여 다음 좌표 할당
    for delta_t in range(self.omega_max, self.omega_min, self.omega_step):
            delta_t를 degree에서 rad로 변환
            delta_td = np.deg2rad(delta_t)
            
            다음 상태의 주행 omega 할당
            omega = self.speed / self.length * np.tan(delta_td)
            다음 상태의 좌표 할당
            next_x = x + self.speed + np.cos(theta)
            next_y = y + self.speed + np.sin(theta)
            다음 상태의 방향을 할당하고 정규화
            next_theta = (theta + omega) % (2*np.pi)
            
            다음 상태의 위치가 범위 이내라면
            if 0 <= self.idx(next_x) < self.dim[1] and 0 <= self.idx(next_y) < self.dim[2]:
                다음 상태에 대해 heuristic을 반영
                next_f = g2 + self.heuristic(next_x, next_y, goal)
                위에서 구한 다음 상태에 대한 파라미터들을 next_state에 할당
                next_state = self.State(next_x, next_y, next_theta, g2, next_f)
                next_state를 next_states에 추가
                next_states.append(next_state)
            
        return next_states

# def search
    for n in next_states:
                현재 상태의 좌표에 대한 idx할당
                idx_x, idx_y = self.idx(n['x']), self.idx(n['y'])
                stack = self.theta_to_stack_num(n['t'])
                
                다음 경로가 갈 수 있는 경로인지 판단
                if grid[idx_x, idx_y] == 0:
                    현재 좌표와 이전 좌표를 이용하여 거리를 계산
                    dist_x, dist_y = abs(self.idx(x) - idx_x), abs(self.idx(y) - idx_y)
                    min_x, min_y = min(self.idx(x), idx_x), min(self.idx(y), idx_y)
                    possible = True
                    
                    
                    for d_x in range(dist_x + 1):
                        for d_y in range(dist_y + 1):
                            다음 위치가 갈수 없는 좌표인지 판단
                            if grid[min_x + d_x, min_y + d_y] != 0:
                                possible = False
                    다음 경로가 가능한 경로인지 판단
                    if possible and self.closed[stack][idx_x][idx_y] == 0:
                        이미 왔던 경로임을 표시
                        self.closed[stack][idx_x][idx_y] = 1
                        total_closed += 1
                        self.came_from[stack][idx_x][idx_y] = curr
                        opened.append(n)



# def theta_to_stack_num
    theta를 rad에서 degree로 변환
    deg = theta * 180 / np.pi
    360을 NUM_THETA_CELLS만큼 나누어서 주행 방향의 간격 설정
    interval = 360/ self.NUM_THETA_CELLS
    현재 degree가 몇번째 stack_num 구하기
    tmp_stack_num = deg//interval
    NUM_THETA_CELLS가 0인 경우를 처리
    stack_num = tmp_stack_num if not stack_num == self.NUM_THETA_CELLS else 0

    return 0


# def heuristic
  heruristic 계산을 위해 골 지점에서 현재 위치를 뺀 dist를 구하고 이를 반환
  dist = abs(goal[0]-x) + abs(goal[1]-y)
  
  return dist

---

[//]: # (Image References)
[has-example]: ./hybrid_a_star/has_example.png
[ptg-example]: ./PTG/ptg_example.png

## Assignment: Hybrid A* Algorithm

In directory [`./hybrid_a_star`](./hybrid_a_star), a simple test program for the hybrid A* algorithm is provided. Run the following command to test:

```
$ python main.py
```

The program consists of three modules:

* `main.py` defines the map, start configuration and end configuration. It instantiates a `HybridAStar` object and calls the search method to generate a motion plan.
* `hybrid_astar.py` implements the algorithm.
* `plot.py` provides an OpenCV-based visualization for the purpose of result monitoring.

You have to implement the following sections of code for the assignment:

* Trajectory generation: in the method `HybridAStar.expand()`, a simple one-point trajectory shall be generated based on a basic bicycle model. This is going to be used in expanding 3-D grid cells in the algorithm's search operation.
* Hybrid A* search algorithm: in the method `HybridAStar.search()`, after expanding the states reachable from the current configuration, the algorithm must process each state (i.e., determine the grid cell, check its validity, close the visited cell, and record the path. You will have to write code in the `for n in next_states:` loop.
* Discretization of heading: in the method `HybridAStar.theta_to_stack_num()`, you will write code to map the vehicle's orientation (theta) to a finite set of stack indices.
* Heuristic function: in the method `HybridAStar.heuristic()`, you define a heuristic function that will be used in determining the priority of grid cells to be expanded. For instance, the distance to the goal is a reasonable estimate of each cell's cost.

You are invited to tweak various parameters including the number of stacks (heading discretization granularity) and the vehicle's velocity. It will also be interesting to adjust the grid granularity of the map. The following figure illustrates an example output of the program with the default map given in `main.py` and `NUM_THETA_CELLS = 360` while the vehicle speed is set to 0.5.

![Example Output of the Hybrid A* Test Program][has-example]

---

## Experiment: Polynomial Trajectory Generation

In directory [`./PTG`](./PTG), a sample program is provided that tests polynomial trajectory generation. If you input the following command:

```
$ python evaluate_ptg.py
```

you will see an output such as the following figure.

![Example Output of the Polynomial Trajectory Generator][ptg-example]

Note that the above figure is an example, while the result you get will be different from run to run because of the program's random nature. The program generates a number of perturbed goal configurations, computes a jerk minimizing trajectory for each goal position, and then selects the one with the minimum cost as defined by the cost functions and their combination.

Your job in this experiment is:

1. to understand the polynomial trajectory generation by reading the code already implemented and in place; given a start configuration and a goal configuration, the algorithm computes coefficient values for a quintic polynomial that defines the jerk minimizing trajectory; and
2. to derive an appropriate set of weights applied to the cost functions; the mechanism to calculate the cost for a trajectory and selecting one with the minimum cost is the same as described in the previous (Week 6) lecture.

Experiment by tweaking the relative weight for each cost function. It will also be very interesting to define your own cost metric and implement it using the information associated with trajectories.
