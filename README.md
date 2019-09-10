# Convex Feasible Set Algorithm
This is an implementation of the Convex Feasible Set Algorithm for Real Time Optimization in Motion Planning (Changliu et al. 2018) ([https://arxiv.org/abs/1709.00627](https://arxiv.org/abs/1709.00627)). My project partner ([https://github.com/OmkarSavant](https://github.com/OmkarSavant)) and I also extended the algorithm to generate optimized trajectories for multiple agents without collision.

Our 9-page report on the paper and the results of our extension is linked here ([https://drive.google.com/file/d/1wexU4hR-PBu9TNvZhhZmS1EmYTiT23oj/view?usp=sharing](https://drive.google.com/file/d/1wexU4hR-PBu9TNvZhhZmS1EmYTiT23oj/view?usp=sharing)), and I will give an overview of the algorithm in the following paragraph.

## The Algorithm

The Convex Feasible Set Algorithm is a fast algorithm for solving motion planning problems with convex objective functions and non-convex constraints. It transforms the non=convex problem into a series of convex problems and solves them iteratively. It does so by finding convex feasible sets within the non-convex domain and solving them as convex optimization problems until convergence.

This graphic from the paper visualizes this procedure quite well:
![alt text](https://github.com/jauckley/convex-feasible-set/blob/master/images/CFS_fig2_1.png)

In our implementation, we formulated the objective function as a quadratic function that penalizes the acceleration necessary to follow the given trajectory over a planning horizon. This resulted in smooth and short trajectories that completely avoided obstacles.

The resulting trajectory for a given scenario can be seen here, where the grey boxes are obstacles, the thin colored lines are solutions of previous iterations, and the converged solution is shown in black:
![alt text](https://github.com/jauckley/convex-feasible-set/blob/master/images/cfs_classic.png)

## Multi Agent Extension

Our approach to multi agent planning is based on the fact that, on a given timestep, if a central planner knows the next *n* steps that each agent is planning to take, from each agent’s perspective, a convex hull can be built around the next *n* positions for every other agent in the planning space. This hull is then treated as another obstacle in the space,, and changes appropiately at each timestep in the planning horizon so that the constraints ensure that no two agents are in the same place at the same time. Looking ahead at the next *n* steps in the trajectory is like a built-in safety buffer so the trajectories don't cut it too close during optimization, where it will try to exploit the geometry of obstacles as much as possible.

The trajectory for each agent is initated as a straight line from start to goal, and the optimization problem is solved for each agent sequentially until every trajectory has converged. We found that this approach worked well still and yielded smooth trajectories. I recommend you view the animated results of two problems (a three agent and a six agent problem) through this link ([https://youtu.be/msUxC3RgsHE](https://youtu.be/msUxC3RgsHE)). The results of the three agent problem is shown here, with the converged trajectories being the thicker red, blue, and black lines:
![alt text](https://github.com/jauckley/convex-feasible-set/blob/master/images/cfs_ext_3ag.png)

