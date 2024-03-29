# Convex Feasible Set Algorithm
This is an implementation of the Convex Feasible Set Algorithm for Real Time Optimization in Motion Planning (Changliu et al. 2018) ([https://arxiv.org/abs/1709.00627](https://arxiv.org/abs/1709.00627)). My project partner ([https://github.com/OmkarSavant](https://github.com/OmkarSavant)) and I also extended the algorithm to generate optimized trajectories for multiple agents without collision.

This project was for a course at the University of Pennsylvania, so the code cannot be publicly displayed due to academic integrity policies. Recruiters interested in the specifics of my work can contact me at jauckley@seas.upenn.edu.

Our 9-page report on the paper and the results of our extension is linked here ([https://drive.google.com/file/d/1wexU4hR-PBu9TNvZhhZmS1EmYTiT23oj/view?usp=sharing](https://drive.google.com/file/d/1wexU4hR-PBu9TNvZhhZmS1EmYTiT23oj/view?usp=sharing)), and I will give an overview of the algorithm in the following paragraph.

## The Algorithm

The Convex Feasible Set Algorithm is a fast algorithm for solving motion planning problems with convex objective functions and non-convex constraints. It transforms the non-convex problem into a sequence of convex problems and solves them iteratively. It does so by finding convex feasible sets within the non-convex domain and solving them as convex optimization problems until convergence.

This graphic from the paper visualizes this procedure quite well:
![alt text](https://github.com/jauckley/convex-feasible-set/blob/master/images/CFS_fig2_1.png)

## Implementation

In our implementation, we formulated the objective function as a quadratic function that penalizes the acceleration necessary to follow the given trajectory over a planning horizon. This resulted in smooth and short trajectories that completely avoided obstacles.

The resulting trajectory for a given scenario can be seen here, where the grey boxes are obstacles, the thin colored lines are solutions of previous iterations, and the converged solution is shown in black:
![alt text](https://github.com/jauckley/convex-feasible-set/blob/master/images/cfs_classic.png)

We compared the performance of our CFS implementation to the interior point method using Matlab's fmincon. We tested the two algorithms on two different planning scenarios- Scenario 1 being the setup shown above and Scenerio 2 being a simpler setup the one square obstacle in between the start and end point. The time taken to run the algorithms on differing number of time steps is shown in the following table:
![alt text](https://github.com/jauckley/convex-feasible-set/blob/master/images/time_comparison.png)

It can be seen from this table that the CFS algorithm performs much faster than the interior point method. Additionally, the trajectory created from running interior point method (even when it converged to a solution) was almost always visibly sub-optimal, in that its shape bends very far out of its way to avoid obstacles. It is clearly not suitable for the application of autonomous vehicles, while CFS generates trajectories that efficiently avoid obstacles.

## Multi Agent Extension

Our approach to multi agent planning is based on the fact that, on a given timestep, if a central planner knows the next *n* steps that each agent is planning to take, from each agent’s perspective, a convex hull can be built around the next *n* positions for every other agent in the planning space. This hull is then treated as another obstacle in the space,, and changes appropiately at each timestep in the planning horizon so that the constraints ensure that no two agents are in the same place at the same time. Looking ahead at the next *n* steps in the trajectory is like a built-in safety buffer so the trajectories don't cut it too close during optimization, where it will try to exploit the geometry of obstacles as much as possible.

The trajectory for each agent is initated as a straight line from start to goal, and the optimization problem is solved for each agent sequentially until every trajectory has converged. We found that this approach worked well still and yielded smooth trajectories. I recommend you view the animated results of two problems (a three agent and a six agent problem) through this link ([https://youtu.be/msUxC3RgsHE](https://youtu.be/msUxC3RgsHE)). The results of a three agent problem is shown here, with the converged trajectories being the thicker red, blue, and black lines:
![alt text](https://github.com/jauckley/convex-feasible-set/blob/master/images/cfs_ext_3ag.png)

