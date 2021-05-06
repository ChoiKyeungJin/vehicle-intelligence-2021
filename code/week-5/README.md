# Week 5 - Path Planning & the A* Algorithm
# optimum_policy_2D 구현
목적지에 도착하면 탐색을 마치기위해 value를 0으로 하고 policy값을 -999로 만든다.
if (y, x) == goal and value[(t, y, x)] > 0: 
                # TODO: implement code.
                change = True
                value[(t,y,x)] = 0
                policy[(t,y,x)] = -999
------------------------------------------
다음 탐색지역이 이동 가능한 지역인지 확인
elif grid[(y, x)] == 0:
               # TODO: implement code.
	       액션을 한번씩 시행하면서 움직여 본다
               for act in action:
                   d1 = (t + act)%4
                   y1 = y + forward[d1][0]
                   x1 = x + forward[d1][1]
		   
	탐색 지역이 갈수 있는 grid맵 이내의 범위인지와 갈수 있는 곳인지 확인한다.
if 0<= y1 <grid.shape[0] and 0<= x1 < grid.shape[1] and grid[(y1,x1)] == 0:
v1에 갱신된 밸류를 할당하고 이전의 value보다 코스트가 더 적으면 더 좋은 경로이므로 밸류와 액션을 갱신한다.
                       v1 = value[(d1, y1, x1)] + cost[act+1]
                       if v1 < value[(t, y, x)]:
                           change = True
                           value[(t, y, x)] = v1
                           policy[(t, y, x)] = act
---------------------------------------------------------
시작지점 초기화
y, x, o = init

    policy_start에 시작점 할당
    policy_start = policy[(o,y,x)]
    
    for i in range(len(action)):
    	
        if policy_start == action[i]:
	start policy에 대한 액션을 확인하고 액션 할당
            policy_start_name = action_name[i]
        
    policy2D[(y,x)] = policy_start_name
    goal지점이 아니면 while문을 계속 반복하여 탐색 
    while policy[(o, y, x)] != -999:
    액션에 따라 방향 할당
        if policy[(o, y, x)] == action[0]:
            o1 = (o - 1) % 4  # turn left
        elif policy[(o, y, x)] == action[1]:
            o1 = o  # go straight
        elif policy[(o, y, x)] == action[2]:
            o1 = (o + 1) % 4  # turn right
        
	방향에 맞게 좌표 이동하고 방향 할당   
        y = y + forward[o1][0]
        x = x + forward[o1][1]
        o = o1
	
	이동한 좌표에 대한 policy를 할당한다.
        temp_policy = policy[(o,y,x)]
	
	골지점이면 맵에 *로 표시한다.
        if temp_policy == -999:
            policy_name = "*"
	    
	골지점이 아니면 그에 맞는 방향을 맵에 표시한다.
        else:
            for i in range(len(action)):
                if temp_policy == action[i]:
                    policy_name = action_name[i]
        policy2D[(y,x)] = policy_name
---

## Examples

We have four small working examples for demonstration of basic path planning algorithms:

* `search.py`: simple shortest path search algorithm based on BFS (breadth first search) - only calculating the cost.
* `path.py`: built on top of the above, generating an optimum (in terms of action steps) path plan.
* `astar.py`: basic implementation of the A* algorithm that employs a heuristic function in expanding the search.
* `policy.py`: computation of the shortest path based on a dynamic programming technique.

These sample source can be run as they are. Explanation and test results are given in the lecture notes.

## Assignment

You will complete the implementation of a simple path planning algorithm based on the dynamic programming technique demonstrated in `policy.py`. A template code is given by `assignment.py`.

The assignmemt extends `policy.py` in two aspects:

* State space: since we now consider not only the position of the vehicle but also its orientation, the state is now represented in 3D instead of 2D.
* Cost function: we define different cost for different actions. They are:
	- Turn right and move forward
	- Move forward
	- Turn left and move forward

This example is intended to illustrate the algorithm's capability of generating an alternative (detouring) path when left turns are penalized by a higher cost than the other two possible actions. When run with the settings defined in `assignment.py` without modification, a successful implementation shall generate the following output,

```
[[' ', ' ', ' ', 'R', '#', 'R'],
 [' ', ' ', ' ', '#', ' ', '#'],
 ['*', '#', '#', '#', '#', 'R'],
 [' ', ' ', ' ', '#', ' ', ' '],
 [' ', ' ', ' ', '#', ' ', ' ']]
```

because of the prohibitively high cost associated with a left turn.

You are highly encouraged to experiment with different (more complex) maps and different cost settings for each type of action.
