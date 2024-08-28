# Game Step Engine:
- The grid is square, 5x5 size, each cell is numbered from 0 to 24 in row-major order.
- The game is played in steps.
- Each game step lasts 0.1s.
- Each Unit can perform actions which cost Time Units (TUs).
- Each step TUs are partly replenished
- TU will accumulate to their finite maximum value if not used. Maximum value can be higher than the action cost.
- Each action uses its own type and amount of TUs. There are 4 types of actions: Moving, Turning, Shooting, Scanning.
- If Unit doesn't have enough TUs to perform an action, the action call will wait until the step when it has enough TUs.
- Unit can perform (or remain waiting for) multiple actions in a single step (multithreading is allowed).
- During most game steps there will be multiple actions performed by Units. There is no specific order of actions execution. The faster Unit make an action call, the earlier it will be executed, and the higher the chance of success.
- Game ends when there is one or zero Tanks left on the grid.

NOTEs:
- If the action is allowed but fails (e.g. shooting in the wrong direction), the action will return None.
- If certain action is not allowed (e.g. moving over the edge or turning in the direcation the unit is already facing), the engine will wait until has enough TU to perform the action to return an error.
- Because the state of the game changes during a single step Actions will return information according to the state of the game at the moment when the call is actually performed.


## Unit Spec:
- Turn: cost `5` TUs, max `5` TUs, replenish `1` TU per step.
- Move: cost `10` TUs, max `15` TUs, replenish `1` TU per step.
- Shoot: cost `10` TUs, max `15` TUs, replenish `1` TU per step.
- Scan: cost `5` TUs, max `5` TUs, replenish `1` TU per step.

## Projectile Spec:
- Move: cost `3` TUs, max `3` TUs, replenish `1` TU per step.
