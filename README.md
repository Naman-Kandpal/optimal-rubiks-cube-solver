## Team Members
- Harshit Dangwal – SDL2 and Visualizations
- Kamal Joshi – Mathematics and Thistlethwaite's algorithm
- Naman Kandpal – Cube Representation and Operations

## References used:
- Scherphuis, Jaap. Thistlethwaite, Morwen. [Thistlethwaite's 52-move algorithm](https://www.jaapsch.net/puzzles/thistle.htm).
- [Computer Puzzling](https://www.jaapsch.net/puzzles/compcube.htm#thisal)
- [Implementing an Optimal Rubiks Cube solver - Benjamin Botto](https://medium.com/@benjamin.botto/implementing-an-optimal-rubiks-cube-solver-using-korf-s-algorithm-bf750b332cf9)
- [StackOverflow Discussion forum on Rubiks Cube](https://stackoverflow.com/questions/500221/how-would-you-represent-a-rubiks-cube-in-code)

### Inspiration

This project is fueled by our curiosity to deeply understand how this timeless puzzle is an Intersection between Mathematics and Computer Science.

#### About the Project
This project aims to explore the computational way of solving the rubiks cube, by using Thistlethwaite's Algorithm along with pattern Databases and Search Algorithms (A* and IDFDS)

We have successfully completed all major components (Cube representation, Class structure, Graphics, PreComputed Databases for better Performance)


## Thistlethwaite's algorithm
Program statistics:
Average solve length: 30 moves

Average solve time: 0.15ms

Thistlethwaite's algorithm solves the cube in less than 52 moves. The cube is split into 5 sub-groups: G0, G1, G2, G3 and G4.

G0 is any scrambled cube (4.33·10^19 states), and G4 is a solved cube (1 state). Instead of looking for a solution directly, the algorithm works by moving from one group to the other where each group is treated as a "simpler" puzzle for the computer to solve.
#### G0->G1:
- Positions: 4.33·10^19
- Unique states: 2^11 = 2048
- Legal moves: All moves

In G1, the orientation of all 12 edges is solved (good orientation means an edge can be solved without using a 90-degree Up or Down move)
G0->G1 only looks at edge orientations (0 for good, 1 for bad), which gives 2^12 unique states. Only half of these states is reachable because
the total edge orientation value is always even (each move affects 0 or 4 edges which gives an overall even value).

Therefore, there are  2^12 / 2 = 2^11 = 2048 states to store in a database.
#### G1->G2:
- Positions: 2.11·10^16
- Unique states: 8C4 * 3^7 = 1082565
- Legal moves: 90-degree Up/Down turns are excluded

In G2, all the M-slice edges are brought back to their home slice and the orientation of all the corners is solved (their Left or Right facelet is in the Left or Right face). There are 3 ways a corner can be oriented which gives 3^8 corner states. Similar to the edges in G0, only 3^8 / 3 of the corner states are actually reachable because the total orientation value of the corners is always divisible by 3. 8C4 for the edges is for 8 positions where the 4 M-slice edges can be (without regard to order)

Therefore, there are 8C4 * 3^8 / 3 = 8C4 * 3^7 = 1082565 states to store in a database.
#### G2->G3:
- Positions: 1.95·10^10

- Unique states: 8C4^2 * 2 * 6 = 29400
- Legal moves: 90-degre Up/Down and Front/Back turns are excluded

In G3, a cube is solvable using 180-degree moves only. This means that all edges are in their home slice and all corners are in their natural orbits. The M-slice is already solved in G1.
One solved slice / tetrad means that the second slice / tetrad is also solved, so the positions
of 4 edges and 4 corners are tracked (8C4 possible combinations for both)

Because of 90-degree L/R face turns, there are states in G2 that have odd parity which adds an
additional factor of 2.

Additionally, a factor of 3 is added due to [lone 3-cycles](https://puzzling.stackexchange.com/questions/5402/what-is-the-meaning-of-a-tetrad-twist-in-thistlethwaites-algorithm).

Therefore, there are 8C4^2 * 2 * 6 = 29400 states to store in the database.
#### G3->G4:
- Positions: 6.63·10^5
- Unique states: 4!^2 / 6 * 4!^3 / 2 = 96 * 4!^3 / 2 = 663552
- Legal moves: Only 180-degree moves are allowed (all 90-degree moves are excluded)

In G3->G4, finally the cube is solved. In this state the cube is seperated to 3 edge slices and 2 corner tetrads (each containing 4 pieces). Because only 180-degree moves are allowed the
pieces will never leave these orbits. Each slice/tetrad can be permuted in 4! ways which gives 4!^5, but there are some restrictions:

Corners: only 4!^2 / 6 of the corner states are reachable due to parity and disallowed lone 3-cycles.

Edges: only 4!^4 / 2 of the edge states are reachable due to parity.

Therefore, there are 4!^5 / 12 = 96 * 4!^3 / 2 = 663552 states to store in the database.



### System Requirements:
- g++ 13 or above
- SDL2 version 3.1 or above
- CMake

##### Building on Linux

- 1. `sudo apt install cmake libsdl2-dev g++`
- 2.
```
mkdir build
cd build
cmake ..
make
```

##### Building on Windows
- 1 Clone this Repository to your machine.
- 2. Open CMD in the root folder and do the following:
```
mkdir build
cd build
cmake .. -G "MinGW Makefiles"
mingw32-make
//make sure to include the path to your local SDL2 folder in engine.h file
```

