# Programming API

## Entrypoint

```python
from typing import Never
from logging import Logger

from worldlib.resources.types import (
    SquareUnitAPI as UnitAPI,
    SquareGridDirectionEnum as GridDirectionEnum,
)


async def run(this: UnitAPI, logger: Logger) -> Never:
    # Your code here
    pass
```
- Entrypoint function `run` is called when the game starts.
- The function can be `async` or "sync", but it should never return.
- When function exists the game engine leaves the unit in the last state.

Other acceptable signatures:
```python
async def run(this: UnitAPI) -> Never: ...
```

```python
def run(this: UnitAPI, logger: Logger) -> Never: ...
```

```python
def run(this: UnitAPI) -> Never: ...
```


## Unit API:

```python
class UnitAPI(Protocol):
    @property
    def self(self) -> UnitAPI: ...
    @property
    def repr(self) -> dict: ...
    @property
    def logger(self) -> Logger: ...

    @property
    def obj_id(self) -> TObjectID: ...
    @property
    def location(self) -> TCellID: ...
    @property
    def direction(self) -> GridDirectionEnum: ...
    @property
    def step(self) -> TInternalStep: ...

    async def wait(self) -> TInternalStep: ...

    async def turn(self, direction: GridDirectionEnum) -> GridDirectionEnum | None: ...
    async def move(self) -> TCellID | None: ...
    async def shoot(self) -> TObjectID | None: ...
    async def scan(self) -> dict[TObjectID, TCellID] | None: ...
```

### Movement:
- TU type: `Moving`
- Max TU: `15`
- TU cost: `10`
- TU replenish: `1` per step
- API:
```python
async def move(self) -> TCellID | None: ...
```
- Description: Move the tank one cell forward in the direction it is facing. If the cell is occupied, the move will fail. If the tank is at the edge of the grid, the move will fail.
- _Returns_: the new cell ID if the move was successful, None otherwise.

### Turning:
- TU type: `Turning`
- Max TU: `5`
- TU cost: `5`
- TU replenish: `1` per step
- API:
```python
async def turn(self, direction: GridDirectionEnum) -> GridDirectionEnum | None: ...
```
- Description: Turn the tank to the specified direction.
- _Returns_: the new direction. If Unit is already facing the specified direction, the turn will fail and return None.

### Shooting:
- TU type: `Shooting`
- Max TU: `15`
- TU cost: `10`
- TU replenish: `1` per step
- API:
```python
async def shoot(self) -> TObjectID | None: ...
```
- Description: Shoot in the direction the tank is facing. The projectile is placed in the next cell in the direction the tank is facing.
- _Returns_: the ID of the projectile if the shot was successful, None otherwise.

### Scanning:
- TU type: `Scanning`
- Max TU: `5`
- TU cost: `5`
- TU replenish: `1` per step
- API:
```python
async def scan(self) -> dict[TObjectID, TCellID] | None: ...
```
- Description: Scan the cells in the quoter of the grid the tank is facing. Scan range is `3` cells. The scan will return a dictionary with the IDs of the targets found and their locations.  Target ID is a unique ID issued for each "persisting" target. Target persists between subsequent scans if it is detected in both scans, and its age is at most `10` game steps.
- _Returns_: a dictionary with the IDs of the targets found and their locations.
- Example: If the Tank is facing North, and in position (0,0), the scan will check cells (0,1), (1,0), (1,1), (0,2), (1,2), (2,0), (2,1), (2,2), (3,0), (3,1), (3,2), (0,3), (1,3), (2,3), (3,3).


### Supplementary API:
All supplementary calls are read-only, do not consume TUs and do not affect the order of actions execution. However calling them will add delay of the code execution.

#### Waiting:
- API:
```python
async def wait(self) -> TInternalStep: ...
```
- Description: Wait until the next game step from when its called. Returns Unit age in steps.
- Returns: the Unit step age. _Next_ - relative to when its called, or _current_ relative to when its returned.

#### Logging:
- API:
```python
@property
def logger(self) -> Logger: ...
```
- Description: Logger object to log messages. This messages appear in object event logs.

#### Object ID:
- API:
```python
@property
def obj_id(self) -> TObjectID: ...
```
- Description: Unique ID of the tank.

#### Location:
- API:
```python
@property
def location(self) -> TCellID: ...
```
- Description: Current cell ID of the tank.

#### Direction:
- API:
```python
@property
def direction(self) -> GridDirectionEnum: ...
```
- Description: Current direction the tank is facing.

#### Step:
- API:
```python
@property
def step(self) -> TInternalStep: ...
```
- Description: Current Unit age in steps.

#### Representation:
- API:
```python
@property
def repr(self) -> dict: ...
```
- Description: Representation of the tank. Contains all the information about the tank. It is a dictionary with the following keys:
  - `type`: str
  - `id`: object ID
  - `location`: current cell ID
  - `direction`: current direction
  - `step`: Game step
  - `intstep`: Unit age

#### Self:
- API:
```python
@property
def self(self) -> UnitAPI: ...
```
- Description: Self reference to the Unit object. This call is used to clone Unit control API.


## Projectile API:
